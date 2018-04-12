# Brickblock Technical Whitepaper
by Marius Hanne, Jakob Drzazga, Adrian Kizlauskas, Philip Paetz, Martin Mischke

### Abstract
This document describes a smart contract platform built on top of the globally distributed
computing network known as [Ethereum](https://ethereum.org/)z.
The suggested Proof-of-Asset (PoA) scheme will enable users to seamlessly trade tokens,
which represent different types of Foreign-Asset on all [ERC20](https://theethereum.wiki/w/index.php/ERC20_Token_Standard) compatible markets.

The basic idea is to create a number of PoA contracts, each representing a different foreign asset.
By linking the token contract to a digital trust fund DTF, there will be a near 1:1 coupling between
the value of the token and the foreign asset.

Users can purchase PoA-Token in exchange for Native-Currency, trade them,
or hold them and receive a share of any dividends that the asset pays out.

### Table of Contents
* [1. Overview](#1-overview)
* [2. PoA Manager](#2-poa-manager)
  + [2.1. Brickblock Token (BBK)](#21-brickblock-token-bbk)
  + [2.2. Access Token (ACT)](#22-access-token-act)
  + [2.3. Digital Trust Fund (DTF)](#23-digital-trust-fund-dtf)
  + [2.4. Fees](#24-fees)
  + [2.5. Contract Discovery and Upgrades](#25-contract-discovery-and-upgrades)
  + [2.6. Broker registration](#26-broker-registration)
* [3. Proof-of-Asset](#3-proof-of-asset)
  + [3.1. Creation](#31-creation)
  + [3.2. Activation](#32-activation)
  + [3.3. Trading](#33-trading)
  + [3.4. Dividend Payout](#34-dividend-payout)
  + [3.5. Redemption](#35-redemption)
  + [3.6. Extension](#36-extension)
  + [3.7. Fraud Proofs](#37-fraud-proofs)
* [4. Real-World-Asset Funds](#4-real-world-asset-funds)
  + [4.1. Funding](#41-funding)
  + [4.2. Failed](#42-failed)
  + [4.3. Pending](#43-pending)
  + [4.4. Activation](#44-activation)
  + [4.5. Dividend Payout](#45-dividend-payout)
  + [4.6. Redemption](#46-redemption)
  + [4.7. Extension](#47-extension)
* [5. Crypto Funds](#5-crypto-funds)
  + [5.1. Passive (CTF)](#51-passive-ctf)
  + [5.2. Active (CMF)](#52-active-cmf)
  + [5.3. Autonomous (ACF)](#53-autonomous-acf)
* [6. Compatibility](#6-compatibility)
* [Glossary](#glossary)


## 1. Overview
During a contribution period, Brickblock tokens (BBK) will be distributed among participating contributors.
Brickblock tokens can be traded on any market, or locked up in order to generate so called access tokens (ACT).
Access tokens are minted and distributed to lockedBBK holders whenever a fee is paid in the Brickblock ecosystem; they are burnt in order to claim a proportional amount of ether.
Proof-of-Asset tokens represent a certain foreign asset available to trade on the Brickblock platform.
The assets backing those tokens are held by a publicly auditable digital trust fund.
All these tokens implement the ERC20 specification and are seamlessly tradable on compatible third-party markets.

<img src="img/BrickblockEcosystem.png" width="900" />

*Figure 1: The different types of tokens, and how they interact*


## 2. PoaManager
Proof-of-Asset tokens will be created by a smart contract that runs on the Ethereum
blockchain, which also handles broker registration and manages individual PoA
contracts. This contract is named PoaManager.

### 2.1. Brickblock Token (BBK)
The PoaManager contract itself implements an ERC20-compatible token,
which will be distributed to the contributors of our fundraiser.

In addition to being tradable, it can be locked in order to generate
access tokens when a fee is paid.

### 2.2. Access Token (ACT)

Access tokens are minted whenever a fee is paid in the Brickblock ecosystem. Only BBK holders who have locked their BBK into the access token contract will receive ACT. Access tokens are distributed based on the holder's amount of locked BBK relative to the total amount of locked BBK.

Fees must be paid in order for certain PoA contract functions. These functions include such things as PoA activation, and payouts. Every fee is paid to a Fee Manager contract which in turn mints access tokens in proportion to the fee that was paid. These newly minted access tokens are distributed to locked BBK holders.

Events are emitted for every payout, notifying locked BBK holders that a payout has been made. Users can also check their current balance using the standard balanceOf function (ERC20).

When BBK holders unlock their BBK from the access token contract, they will no longer receive ACT from fee payments. However, their previous ACT balance remains. Their ACT balance also remains even if they transfer their BBK.

### 2.3. Digital Trust Fund (DTF)
Brickblock will set up a digital trust fund that holds the assets that
are backing the PoA tokens in an investment account with a custodian.

The custodian will both notarise any activities on the DTF’s account and
publish proof allowing everyone to verify that all liabilities are
accounted for.

### 2.4. Fees
Over its lifetime, a PoA contract requires various parties to pay two
different types of fees: an activation fee and a payout fee.

An activation fee needs to be paid by the broker to activate a PoA contract which has passed the funding stage and has moved on to the pending stage.

A payout fee is deducted every time a payout is made, by the custodian, to PoA token holders.

The paid ACT will be burned and thus removed from circulation.
Brickblock does not keep or trade them after they have been used.

Fees are claimed by ACT holders. Access token holders can claim ether fees by claiming on the fee manager contract. Access tokens are burnt when claiming fees.

### 2.5. Contract Discovery and Upgrades
To enable the upgrading of features, all smart contracts will be
accessed through a proxy contract with a fixed address. This contract will be called Brickblock Contract Registry.

Individual contracts can also be suspended, as an emergency measure, in
the case of a bug being discovered.

### 2.6. Broker registration
Brokers must be approved by the Brickblock team and undergo strict
due-diligence procedures before being allowed to trade on the platform.
The PoA Manager contract holds a list of all currently active brokers and
allows the Brickblock administration to add and remove them.

## 3. Proof-of-Asset
<img src="img/PoaEcosystem.png" width="700" />

*Figure 2: The Proof-of-Asset Scheme*

The Proof-of-Asset mechanism works by establishing a “triangle of trust”
between a user, the DTF and a broker, mediated by a mutually trusted
smart contract.

A User pays native currency to the contract. The broker can claim this
amount once he or she has sent the required assets to the DTF.

The DTF proves receipt of the assets to the smart contract, which
releases the native currency to the broker and the newly created PoA
tokens to the User.

The user can trust the contract to only activate when a valid
proof-of-assets is received, or refund the initial amount.

The broker can similarly trust the contract to release the funds, and
must trust the DTF to send the proof of receipt to the contract. The
actual proof of receipt will be provided publicly by that custodian, who
must be trusted not to misappropriate the funds in any case.

Until this process has established trust with brokers, the DTF will
assume a micro-credit to make an atomic swap with the broker through the
custodian. Instead of the broker, the DTF will receive the native
currency from the smart contract, and convert it to pay back the
micro-credit.

The custodian notarizes and publishes any transactions and balances of
the DTF’s account. This allows users to independently verify that all
liabilities of the DTF are still accounted for at any point in time. In
case of a mismatch, the user can send a fraud proof to the PoA contract,
causing it to lock down and suspend all trading.

Users who want to purchase tokens instantly for a fixed price can do so
on arbitrary, ERC20-compatible, external token markets. This alleviates
the burden of acquiring and handling a basket of all the different
currencies, and allows users to simply buy PoA tokens with a single
currency and without any waiting periods, especially in the case of
coin-traded funds (CTFs).

### 3.1. Creation
When creating a new PoA contract, the broker defines its parameters:

| Parameter      | Description                                                                    |
|----------------|--------------------------------------------------------------------------------|
| Asset ID       | Identification of the asset, like ISIN                                         |
| Name / Symbol  | Name and symbol of the token within the smart contract ecosystem               |
| Minimum Supply | Minimum amount of initial funding required                                     |
| Custodian Info | Data required to validate the proof from the custodian                         |
| Timeout        | Time at which the `funding` stage is canceled if it has not reached the target |

The contents of these fields vary between the different types of PoA
contracts, which are detailed below.

For example, a CTF contract usually requires only a negligible minimum
supply. Thus, it has no `funding` stage and requires no extension
contract, since its new tokens activate as soon as they are created.

The custodian info for a real-world-asset contract is a custodian’s
public key, which is used to sign the proof-of-assets. A crypto-asset
contract requires a list of all the accounts used to hold the different
currencies.

### 3.2. Activation
To activate a PoA contract, it must receive valid proof from the
custodian that the assets have been received by the DTF.

For real-world-asset contracts, this proof is a signature from the
custodian, notarizing the current account balance of the DTF.

For crypto-asset contracts, the proof is based on validating the
inclusion of a funding transaction in the foreign blockchain.

If no valid proof is received within the specified timeout, the contract
pays back all collected funds to the investors.

### 3.3. Trading
All PoA tokens are tradeable on ERC20-compatible external markets, as
with any other token.

### 3.4. Dividend Payout
When the asset tracked by the PoA contract yields any dividends, those
will be converted to native currency by the DTF, sent to the PoA
contract, and distributed among all token holders. Users can then claim
their share of the profits at any time.

### 3.5. Redemption
At any time users can redeem their active PoA tokens for their current
value in native currency.

To do this, the user must first complete a mandatory know-your-customer
(KYC) process with Brickblock.

When the user sends PoA tokens back to the contract, they will receive
the current value of the tracked asset in native currency. The contract
will notify the DTF, which provides the required native currency and
liquidates the appropriate number of shares through the broker.

### 3.6. Extension
To extend the of a PoA contract, a subcontract, which implements a new
funding round identical to the parent contract, can be created. Upon
activation, the subcontract will send the proof-of-assets to its parent,
prompting it to merge the new balances.

Note that this will only be necessary when the new funding round itself
has a Minimum Supply restriction. In most cases, users can simply
purchase new tokens from the contract while the DTF acquires the
necessary additional assets.

### 3.7. Fraud Proofs
All liabilities of the DTF will be publicly accounted for in a way that
allows everyone spotting a discrepancy to prove this fact to the PoA
contract.

When the contract receives valid proof-of-fraud, it automatically locks
down and suspends any activities.

Unless the contract is provided with a new and valid proof-of-assets
within a certain time, it will self-destruct and invalidate all its
tokens.

After the contract has been unlocked again, it will resume normal
operations.

## 4. Real-World-Asset Funds
The real-world-asset funds contract implements funds consisting of
foreign assets, such as exchange-traded funds (ETF) and real estate
funds (REF).

### 4.1. Funding
The PoA contract initially sells its tokens to investors in exchange for
native currency, until the specified funding goal is reached.

### 4.2. Failed
If the funding goal is not reached within the specified time frame or
the activation times out, then the contract moves into the `failed`
stage.

Investors can send their purchased PoA tokens back to the contract, and
they will receive their native currency in return.

### 4.3. Pending
If the funding goal is reached, the contract goes into the `pending`
stage and tells the broker to secure the foreign assets.

The broker secures the foreign assets and transfers them to the DTF’s
account with the custodian.

The custodian will notarize and publish all transactions on the DTF’s
account, along with the corresponding PoA contracts address.

The custodian will do so to activate the PoA contract, by
cryptographically signing a statement consisting of the following:

| Parameter | Description                             |
|-----------|-----------------------------------------|
| Address   | The address of the contract             |
| ISIN      | The identification of the foreign asset |
| Amount    | The number of shares transferred        |

### 4.4. Activation
If the Proof is valid, the contract transitions into the `active` stage.
If the contract is not activated within a certain time frame, it moves
into the `failed` stage.

The contract verifies that the signed statement both consists of the
expected data and has a valid signature from the custodian. To do this,
it first recreates the expected statement data from its own memory, then
it uses this data in combination with the received signature to recover
the signing address. If the recovered address equals that of the
custodian, it is simultaneously proven that the data is correct and the
signature was indeed made by the custodian. If the statement data is
different from the information that the custodian used to create the
signature, recovery yields a different address and the contract does not
activate.

The PoA contract notifies the broker that his or her funds are cleared.
The broker can now request reimbursement from the PoA contract and will
receive the collected native currency.

### 4.5. Dividend Payout
Whenever the tracked asset yields any dividends, they will be converted
into native currency and sent to the PoA contract.

Investors can then claim their share of the dividends at any time.

### 4.6. Redemption
Investors can redeem their PoA tokens for the current market price of
the tracked asset.

Tokens sent back to the contract are burned, and the DTF requests the
broker to liquidate the associated assets.

The DTF converts the received funds into native currency and sends it to
the contract, for the user to claim.

### 4.7. Extension
A broker can decide to extend the asset base of an existing PoA
contract.

To do so, the PoA contract creates a new instance of itself, which will
run through the same funding and activation process previously
described. It will also naturally share certain properties, such as the
ISIN and Symbol, with its parent.

Once the subcontract has completed `funding`, it moves into the
`absorbed` stage, and the parent merges its token balances. Since the
subcontract follows the exact same rules as the parent, the parent can
accept the new tokens as valid and fungible with its own.

<img src="https://github.com/brickblock-io/whitepaper-technical/raw/master/img/token-lifecycle.jpg" width="800" />

*Figure 3: Proof-of-Asset token lifecycle for ETFs / REFs*

## 5. Crypto Funds
The crypto fund contracts implement funds consisting of different crypto
currencies or tokens, such as [Bitcoin](https://bitcoin.org),
[Litecoin](https://litecoin.org/), [Dash](https://www.dash.org/),
[Ethereum](https://ethereum.org/), [Golem](https://golem.network/), or
even BBK or ACT themselves.

There are three types of contracts, coin-traded funds (CTFs), coin
managed funds (CMFs), and autonomous coin funds (ACFs).

Users can purchase PoA tokens representing a certain basket of foreign
or native currencies and tokens.

Those funds can be passive CTFs, operating on pre-defined rules, or
active CMFs, traded by a broker on secured or unsecured markets.

In the future, we hope to add fully autonomous coin funds that implement
all trading logic inside the contract.

### 5.1. Passive (CTF)
The passive coin-traded fund contract holds a certain composition of
different cryptocurrencies, based on a pre-defined set of rules.

By changing the composition of the creation and redemption basket, the
contract adjusts its holdings to the changing market.

The custodian answers requests for quotes (RFQs) for the current basket
composition.

### 5.2. Active (CMF)
The coin managed fund contract will allow fund managers to trade the
received funds in their own accounts on third-party markets.

Users can pay in any currency and the fund manager converts it to the
desired composition.

#### 5.2.1. Secured Accounts
Brickblock will offer secured accounts, on which the fund managers can
trade, on verified and trusted exchanges. These accounts will have
limited functionality and can only be used to trade. Withdrawals are
only allowed back to the DTF. This will allow less-trusted fund managers
to offer their services in a controlled fashion in order to establish a
track record and gain credibility.

#### 5.2.2. Unsecured Accounts
To offer established fund managers the full flexibility they need to
perform well, they may be allowed to trade on their own accounts. They
are provided with full ownership of the funds and can manage them in any
way they choose. Fund managers may optionally provide proof-of-assets if
their setup supports them, but this is not mandatory.

Users will always be aware of the type of account and fund manager
securing their investments, and they can factor this information into
their risk calculation.

In the future, Brickblock will provide insurance for custodian accounts
or trusted fund managers.

#### 5.2.3. Dividends
If a fund yields dividends, they are collected by the fund manager,
converted to native currency, and sent to the contract. Token holders
are notified and can claim their share at any time.

#### 5.2.4. Redemption
Investors can redeem their tokens at any time, sending them back to the
contract. The fund manager will liquidate positions, convert them into
native currency, and send it to the contract for the user to claim.

### 5.3. Autonomous (ACF)
We also want to explore fully automatic contracts trading autonomously
across multiple blockchains.

Until a reliable two-way peg between most crypto-currencies is
implemented, the only option to do that is by using a third-party
exchange. The contract could interface with an API such as
[shapeshiftbot](https://github.com/axic/shapeshiftbot) and exchange
currencies on its own.

By allowing users to submit trading algorithms and market models
implemented as smart contracts, we aim to create a market for
trading-bots. Developers can offer their results, and users can evaluate
the performance and invest in the preferred contracts.

## 6. Compatibility
All token contracts will be compatible with the
[ERC20](https://theethereum.wiki/w/index.php/ERC20_Token_Standard)
specification, which makes it trivial to trade them in compatible
exchanges and wallets.

Bitcoin and derived blockchains will be verifiable by the smart contract
through simplified payment verification (SPV), for example
[BTCRelay](http://btcrelay.org/).

The smart contract ecosystem will be implemented on the [Ethereum](https://ethereum.org/) blockchain.

### Glossary
- **Asset Base**: The amount of managed by the PoA contract.
- **Custodian**: A trustworthy organization holding the portfolios of the DTF.
- **Digital Trust Fund (DTF)**: A strictly regulated financial entity which legally owns the assets on behalf of its investors.
- **Foreign Asset**: An asset existing outside of the Ethereum ecosystem, i.e. not Ether but REFs, ETFs or CTFs.
- **Native Currency**: A currency native to the blockchain platform, like Ether (ETH) for Ethereum.
- **PoA Token**: A smart token as per ERC20, representing an equal value to a certain foreign asset.

<br>

#### Disclaimer

Please note that this document is still an early draft; some
implementation details are missing or subject to change.

The purpose of this document is to convey the technical aspects of our
vision, giving the reader a way to evaluate the feasibility of our
design.

We are in the process of developing a proof-of-concept implementation
and will release a first prototype before our pre-sale in August.
Technical implementation details will be added as soon as they are
decided.

We are open to feedback and suggestions from the community and will do
our best to thoroughly evaluate all options and not rush any decisions.

Copyright (c) 2017 brickblock.io Without permission, anyone may use, reproduce or distribute any material in this whitepaper for non-commercial and educational use (i.e., other than for a fee or for commercial purposes) provided that the original source and the applicable copyright notice are cited.
