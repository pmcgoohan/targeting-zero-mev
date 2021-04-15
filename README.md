# Targeting Zero MEV - A Content Layer Solution


## Introduction


### No Satisfaction Guaranteed

Around 1.4 billion dollars will be taken from Ethereum users this year as miner extractable value (MEV). This is in markets valued at around 50 billion dollars.

The nearest equivalent in the traditional financial markets is HFT (high frequency trading). One recent estimate is that HFT is now worth only 1 billion dollars per year in markets valued in the 100s of trillions of dollars, so less than MEV in absolute terms and certainly as a ratio of volume.

It seems odd that a decentralized blockchain like Ethereum could suffer worse exploits than it’s traditional centralized competitors. Wasn’t decentralization meant to fix this stuff?

Well our instincts are correct. Decentralization will fix the problem. The reason these problems have not yet been fixed is that Ethereum has not yet fully decentralized.


### Hidden Centralization

A network is only as decentralized as its weakest point. 

Blockchain structure is fully decentralized. Blocks are proposed and validated by consensus across tens of thousands of nodes. But there is a dirty secret at the heart of each block. While the blockchain _structure_ is created collaboratively, the _content_ of each block is not.

This fact is not obvious because it happens in private in the few milliseconds it takes for a miner/validator to create a block and because it is couched in the elegantly distributed data structure that surrounds it.

But the fact is that the _content_ of each block is created by a centralized authority without recourse, the miner. As long as a proposed block is _structurally_ sound, the _content_ of the block is undisputed by the consensus.

This distinction between _structure_ and _content_ is profound because nothing about block _structure_ creates the problem of MEV. Frontrunning, backrunning, sandwiching and other attacks all come from the centralized way in which block _content_ is produced.

Block content is not trustless.


### Content By Consensus

There's nothing wrong with the existing structural consensus layer in Ethereum, it works beautifully. But look at how block content creation sits uncomfortably within it, sneakily centralized in the miner. 



<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image1.jpg). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image1.jpg "image_tooltip")


Consider the famous double spending problem that blockchain technology was designed to solve: if one computer has complete control of a financial ledger, how can you stop it spending the same money twice? The answer is that you can’t. Instead you build a structural consensus where no single computer is in complete control of currency transfers, and the problem is solved.

MEV is the equivalent of the double spending problem for executable blockchains. If one computer has complete control of transaction inclusion and ordering, how do you stop it from frontrunning, backrunning, sandwiching and generally exploiting everybody else? Again, you can’t. Instead you build a content consensus layer where no single computer is in complete control of transaction inclusion and ordering, and the problem of MEV is solved.

So let’s free content from miner control and give it a dedicated consensus layer. Now we have a content layer within a consensus protocol stack. No-one is in control, and everybody is. We have decentralized. Now _that_ feels good.



<p id="gdcalert2" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image2.jpg). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert3">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image2.jpg "image_tooltip")



### Advantages

We remove control over the content of a block from a single party and distribute it across the network.


#### Fairness

By stripping any one agent of their ability to manipulate content, applications become fair and equitable to all users by default. Fairness will become an innate property of the network without the need for difficult and obstructive workarounds at the application level that are rarely implemented.


#### Integrity

MEV is all but eradicated because there is no centralized authority to bribe.


#### Impact

Block content protocols are a layer on top of existing block structure protocols. Tcp/Ip didn't need to be revised when p2p messenger apps came along. We don't need to revise the underlying block structure protocol to add the block content protocol beyond a few integration changes.


#### Interoperability 

The protocol does not change whether we are creating content for an eth2 validator, a rollup sequencer, eth1 miner or any other Ethereum structural layer. A single content consensus implementation can be used across all of these networks and more. Solve it for one and we solve it for all.


#### Philosophy 

There is currently a centralized aspect to the network and it is causing harm. We need to fix it if we are serious in our ambitions for full decentralization.


## Alex - A Block Content Consensus Protocol

What follows is an overview of one possible block content consensus protocol called Alex.


### Overview

Here is a simplified view of the protocol. Pickers choose transactions. Shufflers mix them up. The printer manages it all and prints the chunks to the blockchain (or rollup).



<p id="gdcalert3" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image3.jpg). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert4">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image3.jpg "image_tooltip")



### In Brief



*   A scheduler allocates a set of roles at random from the pool of nodes to work on each chunk of content:
*   Pickers each provide their unique view of the mempool by bundling pending transactions.
*   These are combined to **prevent transaction censorship**.
*   Shufflers each provide entropy.
*   These are combined to randomize each chunk of transactions and **prevent transaction reordering**.
*   Shufflers share their entropy with vaults who then reveal it if the shufflers don’t to **prevent withholding**.
*   If the process halts because a participant has gone offline or is being obstructive, skippers act to jump the set and **prevent denial of service**.
*   _eth2_: if a validator proposes a block that diverges from this consensus content, it will fail attestation and will not be included and the validator may be slashed (validators only need to subscribe to published content blocks, they don’t have to participate in content creation, although they may do)
*   _centralized rollup sequencer_: if the sequencer fails to write the consensus content, they will be slashed and possibly voted out
*   _distributed rollup sequencer_: as with eth2, their block will not be validated by the consensus and will fail and/or they will be slashed 


### Components



*   Printer (eg: an eth2 validator, a rollup sequencer or an eth1 miner)
*   Node Pool (a pool of nodes willing to take on the roles assigned by the scheduler)
*   Scheduler (a smart contract on L1 which allocates a set of roles at random to the node pool)
*   Picker Queue (a consensus queue of transactions to be included)
*   Shuffler Queue (a consensus queue that randomizes transaction order)


### Roles



*   Picker (picks transactions from the mempool)
*   Shuffler (randomizes the picked transactions using their entropy)
*   Vault (a backup of shufflers entropy)
*   Skipper (kickstarts the picker/shuffler queues if they halt)


### Printer

The printer is our link to the structural layer. It is the miner (eth1), the validator (eth2) or the sequencer (rollups). We'll call them a printer not a publisher because they no longer have meaningful editorial control over block content.

They participate in content creation by publishing chunks of content authored by pickers and shufflers before writing a batch of these chunks to the chain they are servicing. This is done deterministically so they have no power to manipulate the content without failing attestation/being slashed.

They can choose to truncate the content (stop before the end), or even not write any at all (empty block) without failing attestation/being slashed. But this will only delay the inevitable as the next printer will pick up from where they left off in the next block. In that situation the printer loses out on the gas fees from the transactions and gains nothing. So printers have no incentive to withhold.


### Messages

All messages are digitally signed. The public key and signature must be included when messages are embedded in other messages as well as when they are sent over the network.

Where the content layer shares the same network as the structural layer we may be able to reduce network load by including only transaction hashes rather than full transactions in the picker lists (MsgTransactionsLists) and the final chunks (MsgShufflerChunk). We must be sure to avoid any data availability issues that come from this optimization.


### Protocol Overview



<p id="gdcalert4" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image4.jpg). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert5">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image4.jpg "image_tooltip")



### Consensus Queue

We need a data structure for the picker queue and the shuffler queue. We need it to work asynchronously and to be maintained by consensus. 

We could use blockchains but they require a lot of work to achieve consensus and there is always the chance of a fork.

We want the content layer to be fast. We don’t have time to wait around for some threshold of block confirmations to be met. It is also vital that we avoid any chance of forking because doing so gives a choice to the printer and therefore an unfair advantage.

We need to build content by consensus in a way that is uncontestable. We can then prove that the printer either acted with the consensus and published the content correctly (for a gas reward) or against it (failing attestation/getting slashed).

To do this we use consensus queues managed by the printer. The various roles send messages when scheduled to. The printer compiles these into chunks linked together to form a queue. 

Formally it may be better described as a distributed linked list, but we use it like a first-in-first-out queue.


### Skippers 

The consensus queue is fast and simple, but it will get stuck if a node we are expecting a message from goes offline.

If we allow the printer to skip an offline node, it can abuse the privilege. It is equivalent to giving them censorship rights and therefore an unfair advantage and this is what we are working hard to remove.

So instead some nodes are assigned as skippers. If they suspect a node that we are waiting on is offline they send a skip message. If enough of these are sent, it grants the printer the right to skip the set. If the printer skips without the valid threshold of skip messages as proof (eg: 4 out of 7 skippers), they fail attestation/get slashed.

We only skip either an entire set (if the picker queue halts), or all of the shufflers in a set (if the shuffler queue halts). We never skip and replace individual nodes within a set, as this gives too much choice to the printer and therefore an unfair advantage.

As a final backstop if the skipper process fails to get things moving again, we can reschedule on the next L1 block write with a new set of roles.

[TODO diagram of a queue with the printer and skippers]


### Pickers and Picker Queue

The job of the picker queue is to create a stream of transactions grouped in chunks. It does this with minimal transaction censorship and equitable inclusion regardless of the location of the originators (eg: it reduces the advantage of running from a server in Telehouse London vs a laptop in Swansea). Transaction order doesn’t matter at this point.

Pickers work to publish chunks to the picker queue as fast as possible. To do this the picker queue runs asynchronously alongside the shuffler queue and never references it. This means that even if the shuffler queue halts, the picker queue will carry on regardless.

Before starting work on their allocated chunk, a picker must receive the previous chunk published by the printer (MsgPickerChunk).

The picker now collates the pending transactions they can see in the mempool publishing them in a message (MsgTransactionList) with the chunk number. They also include the MsgPickerChunk hash to show that their data is not stale.

The printer waits for either a MsgTransactionList from each scheduled picker, or the threshold number of skip messages from the skippers.

They then create and publish another MsgPickerChunk containing either a full set of valid MsgTransactionLists from every picker in the previous set, or a threshold number of skip messages (MsgSkip) from the skippers.

This process repeats with the next set of pickers.


### Shufflers and Shuffler Queue

The job of the shuffler queue is to order transactions fairly by randomizing their positions within each chunk. This does not mean, for example, that the entire transaction order within an eth2 block will be randomized together. Transactions will only be randomized within the latency window of a picker set, which will usually be much shorter (perhaps every 1.2 secs vs every 12 secs).

The result is to randomize order where the true order is uncertain, and to preserve order where we are sure of it. This is ideal. We have just fixed the gross inefficiencies of HFT as a side effect of decentralization.

Before starting work on their allocated chunk, a shuffler waits for the transaction data to arrive. This comes in the form of a MsgPickerChunk from the printer containing their chunk number (chunk numbers match one to one between the picker and shuffler queues, although the queues run independently).

Having received this message and having seen that every other shuffler has committed to some entropy (MsgCommit) and that their vaults have confirmed this commitment (MsgVaultCommit), the shuffler publishes a message revealing their entropy (MsgReveal). We’ve skipped a bit here, but don’t worry... we’ll come back to this. For now just know that by this time all shufflers will have committed to some entropy known only to them which they now reveal to the world.

The printer waits for either:



*   the MsgReveal from each scheduled shuffler or the MsgVaultReveals from their vaults. 
*   or the threshold number of skip messages from the skippers.

The chunk wasn't skipped so the printer now uses the chunk messages to create the final chunk. Here is the recipe for a chunk:



*   Add the transactions from each MsgTranactionsList into a single list. 
*   Combine the shuffler entropy in shuffler index order with some bitwise operations to get our final consensus entropy.
*   Sort the transactions in the list by applying this consensus entropy to the transaction hashes through a well documented RNG.
*   Cycle through the list removing duplicates (easy now it’s sorted).
*   If needed, truncate the list to fit the maximum allowable chunk size.
*   Put this combined and randomized transaction list into a final chunk message (MsgShufflerChunk) along with the current hash and the previous hash.

The printer does this to create and publish the final MsgShufflerChunk.

This differs from other block content creation processes in one vital way: _any participant can do the same and verify that the printer has respected the consensus_.

The process repeats with the next set of shufflers.

When the printer comes to propose a block, they grab all the content chunks they can fit into it to ensure the highest gas reward, starting with any that didn't make it into the previous block.

The content network may get ahead of the structural network in publishing chunks. This is good as users can begin to see the order pending transactions will be processed before they are committed to a block. It also means the system is running as fast as possible.


#### Shuffler Entropy

In the example above, our shuffler was good and ready to reveal their entropy when needed. So how did they get to this point? Well there is quite a bit they need to do. Fortunately this can all be done in advance.

So first of all, how do shufflers decide on their entropy? We could let them choose it themselves, but this raises the possibility of simple collusion.

Simple collusion requires no active network of collusion between nodes to work. A node could simply always choose an entropy of 1. Other nodes might notice it is doing this and realize that if they did the same it would weaken the randomization. Conceivably, a majority of nodes could end up sending 1 such that when they are allocated as pickers they can game the transaction order. Fortunately we can do better than this. We can force the shufflers to use the entropy available to us in their private key.

The shuffler creates a message containing the current chunk number and perhaps some other public and relatively static data. This message will be the same for everyone and has very low entropy (MsgEntropy). But once signed by the shuffler’s private key, the signature of this message has high entropy.

While MsgEntropy can be recreated by anyone using the same publicly available data, the entropy from the signature is private to each shuffler. As such, by decrypting MsgEntropy to use it’s signature as a shufflers’s entropy, we also get to validate that the shuffler used the correct plaintext MsgEntropy as the source.

Put simply, they can't choose 1 all the time because they don’t choose at all.


#### Shuffler Vaults

In order for this entropy to be truly useful, it must stay hidden until the pickers have submitted their lists and the other shufflers have committed their encrypted entropy. Once this condition is met, all the shufflers reveal their entropy and we can combine them with some bitwise operations to get our consensus entropy and randomize the transaction order. We only need one shuffler to do this honestly for it to work.

But we have a problem. In some cases the shuffler may get an advantage by withholding their reveal. This could include giving themselves a second chance at a better transaction order and statistical frontrunning (see Shuffler Withholding).

This is where vaults come in. Vault roles are also allocated by the scheduler. The shuffler uses threshold encryption (2/3 or 2/4 is probably sufficient) to split their entropy between vaults (MsgEntropySplit). In the case of 2/4, the shuffler splits their entropy so that any 2 of the 4 vaults assigned to them will be able to reveal their key if they fail to.


#### Entropy Commit Reveal

By convention, vaults 0 and 1 will always reveal under the same conditions as the shuffler. This is so they don’t increase latency by waiting until the shuffler has failed to reveal first. It also allows everyone to validate that the shuffler sent the vaults a valid split of their entropy key.

If those vaults are slow to reveal, the others can begin to reveal instead. By not revealing more than is immediately necessary we reduce network messages.

To prevent unnecessary halting when vaults become unavailable, shufflers only need to wait for one pair of confirmation messages out of all the vaults (MsgVaultCommit) along with the other shuffler MsgCommits before revealing.

This all seems like a lot of work, especially as the shuffler must communicate with it's vaults privately to send them their key split (MsgEntropySplit). But remember that everything can be done in advance right up to the point of revealing. This means that it does not increase latency.

[TODO if time- diagram showing the shuffler protocol and how it isn’t held back by commit preparations]


### Skipping 

Although the picker and shuffler queues run asynchronously, the chunks they operate on match one to one as determined by the scheduler. 

The only exception to this one to one rule is when a shuffler set is skipped. This can only be done when the threshold of skip shuffler messages is sent for that chunk (MsgSkip). Because this is decided by consensus, it is low risk.

In this case the printer must use the next set to shuffle both it’s chunk and the previously skipped chunk and publish both.

If the next set is also skipped, then the printer must use the set after that to shuffle both it’s chunk and the two previously skipped chunks and publish all three...and so on.

In this way we can be sure that the shuffler queue will always catch up with the picker queue, even if the latency goes up temporarily.

As well as for simplicity, the reason for this one to one relationship is that if they were scheduled independently it might create some incentive for a role to withhold. They could try and skip to a combination of colluding picker and shufflers sets, although this would be very difficult in practice.


### Validation 

So what happens when roles misbehave and how can we prove that they have?

Validation of the network relies heavily on the cryptographic principle of non-repudiation: that you can prove when more than one signed message was sent from the same address.

To understand the significance of this, let's imagine that Alice sends some entropy that creates a transaction order in a block that she doesn't like. Tough luck. If she sends a second message to try and reorder it, this will be detected and she can be penalized.

Secondly, because the protocol defines exactly how a chunk should be formed given a certain set of messages, we can always validate that the printer did this correctly and without manipulation.

If a violation is simply that a role is not scheduled to send a message for a certain chunk, or a printer for a certain block, we can simply ignore the message and not relay it. This also protects us against DDOS attacks.

Anyone with visibility of the network can validate the active participants in this way. If necessary, they can submit fraud proofs for slashing at a later date on the L1 network where slashable deposits are kept for each participant.

It is hoped that fraud proofs will be calculable deterministically in the EVM. Where this proves difficult, a slasher voting system can be used instead. This is beyond the scope of this initial document.

See Validation Rules And Proofs.


### Withholding

So we can validate messages just fine, but if someone doesn't send a message when they are meant to, this is of no help. If a node withholds a message, we have nothing to validate cryptographically or otherwise.

It is important to remove any incentive for roles to withhold a scheduled message intentionally because it harms us twice: 



*   it gives an unfair advantage to the withholding attacker. 
*   it slows down the network for everyone. 

So let’s take each case in turn:


#### Picker Withholding

Pickers have no incentive to withhold. They have the power to add whatever transactions they like and they stand to lose that power if they delay and are skipped. This is true even if they are colluding with the printer.

Take a situation where we have two pickers, honest Alice (A) and bad Bob (B). B is colluding with the printer.

A publishes her list.

B delays causing the set to be skipped.

The colluding printer then waits for B to reveal their list (they’re one and the same so they probably have it already).

The printer now has a choice, they can publish block AB or they can skip the entire block.

But they gain nothing from skipping, the next block will likely contain all of the transactions they just skipped and new transactions with better information, and they have just given up all control over the chunk content.


#### Shuffler Withholding

Shufflers do have incentive to withhold (if we don’t mitigate it).

If they wait until all other shufflers have revealed, they will be the first to know the final transaction order for that chunk (although still not the content of the entire block). This gives them two lines of attack:

_Second Chance_

If they don’t like where their transaction is in the chunk, they can withhold forcing the set to skip and giving themselves another chance at a more favourable order. It is trivial to calculate whether doing this has a positive expectation for them.

_Statistical Frontrunning_

Imagine we don’t have distinct picker and shuffler queues and have one single queue instead. The shuffler sees a transaction they’d like to frontrun. They add a transaction to the mempool and then delay until their set is skipped, hoping the next picker will add both it and the previously skipped transactions. 

This is far from risk free for them. Other transactions may also enter the pool which make conditions less favourable for them and the order of all transactions in the chunk is randomized.

It also only works if adding the transaction has a positive expectation. A Uniswap frontrun for example wouldn’t be exploitable in this way. Let’s say they are ahead of the victim transaction and win $10. Now let’s say they are behind the victim transaction, they will lose $-10, so their expected win is $0 and not worth doing.

But if the victim transaction aims to take an offer in an order book, they may win $10 if they beat the victim transaction and lose nothing if they fail to. In this case their expected win is $5 so it is worth doing.

No matter, neither of these attacks are possible in the system:

_Second Chances_ are mitigated by the vaults who will reveal the shuffler’s entropy if they don’t. This makes any attempt to withhold ineffective.

_Statistical Frontrunning_ is mitigated by having asynchronous picker and shuffler queues. By the time the shuffler has the chance to withhold, the picker queue has already moved on and the shuffler has no chance to add their transaction into the current chunk.


#### Vault Withholding 

Vaults do not have an incentive to withhold. They do not have a full entropy key and so do not know the final block order until it's too late for them to have an advantage.

Also even if they do withhold, the other vaults will likely not.


#### Skipper Withholding 

Skippers only have the power to withhold when one or both queues have halted. This is always due to another role going offline or withholding. Let’s look at what they might stand to gain from not helping to restart each queue, and let’s use this as an opportunity to examine the more general possibility of _latency attacks_:


##### Shuffler Queue

If the shuffler queue halts, the picker queue will carry on. This is by design as it stops an attacker halting the system to add their transactions in.

Take the situation where a skipper (or more likely a shuffler) delays a pairs market by halting the shuffler queue hoping to be able to exploit it by arbing against the delayed price later.

They withhold their reveal and then add a new transaction into the pool to execute against the arb they just created. But because the picker queue is still running, their transaction will be added into a later chunk than the one they are aiming for. It is impossible for them to add their transaction into the current chunk, as the transactions have already been finalized. They are delaying themselves as much as everyone else.


##### Picker Queue

If the picker queue halts, the shuffler queue will catch up with it, and then also halt. 

Take the situation where a skipper (or a picker) delays a pairs market by halting the picker queue hoping to be able to exploit it by arbing against the delayed price later.

This is of no possible value to a picker as they already have full control of what transactions they put into the queue at that moment. They only stand to lose that control if they delay and get skipped.

There is perhaps a very slight advantage for the skipper, but it is negligible. They could try to delay the picker (who by definition is already delaying themselves for no possible gain) by allowing them to continue to withhold so that the skipper can add their transaction in.

When you take into account the fact that it only takes a subset of skippers (eg: 4 out of 7) to skip the set and that the picker has no advantage from delaying and will likely send their message soon, this slight advantage all but disappears. 


#### Printer Withholding

The printer can choose to truncate the content (stop before the end), or even not write any at all (empty block) without failing attestation/being slashed. But this will only delay the inevitable as the next printer will pick up from where they left off. In that situation the printer loses out on the gas fees from the transactions and gains nothing. 

The same is true of not publishing picker and shuffler chunks when required. If they stop doing this, the next printer will just pick up from where they left off.

So printers have no incentive to withhold.


### Transaction Reordering

Transaction reordering attacks are mitigated by ordering at random. This is the only equitable strategy as exact timestamps are unknowable in a fast moving globally distributed system. As long as one shuffler acts honestly, **transaction reordering attacks are mitigated**.

Even if all shufflers are dishonest, there is little that they can do. They cannot change the transaction order by picking their entropy, because it is derived for them from their private key. They cannot alter the list of transactions they are randomizing because that has already been finalized by the pickers.


### Transaction Censorship 

As long as one picker is honest and includes all or most of the transactions they can see in the mempool, then** transaction censorship is mitigated**. 

Even if all pickers are dishonest, as long as they have competing interests (are not colluding) they will likely select different transactions and transaction censorship may still be mitigated.

As well as this, the set of pickers changes each chunk, not just every block. This means that a single blockchain block will usually represent the combined view of many sets of pickers, not just one set, and certainly not the interests of just one miner.


### Latency Window

The picker queue runs as fast as possible, but the printer needs to wait for each picker to return their transaction lists before they can publish a chunk. This does not negatively impact the throughput of the system, but it does add some latency, and this is a good thing. Let me explain.

Our pickers are distributed across the world. One might be in New York, another in Tokyo and another in Melbourne. One cycle of the picker queue may end up taking something like 1.2 secs to complete.

In the time it takes to complete a cycle, the next set of pickers will be collecting pending transactions. When they see the printer signal that a chunk is completed, they build and submit their lists.

As described earlier, within that latency window all transactions will be statistically simultaneous. This means that on average one transaction is no more or less likely to have priority over any other in the chunk. In any individual chunk they will, but statistically they will not.

It is important to note that we are not losing information here. The true information about when each transaction was initiated is unknowable within the latency window. It is impossible to tell whether Alice in Melbourne sent her order before Bob in New York. Even if we could order by arrival time the way that a centralized authority like Nasdaq does, that does not represent the reality of when those transactions were initiated.

Because the pickers change each cycle and are distributed globally there is no centralized exchange to position yourself close to. The best advantage available in terms of infrastructure spend is to make sure you have a PoP in all major internet hubs and good access to the pool.

This is a huge deal. These two facts mean that the whole messy inefficiency of HFT is dispensed with. Billions of dollars no longer need to be spent digging up the countryside to get sub millisecond latency advantages at zero social benefit. Users will access applications and markets equitably wherever they are in the world and however well or under resourced they are. HFTs are notoriously high volume too, so the transaction rate will fall.


### Collusion Protection

We only need one honest picker and one honest shuffler per set for the system to work perfectly. Although not essential, we can use this to our advantage by having trusted centralized system pickers/shufflers scheduled in every chunk.

If our trust is misplaced and they are not honest, it would not harm the system as we still have the distributed pickers and shufflers. If they are honest, it will ensure that there is always at least one honest view of the transaction pool and some honest entropy, which is all we need even if all the others are colluding.

This is a powerful protection against a collusion network ever getting off the ground. The presence of these system actors is a huge disincentive to develop the complex and expensive systems required to collude against the content layer. For example Uniswap themselves could run a system picker and shuffler in a Uniswap L2.

This is quite significant. Having limited the power of centralized authorities to harm the system, we can now use their reputational collateral to our benefit without risk. 

While the distributed pickers and shufflers ideally change from block to block, the centralized system pickers and shufflers remain relatively constant, changing slowly over time according to some much longer term process.

This extra random element may mean we need fewer distributed pickers and shufflers in the main set which decreases halting, although we now have the risk of halting from the centralized parties.

We may have a partially distributed system picker which combines transaction lists from many light volunteer nodes into a single picker list. This would be a good way of involving large numbers of interested parties that are not willing or able to risk a slashable deposit.


### Scheduler 

Most secure to allocate different roles each chunk.

In some cases network usage goes up.

Roles where this is the case we may want to reuse within an L1 block.

These are shufflers, vaults and skippers.

Pickers can change each time as there is no saving of preparatory work.

Skippers do not need to change every chunk, but they probably should every block to avoid getting stuck for several blocks. You might require 4 skip messages out of 7 skippers to skip a set.

We could reduce network usage by scheduling the same shuffler set n times rather than once. In this case skipping a shuffler set will mean skipping to the next unique set.

Add schedule ids, then when the schedule changes we don't lose printed chunks from the previous schedule. PROBLEM what's to stop nodes faking old messages. Printer signing for one thing. How about for rollups? Printer address must change each time I think. 

Printed picker and shuffler chunks must be preserved between blocks and schedules.

Content must persist between structural blocks. So if a printer does not write all of the shuffled content in their block, the next printer must pick up from where they left off. 

If this is not enforced, then printers can give themselves a second roll of the dice if they don’t like the content as the pickers and shufflers may create it differently next time.

TODO  as simple as not resetting chunk numbers, although the time taken to calculate the schedule will increase.

TODO the transition between printers must be seamless or we could get confusion and disagreement about who has the authority to propose chunks. Because this is specific to each structural layer, we will save a discussion of this for the eth2 and optimistic rollups integration documents to follow.

TODO VIP finish this


### Skipper Delay Line

One variation of the skipper process is a skipper delay line. If skippers start abusing their power by sending skip messages without giving shufflers and pickers a fair chance to respond, we can switch to using a cryptographic delay line.

In this case, rather than skippers sending their messages independently of each other, the first skipper initiates a chain of skipper messages. It works like this:

Skipper 0 decides a set should be skipped and sends a skip message.

Skipper 1 sees their message, and only if they agree, sends a skip confirmation containing skipper 0’s message

Skipper 2 sees the skipper 1’s skip confirmation, and only if they agree, sends a skip confirmation containing skipper 1’s message

Skipper 3 is the final skipper. If they agree they send a skip confirmation containing skipper 2’s message.

As skipper 3 is the final skipper, only it’s skip message can grant the printer the authority to skip the set.

We have still achieved a consensus on skipping the set, but with this process we have also ensured that this takes at least 3 latency windows (perhaps around 4 seconds) giving the shufflers and pickers a fair chance to respond.

There may have to be several skipper delay lines in this case, as the chance of reaching a consensus to skip in series (skip threshold = skipper count) is usually smaller than in parallel (skip threshold &lt;= skipper count). Also the delay line itself can now halt, so for now we will let them work in parallel instead.


### Penalties

Any misbehaviour which creates a choice for the printer must be slashed as heavily as a printer ignoring the consensus and writing their own transactions. This is because a printer could collude with a shuffler or picker to create a choice for them for a small penalty, and then exploit that choice when they publish the content.

In general:



*   If a violation is not severe (eg: sending a message for a chunk you are not scheduled for), we will simply ignore the message. This also mitigates many DDOS attacks. 
*   If a violation will lead to bad content, we can invalidate the chunk or block. 
*   If a violation is a suspected or outright attempt at manipulation, we can slash the perpetrator.

Setting penalties will require detailed and careful analysis and modelling beyond the scope of this initial document.


### Rewards

Rewards will come in the form of L1 payments made to participants. Rewards must be good enough to warrant the cost of running a node and to incentivize good node availability. Beyond this, there are no incentives to withhold so rewards may be low.

To participate in fraud proofs a node will need to keep messages for at least the length of time it takes to persist the content to L1. Nodes will be encouraged to run L1 clients to give them an independent view of the mempool which may require higher rewards.

Setting the exact rewards requires detailed and careful analysis and modelling beyond the scope of this document.


### Recovery From Violations 

While it can be shown that violations of the protocol can be punished, it is also important to show that the system can recover from such violations with as little impact as possible.

This will need to be considered in later documents or later versions of this document.


## Protocol Tables


### Validation Rules And Proofs


<table>
  <tr>
   <td><strong>rules</strong>
   </td>
   <td><strong>proofs</strong>
   </td>
  </tr>
  <tr>
   <td>Ensure that message count rules per role are enforced.
<p>
For each scheduled role and their scheduled chunk number, there must be:
<p>
Max 1 MsgTransactionList per picker.
<p>
Max 1 MsgEntropy, MsgCommit and MsgReveal per shuffler.
<p>
Max 1 MsgEntropySplit per shuffler/vault combination..
<p>
Max 1 MsgVaultCommit, MsgVaultReveal per vault.
<p>
Max 1 MsgSkip per skipper.
<p>
Max 1 MsgPickerChunk per printer.
<p>
Max 1 MsgShufflerChunk per printer.
   </td>
   <td>Submit a fraud proof containing 2 of the same message type from the same address with the same chunk num.
   </td>
  </tr>
  <tr>
   <td>Ensure that the picker’s list is not stale.
   </td>
   <td>Submit a fraud proof containing a MsgTransactionList with an old picker chunk hash.
   </td>
  </tr>
  <tr>
   <td>Ensure that the shufflers entropy is valid.
   </td>
   <td>Submit MsgCommit and MsgReveal/MsgVaultReveals showing that MsgCommit contains an invalid plaintext MsgEntropy, or that the reveal(s) fail to decrypt the MsgCommit.
   </td>
  </tr>
  <tr>
   <td>Ensure that the shufflers split their key correctly.
   </td>
   <td>Submit all MsgEntropySplit for a MsgEntropy showing that you cannot derive MsgEntropy from a full set of MsgEntropySplit messages.
   </td>
  </tr>
  <tr>
   <td>Ensure that the printer formed the MsgPickerChunk message correctly.
   </td>
   <td>Show that the MsgPickerChunk contains missing or invalid MsgTransactionList/MsgSkip messages.
<p>
(if the hash or previous hash is invalid the message can just be ignored)
   </td>
  </tr>
  <tr>
   <td>Ensure that the printer formed the MsgShufflerChunk message correctly.
   </td>
   <td>Show that the MsgShufflerChunk contains an invalid list of ordered transactions or MsgSkip messages.
<p>
(if the hash or previous hash is invalid the message can just be ignored)
   </td>
  </tr>
  <tr>
   <td>Ensure that the printer included only and all of the picker’s transactions.
   </td>
   <td>Submit the relevant MsgShufflerChunk and MsgTransactionList messages showing that the printer added or dropped transactions in MsgShufflerChunk.
<p>
Note: the printer has a line of defence against getting slashed for sending an invalid chunk if they can prove that another role broke the rules when contributing to the chunk.
<p>
This avoids roles getting the printer slashed (albeit along with themselves) by withholding messages and later submitting them.
<p>
For example, in the case of a scheduled role submitting more than one expected message for a chunk, this is the primary violation that must be slashed. 
<p>
If the printer sees this, they can either halt until they are skipped (preferred) or choose one of the messages in this case.
<p>
The printer may submit their defence even if the first they know of it is when a fraud proof is submitted against them. To do this they must watch the L1 slashing contract closely. 
<p>
For this reason, they would do better just to wait for the set to be skipped if they detect duplicate/invalid role messages.
<p>
Also note, in many cases violations are not slashed, blocks are simply not attested.
   </td>
  </tr>
  <tr>
   <td>Ensure that the printer used messages only from the scheduled roles allocated to this chunk.
   </td>
   <td>Similar to the above (and with the same printer defence) but submitting the relevant MsgShufflerChunk, MsgTransactionList, MsgCommit, MsgVaultCommit, MsgReveal and MsgVaultReveal messages that contributed to the block. 
<p>
The chunk can then be re-created from these messages using the protocol rules and compared to the printer’s MsgShufflerChunk, showing that the printer diverged from the consensus.
   </td>
  </tr>
  <tr>
   <td>Ensure that the printer did not use their own entropy.
   </td>
   <td>As above.
   </td>
  </tr>
  <tr>
   <td>Ensure that the printer applied the entropy correctly.
   </td>
   <td>As above.
   </td>
  </tr>
  <tr>
   <td>Ensure that the printer de-duplicated the randomized transaction list into the final chunk correctly.
   </td>
   <td>As above.
   </td>
  </tr>
  <tr>
   <td>Ensure that the printer did not truncate the proposed block more than needed to fit it into the final chunk (ie: that they didn’t censor transactions from the end of the chunk)
   </td>
   <td>As above. The maximum block size for the network will be known globally so it can be shown that the final MsgShufflerChunk contains fewer transactions than it needs to.
   </td>
  </tr>
  <tr>
   <td>Ensure that the printer used contiguous chunk content between blocks
   </td>
   <td>Submit the printer’s block hash and the previous block hash showing that the printer skipped chunk numbers between them. 
   </td>
  </tr>
  <tr>
   <td>Ensure that the printer used contiguous chunk content within their block
   </td>
   <td>Submit the printer’s block hash and the relevant range of MsgShufferChunk messages showing that the printer failed to include or order them correctly
   </td>
  </tr>
  <tr>
   <td>Ensure that the printer used only valid chunk content within their block
   </td>
   <td>Submit the printer’s block hash and the relevant range of MsgShufferChunk messages showing that the printer must have included chunks not created by the consensus.
   </td>
  </tr>
</table>



### Message Types

Message types used in communication between roles and chunk building.


<table>
  <tr>
   <td><strong>message type</strong>
   </td>
   <td><strong>fields</strong>
   </td>
  </tr>
  <tr>
   <td>MsgTransactionList
   </td>
   <td>chunknum
<p>
previous picker chunk hash
<p>
txn0
<p>
txn1...
   </td>
  </tr>
  <tr>
   <td>MsgEntropy
   </td>
   <td>chunknum
<p>
some other slow changing public data
   </td>
  </tr>
  <tr>
   <td>MsgEntropySplit 
   </td>
   <td>chunknum
<p>
threshold encrypted split of a shuffler's MsgEntropy
   </td>
  </tr>
  <tr>
   <td>MsgCommit 
   </td>
   <td>chunknum
<p>
encrypted MsgEntropy
   </td>
  </tr>
  <tr>
   <td>MsgReveal 
   </td>
   <td>chunknum
<p>
key to the encrypted MsgEntropy in MsgCommit
   </td>
  </tr>
  <tr>
   <td>MsgVaultCommit 
   </td>
   <td>chunknum
<p>
encrypted split of shuffler’s MsgEntropy
   </td>
  </tr>
  <tr>
   <td>MsgVaultReveal 
   </td>
   <td>chunknum
<p>
key to encrypted split of shuffler’s MsgEntropy
   </td>
  </tr>
  <tr>
   <td>MsgSkip 
   </td>
   <td>chunknum
<p>
last picker chunk hash
<p>
last shuffler chunk hash
<p>
skip (0=pickers+shufflers,1=shufflers only)
   </td>
  </tr>
  <tr>
   <td>MsgPickerChunk 
   </td>
   <td>chunknum
<p>
hash
<p>
previous hash
<p>
isSkip
<p>
MsgTransactionList/MsgSkip 0
<p>
MsgTransactionList/MsgSkip 1...
   </td>
  </tr>
  <tr>
   <td>MsgShufflerChunk 
   </td>
   <td>chunknum
<p>
hash
<p>
previous hash
<p>
isSkip
<p>
ordered transactions/MsgSkip 0
<p>
ordered transactions/MsgSkip 1...
   </td>
  </tr>
</table>



### Parameters For Optimization

Parameters to be tested and configured for different instances of the content consensus network.


<table>
  <tr>
   <td><strong>parameter</strong>
   </td>
   <td><strong>sensible default</strong>
   </td>
  </tr>
  <tr>
   <td>picker count per set
   </td>
   <td>3 + 1 system picker
   </td>
  </tr>
  <tr>
   <td>shuffler count per set
   </td>
   <td>3 + 1 system shuffler
   </td>
  </tr>
  <tr>
   <td>{t,n} values for skipper
<p>
t=(n/2)+1
   </td>
   <td>4/7
   </td>
  </tr>
  <tr>
   <td>{t,n} values for vault threshold encryption 
<p>
(n=vault count per set)
   </td>
   <td>2/4
   </td>
  </tr>
</table>



## Conclusion 

We have identified problematic centralization in the Ethereum network, and then proposed a solution to it. 

The solution eradicates the vast majority of Miner Extractable Value by ensuring fair transaction inclusion and ordering. It also mitigates the worst excesses of High Frequency Trading (HFT), ensuring that as traditional finance moves to Ethereum, these negatives do not follow it.

We have done this in a way that works across many Ethereum networks, including eth2, rollups and mainnet (although fork risk is likely too great here).

We see the creation of a content consensus layer as the realization of Ethereum’s ambitions for full decentralization as well as it's ambitions to create fair and equitable globally distributed systems for everybody.

The next revisions of this document will focus on the specifics of integrating the protocol with eth2 and Optimistic rollups.
