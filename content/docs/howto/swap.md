---
weight: 3
bookFlatSection: true
---

# Swap

Swap in Terraswap works as same as trade in other exchanges. Price is moved by supplies & demands in other centralized exchange. And we know its move by price bidding. As both of conventional stock markets and crypto markets vary its price at every millisecond. So realtime transaction is the most important factor in the market. Otherwise, if somebody want to implement an exchange based on blockchain, bidding-based pricing is impossible to implement because of the block time. All transactions are executed as same sequences if they are in one block in DEX (decentralized exchanges) while a centralized exchange has its sequence at millisecond order. Instead of realtime bidding system, DEX changes the prices by formula & smart contract.

Swap transaction moves the prices according to the algorithm. If you have any interest about that, you may read [this article]({{< relref "/docs/introduction/mechanism" >}}). In this section, we will concentrate to explain in technical way.

## Prerequisites

- Token & pair should be deployed
- You should know the addresses of token & pair.
- Increase your allowance. [Execute `IncreaseAllowance`]({{< relref "/docs/reference/token" >}})

## Just swap (as a typical user)

### (Native token, Contract-minted token) -> Contract-minted Token

By command line, all transactions can be executed in that way:

```bash
terracli tx wasm execute <contract-address> <handle-msg> <coins>
```

- `contract-address`: In swap transaction, this should be the pair addrss.
- `handle-msg`: It represents the method and parameters of this execution. Will explain below.
- `coins`: Fee of transaction

I have no worry you with filling `contract-address` and `coins`. Then, let's organize the `handle-msg`. If you want to know general rule of it, you may check [here]({{< relref "/docs/howto/query" >}}).

- Source asset: native token

```json
{
    "swap": {
        "offer_asset": {
            "info" : {
                "native_token": {
                    "denom": "uluna"
                }
            },
            "amount": "10"
        },
        "to": "<HumanAddr>"
    }
}
```

- Source asset: contract-minted token

```json
{
    "swap": {
        "offer_asset": {
            "info" : {
                "token": {
                    "contract_addr": "<HumanAddr>"
                }
            },
            "amount": "10"
        },
        "to": "<HumanAddr>"
    }
}
```

`swap.offer_asset` represents your source asset. Be sure that `swap.offer_asset.amount` is not same as your amount. It depends on the decimal of the token setting. In case of Luna, its decimal is 9. Then, if `swap.offer_asset.amount` reads `10`, the actual amount is `10 x 10^-9`. So, you should multiply with mathcing value, `10^(decimal)`.

`swap.to` is the address of token address of detination. You don't have to put how much is. Again, it calculates by algorithm.

After filling it out, you may change it as a inline string (not necessary if you can make it with multiline):

```bash
'{"swap":{"offer_asset": {"info" : {"token": {"contract_addr": "<HumanAddr>"}},"amount": "10"},"to": "<HumanAddr>",}}'
```

This is your `handle-msg`. Fill your CLI and swap it!

### Contract-minted token -> Native Token

It needs different `handle-msg`. It works as a same way logically, but it uses different method because of the difference of token system and its implementation. Message is like below:

```json
{
    "send": {
        "contract": "<HumanAddr>",
        "amount": "10",
        "msg": Binary({
            "swap": {
                "to": Option<HumanAddr>
            }
        })
    }
}
```

The method is a little bit tricky. Please do not loose your concentration!

```bash
terracli tx wasm execute <contract-address> <handle-msg> <coins>
```

In CLI, `contract-address` **should be the token address!**

And, in message:
- `send.contract`: **pair address should be on it!!**
- `send.amount`: 
