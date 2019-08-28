This is mostly a list of audit tools.

More tools can be found here:
<https://github.com/ethereum/wiki/wiki/Ethereum-Virtual-Machine-(EVM)-Awesome-List#live-on-main-network>

Solidity
========

### Mythril

https://github.com/ConsenSys/mythril

Super quick automatic tool, just do

```
myth analyze -t <num transactions> <solidity file>
```

or

```
myth analyze -t <num transactions> -a <contract address>
```

Automatic detection of security vulnerabilities in EVM contracts using symbolic execution

### Manticore

Best current examples of usage can be found here: https://github.com/trailofbits/manticore/tree/master/examples/evm
Symbolic execution of EVM contracts (and solidity).

### Echidna

https://github.com/crytic/echidna

Fuzz testing

### Visual auditor

Use VS Code and this plugin to get a sense of program and data flow inside a contract.

https://marketplace.visualstudio.com/items?itemName=tintinweb.solidity-visual-auditor


### Solidity decompiler
JEB
https://www.pnfsoftware.com/blog/ethereum-smart-contract-decompiler/
