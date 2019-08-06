Here's a good list of [known attacks](https://consensys.github.io/smart-contract-best-practices/known_attacks/)

Faulty assumptions
==================

Could also be titled: "Falsehoods programmers believe about Ethereum contracts"

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

The relevant feature is that in order to make state change, it first performs a send, and throws if that fails (due to lak of gas), rather than doing some more careful error handling. The default gas amount that gets passed along with `send** is 23,000. 

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

