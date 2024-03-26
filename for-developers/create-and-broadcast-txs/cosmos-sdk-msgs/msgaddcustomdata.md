# MsgAddCustomData

MsgAddCustomData is a part of the x/anchor module which allows you to anchor any custom data to the blockchain. The blcokchain will store with an incrementing location ID along with the timestamp. This can be used to prove knowledge of something at a specific time by anchoring it to the chain.

```protobuf
message MsgAddCustomData {
  string creator = 1;
  string data    = 2;
}
```
