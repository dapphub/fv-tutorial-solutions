Formally verified contracts are only guaranteed to be correct insofar as their specs are.
The [token.sol](https://github.com/dapphub/fv-tutorial/blob/master/2/dapp/src/token.sol) example is very seriously flawed, but still, it passes the given spec.

The problem stems from a hidden assumption in the spec - that `CALLER_ID` and the `to` variable are distinct. The bug appears when does a transfer to oneself. Let's see why. Consider a call to the transfer function where `msg.sender == to`:

```sol
  function transfer(address to, uint256 value) public {
    uint256 srcBal = sub(balances[msg.sender], value);
    uint256 dstBal = add(balances[to], value);
    balances[msg.sender] = srcBal;
    balances[to] = dstBal;
  }

```
Assuming the balance of sender is at least `value`, the last line will cause the senders balance increase with `value` every call.

To spot this faulty behaviour we need to add an additional case in our specs. We have added this condition with its intended behaviour to the [src/spec.md](src/spec.md) in this directory, but left the contract unchanged. An example of a correctly verified token can be found in the [klab repo](https://github.com/dapphub/klab/tree/master/examples/token).
