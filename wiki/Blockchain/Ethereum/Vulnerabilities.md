Here's a good list of [known attacks](https://consensys.github.io/smart-contract-best-practices/known_attacks/)

Front-running attacks
=====================

Front-running takes advantage of the delay between a transaction being sent and it being mined.
The attacker notices a transaction that they can benefit from and sends a transaction of their own with high gas-price to ensure they get mined quicker.

## Market manipulation / "insider trading"

Relevant to a decentralized exchange, or any other contract in which assets are sold and bought at some market price.
If a big order comes in, say a sell order on asset ASS, an attacker can front-run it and sell their ASS before the price drops.
It's a format of shorting, but an arbitrage, so the gains are almost guaranteed.
For quadratic shares contracts, this can be very relevant: if someone wants to buy some shares, a fron-runner can long it, buying one share, then waiting for the larger order to go through, then selling their share.
Note that, since anyone can front-run, this could cause some pretty wild fluctuations as price drops due to many sell-orders coming in, essentially blocking the big sell order, until prices readjust.

## ERC20 `approve` and `transferFrom` attack.

Well known: https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit

This attack works for *any* ERC20-compliant contract: it's an attack on the API, not an implementation.
If Alice calls `approve` for `N` tokens to Bob, Bob may not call `transferFrom` right away, but may wait for a bit to transfer the tokens on Alice's behalf.
If Alice later want to adjust the amount and calls `approve` for `M` tokens to Bob, she might expect that `M` is the new value Bob can withdraw from her.
But now Bob can front-run: when he sees Alice's transaction, he immediately calls `transferFrom` and takes out the `N` tokens from Alice.
Bob puts a high gas-value to ensure he beats Alice's transaction.
When Alice's transaction arrives, Bob can now withdraw `M` of her tokens, and he has gotten an allowance of `N + M` tokens.

### Mitigation

The simplest mitigation is to send 2 transactions: one setting the `approve`d amount to `0` (so Bob can only get `N+0` tokens by front-running, which he already could have gotten).
When that approval is mined and buried enough, she can send a second transaction, approving `M` tokens, or `M-N` if Bob did a front-run and she wanted to adjust the apprved amount up.

If Alice wants to see if Bob is disonest, she can do the following:
Alice can mitigate this by revoking her initial transaction if she sees Bob front-run.
She simply sends another front-running transaction of her own (say, transferring some Ether to herself), which only needs to have higher gas-price then her `approve` transaction.
Her front-running transaction will inrement her account nonce, making the `approve` transaction invalid.
Game-theoretically, if both Alice and Bob are prepared for this attack, Bob can thus only gain `N` tokens, which he was already approved for, but it will cost them both more (in gas-price) than if Bob had just withdrawn in the first place.


Faulty assumptions
==================

Could also be titled: "Falsehoods programmers believe about Ethereum contracts"

False: If you send a transaction, you can assure that the relevant state (e.g., your token balance) won't change before it gets mined
-------------------------------------------------------------------------------------------------------------------------------------

See the section on front-running.

### Special case: If you ever do an `approve` on an ERC20, consider that amount already gone -- you can not adjust amount downwards later, only up

See above on front-running in ERC20.

False: Every contract is created with 0 ether balance
-----------------------------------------------------

In fact, it is possible to send ether to an address *before* a contract is created at that address. And it's reasonable to expect someone to do that (see next item).

False: It is not known beforehand what address a contract will have (it's a hash of some data that isn't known until creation time, right?)
-------------------------------------------------------------------------------------------------------------------------------------------

A contract address is indeed (the 160 rightmost bits) of a Keccak hash, but the inputs to the hashing function are only the creator account address and it's nonce. So you can easily know beforehand which address the next contract created by a user or contract will have.

See https://jellopaper.org/evm/#account-creation-deletion or section 7 of [the Yellow Paper](https://ethereum.github.io/yellowpaper/paper.pdf)

Also see this: [Close call on Lockdrop samrt contract with $290 million](https://medium.com/@nmcl/gridlock-a-smart-contract-bug-73b8310608a9). Programmer using asserts to check invariants that seem sensible, but in fact are not, could cause an entire contract to lock down.

False: Contracts can reason about exactly how much ether they contain
---------------------------------------------------------------------

It is not only possible to send ether to a contract before it is created: it is also possible to send ether to a contract without triggering the fallback function.
There are only two ways to do this:
1. By using `selfdestruct` (easy, anyone can do it)
2. By mining to the contract. (high cost, unreliable)

False: It is always possible to make a send succeed (Griefing Wallets)
----------------------------------------------------------------------

**Cause:** Using `send**, assuming that it will always succeed.
**Solution:** Consider that `send` can fail if the recipient is a contract with an expensive default function. Write your error handling accordingly.

[Described here.](https://vessenes.com/ethereum-griefing-wallets-send-w-throw-considered-harmful/)

Imagine some other contract has code like this.

```sol
address current_staker = 0x0;
uint current_stake = 0 wei;

function take_over() {
  if (msg.value > current_stake) {
    bool success = current_staker.send(current_stake); // SEND.
    if (!success) throw;                               // THROW.
    current_staker = msg.sender;
    current_stake = msg.value;
  }
}
```

The relevant feature is that in order to make state change, it first performs a send, and throws if that fails (due to lack of gas), rather than doing some more careful error handling.
The default gas amount that gets passed along with `send` is 23,000.

**A contract calling send _must_ pass along enough gas to execute the default function, or the send will be unsuccessful.**

The problem: we can, malicously or unwittingly, cause the contract to lock up and never complete the `take_over` function, by making sure that the `send` always fails.

Imagine a contract with a very gas-expensive default/fallback function.

```sol
function() payable {
  // Big loop, lots of storage.
  // Super expensive gas-wise.
}
```

If we have the malicious contract make a call to the vulnerable contract, e.g., in its constructor, making our malicous contract the new owner, then the vulnerable contract will never again be able to execute `take_over` to the end. The default function in the malicous contract requires more than 23,000 gas, so the line `SEND` will give `false` and the line `THROW` will revert execution to what it was before.

The vulnerable contract is locked down forever.

False: A program will execute once fully (updating variables) before it is called again, even if it contains a send (Reentrancy attack)
---------------------------------------------------------------------------------------------------------------------------------------

The Uniswap made this error, assuming they could calculate an token2eth price, then send ether, and calcualte a new price.
https://blog.openzeppelin.com/exploiting-uniswap-from-reentrancy-to-actual-profit/

Explore vulnerabilities
=======================

At <https://contract-library.com/> you can find a scan of all smart contracts on Ethereum that have warnings for certain vulnerabilities.

