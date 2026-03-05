---
title: "Creating a Cryptocurrency for a Pet E-commerce: The PetCoin Case"
date: 2017-11-20 00:00:00 +0000
categories: [Blockchain, Entrepreneurship]
tags: [blockchain, ethereum, solidity, smart-contracts, cryptocurrency]
lang: en
layout: post
---

## The Idea: A Digital Currency for Pet Lovers

In 2017, I dove headfirst into the blockchain universe with a challenging and exciting project: creating a proprietary cryptocurrency for a pet product e-commerce. The idea was to have a digital currency, **PetCoin**, that could be used to buy products, earn discounts, and participate in an exclusive loyalty program.

## The Technology Behind PetCoin

To bring the project to life, technology choice was fundamental. I opted for the **Ethereum** platform, which already stood out for its ability to execute "smart contracts."

- **Ethereum:** A decentralized blockchain that allows the creation of applications (DApps) and custom tokens.
- **Solidity:** The programming language used to write smart contracts on Ethereum. Its syntax resembles JavaScript and C++, which facilitated the learning curve.
- **ERC-20 Standard:** A set of standard rules for creating tokens on the Ethereum network. Following this standard ensured that PetCoin would be compatible with most existing wallets and exchanges.

## Development Stages

The PetCoin creation process involved several stages:

1. **Token Definition:** I determined the name (**PetCoin**), symbol (**PETC**), total coin supply, and number of decimal places.
2. **Smart Contract Development:** Using Solidity, I wrote the contract that defines all PetCoin rules: how coins are created, transferred, and queried. I implemented basic ERC-20 standard functions like `transfer()`, `balanceOf()`, and `approve()`.
3. **Testnet Deployment:** Before officially launching, I published the contract on an Ethereum test network (like Ropsten). This allowed testing all functionalities without spending real Ether.
4. **Wallet Creation:** I developed a simple web interface using Web3.js, so users could create their wallets, view PetCoin balance, and perform transactions within the e-commerce site.
5. **E-commerce Integration:** The final step was integrating the wallet with the store's payment system, allowing customers to use their PetCoins to buy products.

## Conclusion

Creating PetCoin was an immense learning experience about blockchain technology's potential to transform businesses. The project not only enabled an innovative payment system but also opened doors to a new model of customer engagement and loyalty. It was one of the first steps in my journey as a decentralized solutions developer.