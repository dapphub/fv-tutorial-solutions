We split the transfer function up into different cases, based on whether `CALLER_ID == to`:
```act
behaviour transfer-different of Token
interface transfer(address to, uint256 value)

types
   SrcBal : uint256
   DstBal : uint256

storage

    #hashedLocation("Solidity", 0, CALLER_ID) |-> SrcBal => SrcBal - value
    #hashedLocation("Solidity", 0, to)        |-> DstBal => DstBal + value

iff in range uint256
    
    SrcBal - value
    DstBal + value

if
    VGas >= 100000
    CALLER_ID =/= to

```
```act
behaviour transfer-same of Token
interface transfer(address to, uint256 value)

types
   SrcBal : uint256
   DstBal : uint256

storage

    #hashedLocation("Solidity", 0, CALLER_ID) |-> SrcBal

if
    VGas >= 100000
    CALLER_ID == to

```
