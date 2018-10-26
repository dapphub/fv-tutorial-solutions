The [DemurrageToken.sol](https://github.com/dapphub/fv-tutorial/blob/master/1/dapp/src/DemurrageToken.sol) contract has one intentional bug in its multiplication function:
```sol
  function mul(uint256 x, uint256 y) internal pure returns (uint256 z) {
    if (x == 0) {
      return 0;
    }
    z = x * y;
    require(z / y == x);
  }
```
The bug here is a simple switch of arguments. Instead of checking whether `y == 0` before we divide by `y` we check `x`, meaning that whenever `y == 0` and `x =/= 0`, we will run into a division by zero. In this case, Solidity will `REVERT` automatically.

There is also a couple of problems with the given spec. First of all, we need to make sure that the following values do not overflow:
```
    99 * FromBal
    (99 * FromBal) / 100 - Value
    ToBal + Value
```

Furthermore, since klab decomposes each `act` into a success case and a failure case based on the conditions in the `iff` header but the `transfer` function of `Demurrage` has two different success behaviours (depending on the condition `TIME - CallerLastTouched > #week * 10`), we need to write two different specs.

This directory contains an example solutions, with passing specs.
