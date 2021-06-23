# API Protocol

### /get-metaparameters
#### [GET] get a JSON of current IceCereum Network values

```
{
    "difficulty"          : (int) difficulty
    "percent_tx_rewarded" : (float) percentage of transactions
                            rewarded
    "mining_fees"         : (float) mining fees per transaction
    "reward_for_tx"       : (float) reward for rewarded transaction
    "ntx_in_txpool"       : (int) number of transactions in TXPOOL
}
```


### /get-txpool
#### [GET] get a JSON of transactions in the TXPOOL

```
{
    "tx_index" (str) :
    {
        "sendr_addr"  : (str) sender address
        "recvr_addr"  : (str) receiver address
        "value"       : (float) amount transacted
        "mining_fee"  : (float) mining fees
        "final_value" : (float) value - mining_fee
        "skscript"    : (str) signature of transaction
        "timestamp"   : (str) Python3 datetime timestamp
    },
    "tx_index" (str) : ...,
    "tx_index" (str) : ...,
}

```

### /get-complete-chain
#### [GET] a JSON of JSON objects containing the entire blockchain
#### WARNING: MAY BE REALLY LARGE

```
{
    "block_index" (str) :
        {
            "block_index"         : (int) block index
            "mined_block_hash"    : (str) the (mined) hash of that block
                                          *NOT* equal to block.block_hash
            "previous_block_hash" : (str) hash of the prev block
            "nonce"               : (str) nonce used to make hash meet difficulty
            "difficulty"          : (int) difficulty at the time the block was 
                                          mined
            "transactions" :
                {
                    "transaction_index" (str) :
                        {
                            "sendr_addr"  : (str) sender address
                            "recvr_addr"  : (str) receiver address
                            "value"       : (float) amount transacted
                            "mining_fee"  : (float) mining fee for the tx
                            "final_value" : (float) "value" - "mining_fee"
                            "skscript"    : (str) signed message from
                                                  sender
                            "timestamp"   : (str) timestamp of tx
                        },
                    "transaction_index" (str) : ...,
                    "transaction_index" (str) : ...,
                }
            "timestamp" : (str) timestamp the block was joined into the
                                chain
        },
    "block_index" : ...,
    "block_index" : ...,
}
```

### /get-balance
#### [GET] balance of a particular address

request (JSON):
```
{
    "sendr_addr" : (str) sender address
}
```

response (JSON):
```
{
    "message" : (str) Message
    "balance" : (float) Balance
    "exists"  : (bool) Account Existence
    "success" : (bool) Success
}
```
Message can be:

- sendr_addr NOT FOUND IN REQUEST
- SENDER ADDRESS DOES NOT EXIST
- SENDER ADDRESS FOUND

### /get-transactions
#### [GET] all the transactions of a particular address

request (JSON)
```
{
    "address" : (str) address
}
```

response (JSON)
```
{
    "message"      : (str) Message
    "exists"       : (bool) Account Existence
    "success"      : (bool) Success
    "transactions" :
        {
            "1" (str) :
                {
                    "type"        : (str) one of {"send", "receive"}
                    "in_txpool"   : (bool) one of {True, False}
                    "sendr_addr"  : (str) sender address
                    "recvr_addr"  : (str) receiver address
                    "value"       : (float) amount transacted
                    "mining_fee"  : (float) mining fee
                    "final_value" : (float) "value" - "mining_fee"
                    "skscript"    : (str) signed message
                    "timestamp"   : (str) Python3 datetime timestamp
                },
            "2" (str) : ...,
            "3" (str) : ...,
        }

}
```

Message can be:

- address NOT FOUND IN REQUEST
- ADDRESS DOES NOT EXIST
- ADDRESS FOUND

### /transfer-funds
#### [GET] and [POST] some funds

Protocol:

1. Client initiates via GET 

request (JSON):
```
{
    "sendr_addr" : (str) sender_address
}
```

2. Server sends response

response (JSON)
```
{
    "message"        : (str) message
    "mining_fees"    : (float) current mining fees of the network
    "one_time_nonce" : (str) one time nonce used for the session,
    "success"        : (bool) success of the request
}
```

Message can be:

- sendr_addr NOT FOUND IN REQUEST
- SUCCESS

3. Client replies with POST

request (JSON)
```
{
    "sendr_addr"     : (str) sender address
    "recvr_addr"     : (str) receiver address
    "value"          : (float) amount being transferred
    "one_time_nonce" : (str) one time nonce generated during GET
    "skscript"       : (str) signed message to authenticate without nonce
    "skscript_nonce" : (str) signed message to authenticate with nonce
}
```

skscript must be `{sendr_addr}{recvr_addr}{value}` (including the { })

skscript_nonce must be `{sendr_addr}{recvr_addr}{value}{nonce}` (including the
{ })

Response (JSON) can be:

- MISSING SESSION IDENTIFIER: { }
  - The session is missing the session identifiers (cookies)
- MISSING { } FIELDS
  - The POST request is missing the fields mentioned
- SIGNATURE VALIDTION ERROR - UNEXPECTED RECOVERABLE SIGNATURE LENGTH
  - skscript_nonce was not a proper signature
- SIGNATURE VALIDTION ERROR - SIGNATURES DO NOT MATCH
  - This should never happen cryptographically, but someone else is trying to
  spend on the requested account
- INSUFFICIENT FUNDS
- SENDER ADDRESS DOES NOT EXIST
- RECVR ADDRESS NOT VALID
- TRANSACTION SUCCESSFULLY ADDED TO TXPOOL

