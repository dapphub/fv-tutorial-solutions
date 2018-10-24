The transfer function of DemurrageToken has varying behaviour based on the 
time passed since last extraction. Since the klab `act` format is fairly 
opinionated in that it requires acts to only have one "success" case 
behaviour, we need to specify the two cases as two different acts.

```
behaviour transfer-with-demurrage of DemurrageToken
interface transfer(address To, uint Value)

types

    FromBal : uint256
    ToBal   : uint256
    CallerLastTouched : uint256

storage

    #hashedLocation("Solidity", 1, CALLER_ID) |-> CallerLastTouched => TIME
    #hashedLocation("Solidity", 0, CALLER_ID) |-> FromBal => (99 * FromBal) / 100 - Value
    #hashedLocation("Solidity", 0, To)        |-> ToBal   => ToBal + Value


iff in range uint256
    99 * FromBal
    (99 * FromBal) / 100 - Value
    ToBal + Value

if
    VGas >= 100000
    TIME - CallerLastTouched > #week * 10
```
```
behaviour transfer-without-demurrage of DemurrageToken
interface transfer(address To, uint Value)

types

    FromBal : uint256
    ToBal   : uint256
    CallerLastTouched : uint256

storage

    #hashedLocation("Solidity", 1, CALLER_ID) |-> CallerLastTouched
    #hashedLocation("Solidity", 0, CALLER_ID) |-> FromBal => FromBal - Value
    #hashedLocation("Solidity", 0, To)        |-> ToBal   => ToBal + Value


iff in range uint256
    FromBal - Value
    ToBal + Value
    TIME - CallerLastTouched

if
    VGas >= 100000
    TIME - CallerLastTouched <= #week * 10
```
