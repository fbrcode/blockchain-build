# Blockchain Building

Build your own blockchain to understand it 😉

Reference: <https://hackernoon.com/learn-blockchains-by-building-one-117428612f46>

## Requirements

1. Need to have python3 installed follow these instructions and make sure your `$PATH` environment variable is [setup correctly](https://ehmatthes.github.io/pcc/chapter_01/osx_setup.html)

2. Make sure PIP is [installed](https://pip.pypa.io/en/stable/installation/)

## Using Python Virtual Environment

Use python virtual environment (venv) for local development. Check existing global installation with `virtualenv --version`. If not installed, run the following `pip3 install virtualenv`.

### Check pip location

```sh
which pip3
pip3 list
python3 --version
```

### Apply venv to the project

```sh
cd ~/blockchain-build
python3 -m venv ./venv
source ./venv/bin/activate
```

### Deactivate venv

While in venv mode, type `deactivate` to exit from the virtual environment.

### Generate project dependencies

In order to generate the libraries dependency requirements, run:

```sh
pip3 freeze > requirements.txt
```

### Load project dependencies

To reapply the dependencies to the project, run:

```sh
pip3 install -r requirement.txt
```

## Blockchain project interaction

So, we have our `blockchain.py` with the full blockchain application and this is how we interact with it:

### Start up the server/API

```sh
python3 blockchain.py
```

### Mine blocks

Perform mining action by the node:

```sh
curl http://localhost:5000/mine
```

### Add a new transaction with cURL

Adding the transaction to a block:

```sh
curl -X POST -H "Content-Type: application/json" -d '{
 "sender": "d4ee26eee15148ee92c6cd394edd974e",
 "recipient": "someone-other-address",
 "amount": 5
}' "http://localhost:5000/transactions/new"
```

### Chain status

This is how we check the chain status:

```sh
curl http://localhost:5000/chain
```

## Blockchain project interaction with consensus

Now, we have our `blockchain-consensus.py` with the full blockchain application and this is how we interact with it:

### Start up both servers/APIs

Start two servers on different ports:

```sh
python3 blockchain-consensus.py -p 5000
python3 blockchain-consensus.py -p 5001
```

### Register the 2nd server/node

Call the register node endpoint on the 1st node, passing the 2nd node as input:

```sh
curl -X POST -H "Content-Type: application/json" -d '{
  "nodes": ["http://localhost:5001"]
}' "http://localhost:5000/nodes/register"
```

### Mine to add blocks

In the second chain mine a couple of blocks to be the longest chain by calling the following endpoint a couple of times:

```sh
curl http://localhost:5001/mine
```

### Sync the longest chain status with the 1st node

To sync the longest chain in the 1st node, run:

```sh
curl http://localhost:5000/nodes/resolve
```

The chain will be replaced in the first node since it's the longest one.

A similar response to this will be presented:

```json
{
  "message": "Our chain was replaced",
  "new_chain": [
    {
      "index": 1,
      "previous_hash": 1,
      "proof": 100,
      "timestamp": 1660920393.601711,
      "transactions": []
    },
    {
      "index": 2,
      "previous_hash": "6e17543167d8f11c9f855d5e6dda6f8a753e69eb1950c777dca0b8eb2832c5b4",
      "proof": 35293,
      "timestamp": 1660921190.041497,
      "transactions": [
        {
          "amount": 1,
          "recipient": "40b14f1432054516baa7d1013012d3ff",
          "sender": "0"
        }
      ]
    },
    {
      "index": 3,
      "previous_hash": "a01ccf2b9b7f4c46835432f006de673aac54eb4c9edab3248e05fc13fd591c21",
      "proof": 35089,
      "timestamp": 1660921191.180373,
      "transactions": [
        {
          "amount": 1,
          "recipient": "40b14f1432054516baa7d1013012d3ff",
          "sender": "0"
        }
      ]
    }
  ]
}
```
