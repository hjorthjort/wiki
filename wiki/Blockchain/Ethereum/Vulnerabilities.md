Explore vulnerabilities
=======================

At <https://contract-library.com/> you can find a scan of all smart contracts on Ethereum that have warnings for certain vulnerabilities.

Griefing Wallets
================

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

