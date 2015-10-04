---
layout: post
title: Bitcoin Basics
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [Iteration 1](#iter1)
*  [Iteration 2](#iter2)
*  [Iteration 3](#iter3)
*  [Iteration 4](#iter4)
    -  [Proof of Work](#pow)
    -  [Mining](#mining)
*  [Bitcoin](#bitcoin)
    -  [Bitcoin Exchanges](#bitcoinexchange)


##<a id="summary">Summary</a>

Bitcoin is an online peer-to-peer currency.  Alice wants to give another person, Bob, an infocoin.  Let's walkthrough how we could implement this/what the Bitcoin Protocol is.

##<a id="iter1">Iteration 1</a>

Alice uses a string of bits as a primitive form of money.  She writes "I, Alice, am giving Bob one infocoin".  She then signs with a private cryptographic key and announces this to the world.

*  Pros: Anyone in the world (including Bob) can use Alice's public key to verify Alice was the one who signed the message; i.e. we can verify that Alice's intent to give Bob an infocoin
*  Issues: Duplication of coins

##<a id="iter2">Iteration 2</a>

Sign with a serial number to fix duplication.  Alice writes "I, Alice, am giving Bob an infocoin with serial number 1111123"

*  Needs a trusted source to produce and track these serial numbers, i.e. a bank
*  Bob accepts the infocoin, but also needs to verify the serial number belongs to Alice and she hasn't spent it yet.  If this is correct, the transfer is complete.

##<a id="iter3">Iteration 3</a>

Eliminate the bank, make everyone the bank.  Everyone keeps a __block chain__ that is a public ledger of transactions.

*  Cons: Alice can send two transactions, one to Bob and one to Charlie.  If they happen to verify at the same time (before the block chain is updated), you can double send.
*  Possible Solution: Bob shouldn't verify the transaction alone, it should broadcast to the entire network of users to ask them to help determine if the transaction is legitimate.
*  Issues with the possible solution are:
    -  What is 'once enough people verify'; what is enough?  It can't be everyone since we don't know who is on the entire network.
    -  Alice can have puppet identities that swamp the network and verify both transactions

##<a id="iter4">Iteration 4</a>

This last iteration is closer to what Bitcoin is.  It's made up of:

1.  __proof-of-work__ is a hard computational puzzle that artifically makes it computationally costly for network users to validate transactions
2.  users __mine__ (get rewards) users for trying to help validate transactions

This makes it so that a cheater would need enormous computational resources to cheat (thus impractical).  We no longer rely on the number of people on the network, but focuses on the total computational power they bring.

Users need to solve the proof-of-work puzzle or else the network won't accept it as a valid transaction.

####<a id="pow">Proof of Work</a>

Bitcoin uses a __SHA-256__ hash function.  An example __proof-of-work__ may be adding say a __nonce__ (e.g. 0) to the end of the actual text (e.g. "hello world"):

    h("hello world0") = fjdskal43242jkfdldajkll34jlksfdl

We would keep incrementing the nonce (e.g. 1, 2, 3) until we find a result that has say 4 starting zeroes

    h("hello world4323") = 0000fjlaj24jlkdssfajl4j3jllfj

The actual Bitcoin network is a little more sophisticated and can adjust how difficult the __target__ (e.g. the number of zeroes needed) is so that it averages about 10 minutes to validate.  We can also require multiple puzzles to be solved.

####<a id="mining">Mining</a>

The validation process is called __mining__.  For every block of transactions validated, the user gets a bitcoin reward.  A 10^-8 bitcoin is the minimial unit of Bitcoin and is called a __satoshi__.  As another reward besides mining, there can also be a minimal __transaction fee__ that goes to the people helping validate.

####<a id="order">Order</a>

We want an __order__ to see which transactions have occurred first.  Without an order, we won't know who owns what infocoins.  New blocks always include a pointer to the last block validated in the chain (in addition to the list of transactions in the block).

Sometimes there's a __fork__ in the block chain, which means two miners validated and broadcasted to the entire network at the same time.  To resolve this, new miners are assigned the longest fork.  A valid fork needs at __least 5 blocks__ following.

####<a id="issues">Issues</a>

Alice can still __double spend__ by paying herself and Charlie in an inprobable scenario; say Charlie accepts the infocoin (which happens after the transaction has been confirmed 6 times in the longest chain), and somehow Alice has so much computing power to overpower the network so that her fork is the longest.  Again this is unlikely, but possible.

##<a id="bitcoin">Bitcoin</a>

To use bitcoin, you install a __wallet__ program (e.g. Multbit).  This shows you how many bitcoins you have.  If you're a merchant that allows people to pay using Bitcoin, you tell your wallet program to generate a __Bitcoin address__; this creates a public/private key pair where the public key is a result of hashing your public key.  E.g. Address: 17jfdkaljklfdasjlfsajl

1.  You send your Bitcoin address to the person who wants to buy from you.
2.  The person who wants to pay you generates a __transaction__
    -  This includes an identifier for the transaction
    -  Version number of Bitcoin
    -  Number of inputs and outputs
    -  Any lock_time (when can the transaction be finalized); usually 0 (i.e. immediately)
    -  Input of the transaction (e.g. signature of person sending the money, corresponding public key)
    -  Note: there is nothing explicitly specifying how many bitcoins should be spent in this transaction (like trying to buy bread with 20 dollars and can't break that down)
    -  Output of the transaction (i.e. how many bitcoins are we spending?)

Instead of creating a serial number from a central area, users self-generate a transaction id by hashing the transaction.

####<a id="bitcoinexchange">Bitcoin Exchange</a>

A Bitcoin exchange is like a bank where you deposit money and can buy or sell bitcoins when it reaches a certain amount.

