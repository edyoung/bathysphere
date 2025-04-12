+++
author = "Edwin Young"
tags = ["Crypto"]
date = "2022-01-21"
description = "Why DAOs don't work the way you might expect"
slug= "dao-hype"
image= "image.png"
title = "DAOn't believe the hype"
type = "post"
+++


Disclaimer: I'm not an expert on the crypto space, just a curious amateur. It's quite possible I have misunderstood something important below. Do let me know of any corrections via the comment section!

If you read the [Ethereum website on DAO's](https://ethereum.org/en/dao/), you'll see this:

>DAOs are an effective and safe way to work with like-minded folks around the globe.

>Think of them like an internet-native business that's collectively owned and managed by its members. They have built-in treasuries that no one has the authority to access without the approval of the group. Decisions are governed by proposals and voting to ensure everyone in the organization has a voice.

>There's no CEO who can authorize spending based on their own whims and no chance of a dodgy CFO manipulating the books. Everything is out in the open and the rules around spending are baked into the DAO via its code.

This is *not really true* for most DAOs. I'll explain. 

I will use 'The Spice DAO' (aka Dune DAO) as an example, since that's been [in the news](https://www.buzzfeednews.com/article/amansethi/spicedao-dunedao-soby) lately. This DAO was originally set up with the stated goal of purchasing a copy of the 'pitch book' Alejandro Jodorowsky put together while trying to persuade Hollywood studios to fund a movie he wanted to make based on Frank Herbert's novel Dune. They succeeded in raising a substantial amount of money - approximately $12M dollars. They were then able to purchase the book at auction, but then had to work out what to do with the rest of the money. I think the fight that ensued over the proper fate of the funds encapsulates a lot of the issues around DAO governance. 

# The contract

It *is* true that there's no official CEO or CFO for a DAO. But that doesn't mean there isn't an inner circle of people who hold the true power. In a DAO, these are the people who know the private keys to the DAO's wallet. For the Dune DAO these are 

```
Spice DAO's funds will be held in a 3/5 Gnosis Multi-sig of the following team:

@Yojimbo_King (Remilia Collective)
@Sobylife (Ex Populus)
@CharlieFang77 (Remilia Collective)
@DeezeFi (Fractional Art)
@DrydenwtBrown (Praxis Society)
```
[source](https://dune.foundation/)

This means that the DAO can only transfer funds if 3 out of 5 of these people agree to do so by signing the transaction. (Incidentally, it would be entirely possible to create a DAO where all of the signing keys are held by the same real-world individual via multiple separate wallets, thus giving the illusion of a 'board' where none exists. I'm not alleging that is the case for the Spice DAO, just that in general it is possible to do so, and quite hard for someone else to detect.)

What limitations are there on what the signers can do, as long as 3 of them agree to do it? Can they spend all the money on fine wines and yachts? Well, they can do whatever the smart contract allows them to. Ethereum allows 'smart contracts' - snippets of code which enforce rules, mostly about how cryptographic transactions occur. So you might hope from the Ethereum website's description that "There's no CEO who can authorize spending on their own whims" is enforced by a smart contract. 

For Spice DAO, and I believe the vast majority of DAOs, this is not the case. The 'smart contract' can be found [here](https://etherscan.io/address/0x9b6db7597a74602a5a806e33408e7e2dafa58193#code). It's rather difficult to audit because there's no verified source code for this contract, just a bunch of Ethereum bytecode. If it's not obvious to you what a page of hex digits starting with '0x608060405234801561001057600080fd5b50600436106101515760003560e01c8063715018a61...' means, you're not alone. Fortunately the Etherscan.io website comes with a decompiler which can convert it to [something semi-readable](https://etherscan.io/bytecode-decompiler?a=0x9b6db7597a74602a5a806e33408e7e2dafa58193). 

I'm not an expert in deciphering these contracts (and I definitely wouldn't be able to spot a well-disguised malicious contract), but as far as I can see it's just a wallet. In particular there's no encoded limitations on what the wallet holders can do (such as limits on transaction size or counterparties). So my claim is that in practice, '3 out of 5 wallet holders *can* authorize spending based on their own whims'.

"But wait!" I hear you say. What about governance tokens? Don't those control the DAO?

Glad you asked. 

# Governance Tokens and Proposals

When you fund a DAO, you are typically given tokens in return - in the case of the Spice DAO, these are called $SPICE. These can be bought and sold, and each such token gives you one vote on any governance proposal. If a majority of tokens vote in favor of the proposal, it is passed. The Spice DAO page says "Proposals will be executed on-chain by the multisig." which sounds important. Power to the people! 

Well, sort of.

The Spice DAO has had one governance proposal so far, the text of which can be seen here: https://snapshot.org/#/dunedao.eth/proposal/0x2038fc240d0e85c496cc692d0001fb159d6f613b546392aa96e49a3b752ced0d . This was held *after* the initial auction. It's interesting to me that during the most important times in the DAO's existence - the initial fundraising and the purchase of the book - there was actually no formal governance of the DAO beyond the mission statement on the fundraising page. In fact, the book was actually purchased by @sobylife using his own money, and the DAO reimbursed him.

The proposal formalizes who can do what, and how much money they can spend, eg.

> Operations Budget

> As Treasurer, Charlie will have autonomy in using funds to cover monthly operational expenses such as core team compensation and third-party independent contractor invoices with a monthly discretionary budget of 50,000 USDC:

> Core team compensation = Maximum 31,665.60 USDC/month

> Third-party contractors such as IT, social media agency, book shipping and storage and software developers, and unforeseen incidentals = Maximum 18,334.40 USDC/month


The governance proposal was passed with little fuss and 95.97% of votes in favor. It was 'executed on chain' by creating a block with a link to an [IPFS file](https://cloudflare-ipfs.com/ipfs/QmYLXfkgsoPkHJsEZxNV1GfgnSp8u4qrjaocx175yVVzVb) containing the English text of the proposal. But it's important to note a couple of things.

1) The governance proposal is just English, not code. It has no technical effect on the smart contract or what the DAO signature holders can actually do.
2) It's a little unclear whether it constitutes a legally binding contract either. I Am Not A Lawyer, but I don't believe it's well-settled law whether a DAO token owner can sue anyone for not abiding by the terms in the governance proposal. 

So these governance proposals are purely advisory, and they're almost certainly toothless if the signature holders don't want to play ball. Also also:

- Who gets to decide if a governance proposal even comes up for a vote? Why the signature holders of course. If you have a proposal they don't like, nobody will ever get to vote on it.
- In this case, over 50% of the votes were cast by a single individual. I suspect whales will determine the outcome of any proposal vote in practice.

# Conclusions

Many DAOs are neither decentralized (power belongs to the signers and somewhat to major token holders), autonomous (most smart contracts are anything but) nor particularly organized. They are essentially wallets with a handful of signers and a manifesto. You might want to bear this in mind when choosing whether to invest. There are definitely some projects which are trying more complex approaches (see https://medium.com/compound-finance/building-a-governance-interface-474fc271588c) but you should not assume that any given DAO uses these. 

