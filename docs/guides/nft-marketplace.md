---
title: NFT Marketplace
sidebar_label: NFT Marketplace
description: An overview of the NFT marketplace guide
sidebar_position: 5
sidebar_custom_props:
  icon: 🔍
---

This guide provides the required information to the development teams looking to build NFT marketplaces on the Flow blockchain.

Developers should read this guide in conjunction with the other materials for developers looking to build on the Flow blockchain, especially the [Flow concepts](../build/flow.md).

## App-Custody vs. Self-Custody model

The app-custody model for an NFT marketplace is where the platform controls all the user accounts, the associated private keys, and, therefore, the assets in those accounts. The self-custody model for an NFT marketplace is where users control their assets and use their wallets to perform activities in the marketplace.

If you are building a general-purpose NFT marketplace, the preferred approach is self-custody, allowing users to interact with their third-party NFTs, and list them for sale.

**This guide will primarily focus on the general-purpose self-custody NFT marketplace platforms that enable users to utilize third-party NFTs on the marketplace platform for secondary sales.**

## Key capabilities

Below are the key capabilities an NFT marketplace would typically perform.

### Handling user accounts

Let users connect their wallets and ...

- Display the list of NFTs belonging to the user so that they can browse through to decide which ones to list for sale
- Display crypto balance of the user account

### Minting NFTs

Allow users to upload images and other types of media to mint new NFTs. These NFTs, in turn, can be listed for sale by the users.


### Selling NFTs

Enabling sales on an NFT marketplace requires ...

- Enabling users to list their NFTs for sale
- Implement various sale methods, including auctions
- Enabling users to purchase NFTs for sale
- Display list of NFTs for sale
- Facilitating payment between buyers and sellers
- Facilitating royalty payments to the creators/IP-holders

## Best practices
Besides following the best practices of building a scalable web application that can handle unexpected user traffic surge, NFT marketplaces should also follow the best practices listed below but often ignored.

## Maintain accuracy of displayed price

NFT purchase ultimately settles on the blockchain, but NFT buyers see the prices of the NFTs for sale on the marketplace website. The marketplace platform should ensure that the price listed on the web page, their backend, and the blockchain are in sync. If that is not the case, it can lead to ugly surprises for the NFT buyers.

## Clean up stale NFT listings

A stale listing is when marketplace lists an NFT that is no longer for sale or the price has changed. Often, sellers change their listing price for the NFT on sale. That can lead to multiple listings on the chain. After a price change, marketplace platforms should ensure that any lower price listings are deleted from the chain. Also, once the NFT is sold, the marketplace should [clean up](https://github.com/onflow/nft-storefront/blob/main/transactions/cleanup_purchased_listings.cdc) all sale listings for that NFT.

## Provide rich filtering/search capabilities

Frequently, NFT buyers are inundated by irrelevant NFTs while not being able to find what they are looking for. Having comprehensive search and filtering capabilities on the platform can help provide good UX to the NFT buyers. Filtering capability should be implemented, keeping in mind that different NFT types have different traits

## Core components

Following are the core components for any NFT marketplace solution on the Flow blockchain. Throughout the guide, these will be referred to.

- [NFT Standard](https://github.com/onflow/flow-nft): Every NFT minted on Flow should follow this standard.
- [Fungible Token Standard](https://github.com/onflow/flow-ft): All fungible tokens on Flow follow this standard. NFTs purchased on an NFT marketplace will be paid for using a fungible token in many scenarios.
- [FLOW Token](../build/basics/flow-token.md): Native currency of the Flow blockchain used to pay for the transaction fees. NFT buyers can use FLOW tokens to buy NFTs in a marketplace.
- [NFT Metadata Standard](https://github.com/onflow/flow-nft/#nft-metadata): A generalized framework for NFTs on Flow to expose their metadata. The framework described already has [a mechanism specified to render the basic representation](https://github.com/onflow/flow-nft/#list-of-common-views) of an NFT.
- [Flow NFT Catalog](https://github.com/dapperlabs/nft-catalog): NFT marketplaces can use this on-chain registry of NFTs to obtain the list of NFTs owned by an account and obtain display metadata for those NFTs and their collections. Developers should use the [example scripts](https://github.com/dapperlabs/nft-catalog#using-the-catalog-for-marketplaces-and-other-nft-applications) in conjunction with the [NFT Metadata Standard](https://github.com/onflow/flow-nft/#nft-metadata).
- [NFT Storefront Contract](https://github.com/onflow/nft-storefront): ​​Contract used by almost all Flow NFT marketplaces for creating NFT sale listings. Note that you can directly use the version of this contract already deployed on the Mainnet.
- [FCL (Flow Client Library)](../tools/clients/fcl-js): This library is like web3.js in Ethereum. Application frontend will use FCL to interact with user wallets and the blockchain. JS-based backends of applications can also use it to interact with the blockchain.
- Flow SDKs: Multiple SDKs are available in different programming languages ([Go](../tools/clients/flow-go-sdk/index.mdx), [Java](https://github.com/the-nft-company/flow-jvm-sdk)) for Flow application backends to interact with the blockchain.

## Coming from Ethereum

If you are coming from Ethereum, the following list shows corresponding modules and services available on Flow.

| Resource                            | Ethereum                | Flow                                                                                                  |
| ----------------------------------- | ----------------------- | ----------------------------------------------------------------------------------------------------- |
| Smart Contract Programming Language | Solidity                | [Cadence](../build/cadence.md)                                                                     |
| Fungible Token Standard             | ERC-20                  | [Flow Fungible Token Standard](https://github.com/onflow/flow-ft)                                     |
| NFT Standard                        | ERC-721/ERC-1155        | [Flow NFT Token Standard](https://github.com/onflow/flow-nft)                                         |
| NFT Metadata Standard               | ERC-721                 | [Flow NFT Metadata Standard](https://github.com/onflow/flow-nft/#nft-metadata)                        |
| Native Crypto Currency              | ETH                     | [FLOW](../build/basics/flow-token.md)                                                                  |
| Wallet Interaction Library          | web3.js                 | [Flow Client Library (FCL)](../tools/clients)                                                     |
| Blockchain Interaction SDK          | web3.js                 | [Language Clients](../tools/clients)                                                              |
| Block Explorer                      | Etherscan               | [Block Explorers](../tools)                                                            |
| Node Service Providers              | Infura                  | [Official Flow Access Nodes](https://flowscan.org/staking/nodes)                                      |
| Wallets                             | Metamask, WalletConnect | [Blocto](https://portto.com/), [Dapper Wallet](https://www.meetdapper.com/)                           |


## Minting 


Most NFT marketplaces give their users the ability to mint NFTs from user-provided digital media. Users can, later on, post the minted NFTs for sale.

An NFT marketplace can add support for minting NFTs on the Flow blockchain using a contract based on the [Flow NFT Standard](https://github.com/onflow/flow-nft). [Here](https://github.com/onflow/flow-nft/blob/master/contracts/ExampleNFT.cdc) is an illustration of how to write a contract for your own NFT.

A few important points to note:

- Do implement your NFT contract to conform to the [NFT Metadata Standard](https://github.com/onflow/flow-nft/#nft-metadata). You should implement the “Display” view to provide the rendering information for the NFTs. This [code snippet](https://github.com/onflow/flow-nft/blob/master/contracts/ExampleNFT.cdc#L73) shows how to implement metadata in the NFT contract. Conforming to the metadata standard will facilitate rendering by wallets, tools, and other applications.
- Do make sure to register your contract in the [Flow NFT Catalog](https://github.com/dapperlabs/nft-catalog). Doing so will ensure that wallets and other applications will render your NFTs. It will also guarantee the authenticity of NFTs minted by your application on other platforms.
- Individual collections per user are not currently supported. So NFTs for all the users will have to share the same contract. You can include a field in the contract indicating a collection name specific to a creator.

## ​​Lazy Minting

Some blockchains have super high gas fees. That compels NFT platforms, especially marketplaces, to implement "[lazy minting](https://medium.com/rarible-dao/nft-minting-vs-lazy-minting-mining-explained-4330dd57a4c4)" to save on gas/transaction fees.

Flow blockchain's mission is to make blockchain accessible for the masses. We will never expect to see high transaction fees on the Flow blockchain.

<Callout type="info">
You can check the details on Flow fees [here](../build/basics/fees.md#fees).
</Callout>

Based on current and future low fees on the Flow blockchain, NFT marketplaces do not need to implement lazy minting.


## Selling


Most NFT marketplaces performing on-chain sales use the [NFT Storefront Smart Contract](https://github.com/onflow/nft-storefront). The contract allows NFT sellers to list their NFT for sale in any fungible token. NFT buyers send the fungible token amount equal to the listed price and receive the listed NFT in return.

A few important points to note regarding listing NFTs for sale through the [NFT Storefront docs](../references/core-contracts/nft-storefront):

- Same NFT can have multiple sale listings created. The NFT owner can create multiple listings on the same marketplace or different marketplaces.
- NFT owners can create different sale listings for the same NFT with different fungible tokens as payment methods. For example, the NFT owner can list the same NFT for sale in both FLOW and USDC tokens.
- NFT listed for sale using the [NFT Storefront docs](../references/core-contracts/nft-storefront) is not locked. It remains in complete control of the NFT owner before the sale, and the NFT owner can transfer it before the sale.
- Any sale listing created allows for multiple sale cuts. That can enable royalty payments to multiple entities for sale.
- For an NFT, the NFT owner can create different listings with different sale cuts. That allows each listing to have unique royalty payment schemes.
- An NFT marketplace platform can create sale listings for NFTs minted by it and NFTs minted by third-party projects.
- NFT Storefront Contract allows for only simple sale schemes of individual NFTs. You will need to write your version of the contract if
  - NFT has to be sold through an auction
  - Instead of NFTs, packs of NFTs need to be sold on-chain

## Listing NFTs for sales

[Sell Item](../references/core-contracts/nft-storefront#selling-nfts) is an example of a transaction creating an NFT sale listing. The listing creator provides sale cuts (royalties, platform fees). The NFT Storefront docs does not determine the sale cuts by itself. The listing creator (NFT marketplace) should figure out the royalties to be paid and populate the sale cuts fields of the transaction.

The sale listing specifies the fungible token the NFT is sold in. Typical fungible tokens used are FLOW and USDC.

## Purchasing NFTs

[Buy Item](../references/core-contracts/nft-storefront#purchasing-nfts) is an example of a transaction purchasing an NFT from an NFT Storefront Contract listing. The buyer needs to use the same fungible token specified in the listing.

### Setting up accounts to receive NFTs

Flow blockchain has a feature that unless an account is setup to receive a particular NFT type, they can not receive NFTs of that type. That is why users need to authorize a transaction through their account to receive a specific NFT type. 

An NFT marketplace should enable users to set up their accounts to receive an NFT before purchasing that NFT. [Flow NFT Catalog](../tools/nft-catalog/index.mdx) can provide the storage path information to let platforms craft such account set up transactions.

After the NFT purchase operation is complete on the blockchain, the NFT marketplace platform should explicitly clean up the sale listings. Leaving dangling sale listings is not a good practice.

## Payment options

Currently, for the on-chain sale of NFTs, the most common option is to let sellers list NFTs for sale in fungible tokens like USDC or FLOW.

The marketplace will have the NFT owner sign a sell item transaction to create the sale listing. The sell item transaction is a type of fungible token the NFT seller will accept.

### Fiat payments

NFT marketplaces can reduce UX friction for the crypto-uninitiated NFT buyers by accepting payments in fiat (credit/debit cards and bank payments).

But enabling fiat payments invariably requires the NFT marketplace platform to take custody of users' funds and/or NFTs. And in doing so, the platforms will have to handle regulatory and payment-fraud/chargeback/AML issues.

Following are typical ways NFT marketplaces can enable fiat payments for P2P sales:

- Using payment providers like Stripe: NFT marketplaces can enable fiat P2P sales by onboarding the sellers on a payment provider like Stripe, taking custody of the NFTs from the sellers, and accepting payments from the buyers using credit cards. NFT sale is completed through a transfer of NFT. Sellers may have to go through KYC with Stripe to get onboarded. Sellers will get paid in their bank accounts by Stripe. The downside is that seller onboarding can take time, and the platform can be hostage to the capricious rules of the payment providers.
- Using crypto on-ramps like Moonpay: With this approach, the NFT marketplace facilitates NFT sales on-chain but leverages crypto on-ramps such as Moonpay/Wyre to help NFT buyers obtain crypto to make the purchase. The downside is that the crypto on-ramps charge hefty fees, and NFT sellers still need to find off-ramps for the proceeds of the crypto sale.
- Using a payment gateway to maintain user fund balances: This is the approach that NBA Topshot/Dapper wallet follows. The marketplace uses a payment gateway like Circle where users use fiat payment methods to maintain fund balances. The marketplace also maintains a app-custody wallet for each user. It makes the on-chain NFT purchase on behalf of users if sufficient fund balance is maintained with the payment gateway. Fund balances with the payment gateway are updated based on sale/purchase activity on the blockchain. The downside of this approach is that it requires the marketplace to maintain a app-custody wallet service. Additionally, the marketplace must incorporate multiple operational checks and balances to comply with the payment/AML regulations and protect against chargeback/fraud risks.

See this `In app payment`section below to get more information.

## Royalty payments

A marketplace can gather the royalty information for an NFT listed for sale through the following ways:

- Offline methods: The project behind the NFT can inform the marketplace of the royalty percentage it expects from the NFT sale.

- NFT metadata: NFT projects can specify the “Royalty” view per the [NFT Metadata Standard](../references/core-contracts/nft-storefront#enabling-creator-royalties-for-nfts) . Marketplaces can use that information to enforce royalty payments when creating the NFT sale listings. [_Currently, “Royalty” view specification is unspecified. Proposals from NFT Marketplace projects are welcome._]


# In App payments


## Introduction

User payments are an important part of many blockchain apps. In order to create a stellar user experience, your app should make it as easy as possible for users to purchase digital assets. For most users, this means being able to pay for digital assets (e.g. NFTs) using their local fiat currency (e.g. via credit card).

## Direct vs Peer-to-peer Sales

Before diving into payments, it is important to define the difference between direct sales and peer-to-peer sales.

* **Direct sales:** users purchase digital assets (e.g. NFTs) directly from a app developer.
* **Peer-to-peer sales:** users purchase digital assets (e.g. NFTs) from other app users, usually via a marketplace.

This section will highlight the considerations you should make when implementing payments for direct sales and peer-to-peer sales.

## Available Payment Options

### FLOW

FLOW is the default cryptocurrency that powers the Flow blockchain network, and is primarily used for staking and for paying [network fees](../build/basics/fees.md#fees).

Many of the payment providers support FLOW, including well-trusted cryptocurrency exchanges.

However, FLOW isn’t always well-suited for day-to-day user payments due to its price volatility, much like any cryptocurrency.

<Callout type="warning">

**App Custody Gotcha:**  app-custody apps that allow their users to hold FLOW will need to comply with any necessary regulations regarding cryptocurrency asset custody. Consult your legal counsel.

</Callout>

### Direct Credit Card Payments

Some apps choose to directly integrate off-chain credit card providers such as [Alipay](https://en.wikipedia.org/wiki/Alipay), [Circle](https://en.wikipedia.org/wiki/Circle_(company)), [PayPal](https://en.wikipedia.org/wiki/PayPal) or [Stripe](https://en.wikipedia.org/wiki/Stripe_(company)) in order to support NFT sales. In these scenarios, payments are processed externally by the provider, and then the NFT asset is delivered in a transaction authorized by the app developer.

While direct credit card payments result in a good user experience, they bring significant challenges for the app developer. Credit card payments are prone to [chargebacks](https://en.wikipedia.org/wiki/Chargeback) and fraud, meaning users can purchase an asset, dispute the credit card transaction, and then keep the original asset without paying. Because of this, app developers sometimes opt for the app custody model when supporting direct credit card payments.

Credit card payments introduce additional challenges when used in peer-to-peer payment platforms such as an NFT marketplace. When a user buys an NFT from another user using their credit card, the payment is still processed by the app developer, which makes the developer responsible for paying the seller. To pay the proceeds to the seller, the developer must comply with necessary regulations. Consult your legal counsel.

### Recommendation

USDC and FLOW are the recommended payment methods for FCL-powered apps, due to their ease of integration, stability and widespread ecosystem support.


# Handling Accounts

## Sign-in/up functionality for Users

On many blockchains, decentralized apps offer the Connect Wallet button to let users connect their wallets. Flow blockchain tries to make things even more friendlier for the end-users. It allows applications to offer users the opportunity to register for a wallet (aka create a wallet) if they do not have a Flow wallet already. If users already have a wallet, the Sign-in/up functionality authenticates the user.

Sign-in/up functionality is implemented using [FCL](../tools/clients/fcl-js/index.md). The following documentation provides details on how to implement this functionality on the application frontend:

- [FCL Authentication Functionality](../tools/clients/fcl-js/authentication.md)
- [Sign-in/up using FCL with Blocto Wallet](https://docs.blocto.app/blocto-sdk/flow/login-register)

Using FCL, you can get the authenticated account information for your users.

<Callout type="info">
You can see this functionality in action on the [Vault by CNN](https://vault.cnn.com/) website.
</Callout>

## Displaying NFTs belonging to the User

NFT marketplaces can use the [Flow NFT Catalog](https://github.com/dapperlabs/nft-catalog), an on-chain registry of NFTs, to obtain the list of NFTs owned by an account and obtain display metadata for those NFTs and their collections. Developers should use the [example scripts](https://github.com/dapperlabs/nft-catalog#using-the-catalog-for-marketplaces-and-other-nft-applications) in conjunction with the [NFT Metadata Standard](https://github.com/onflow/flow-nft/#nft-metadata).

For an NFT project that is not yet present in the [Flow NFT Catalog](https://github.com/dapperlabs/nft-catalog), to find out the NFTs owned by an account, you need to know the public path of that NFT’s collection. See [this](https://github.com/onflow/flow-nft#list-nfts-in-an-account) for more information.

### Rendering NFTs

All newer NFT projects will conform to the [NFT Metadata Standard](https://github.com/onflow/flow-nft/#nft-metadata). You can use the Display view to get the rendering information from the NFTs conforming to the metadata standard. [Here](https://github.com/onflow/flow-nft#how-to-read-metadata) is the code example on how to do that.


