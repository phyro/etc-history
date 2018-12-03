# Struggle - The DAO nightmare

As mentioned, Slock.It was wrong about the security of their DAO contract. Only five days after the [blog post](https://blog.slock.it/no-dao-funds-at-risk-following-the-ethereum-smart-contract-recursive-call-bug-discovery-29f482d348b) [[1]](https://web.archive.org/web/20180823103440/https://blog.slock.it/no-dao-funds-at-risk-following-the-ethereum-smart-contract-recursive-call-bug-discovery-29f482d348b?gi=38eed1977174) [[2]](http://archive.is/fmYHI), a user called *ledgerwatch* makes a [post on reddit](https://www.reddit.com/r/ethereum/comments/4oi2ta/i_think_thedao_is_getting_drained_right_now/) [[1]](https://web.archive.org/web/20181202194923/https://www.reddit.com/r/ethereum/comments/4oi2ta/i_think_thedao_is_getting_drained_right_now/) [[2]](http://archive.is/byj7G) with a very concerning title `I think TheDAO is getting drained right now`.

That's right, the DAO contract has fallen victim to the `recursive call bug`. The attacker - often referenced as `The DAO hacker` - stole
around 4 million Ether which was valued close to 50 million dollars at the time, making it one of the biggest hacks in the history of cryptocurrencies.
One important detail was that the hacker didn't yet get the Ether to his wallet address yet, it was still stored inside the contract. The hacked funds
were locked up in a child DAO for 28 days, meaning he would only be able to get them transfered to his address after.


## Another DAO hacker strikes - this time a White Hat
The total amount of Ether in the DAO contract was 11.5 million, so another 7 million Ether remained that was still subject to being hacked and so some
group of hackers formed Robin Hood Group (RGH) and decided to drain the rest of the DAO contract and thus prevent the DAO hacker from stealing everything.
They successfully replicated the attack that the original DAO hacker used to steal the funds and stole the remaining 7 million Ether in the DAO contract.
With this, we got ourselves another DAO hacker that was a group of white hat hackers that claimed they will return the money to the rightful owners afterwards.


## Rescuing funds by forking... wait, what?
Despite first stating that we should never intervene and bail out people, Ethereum Foundation saw an opportunity to save the money and started thinking
of possible options to save the situation. Even though it's clear that the contract was not used with the original intent of the contract authors, changing the history goes against the original promise of `Code is Law` mentality. Arguably it even breaks the whole promise of having an immutable ledger.

Even Vitalik Buterin himself said that reversing history destroys the whole idea:
> With history reversion (ie. 51% attacks), it's clear why carrying out such an attack would destroy the ecosystem: it undermines literally the only guarantee that makes blockchains a single bit more useful than BitTorrent.

It's not hard to agree with his statement. If your actions can be reversed, then it makes the whole blockchain concept useless. It goes against everything
Bitcoin was fighting for. Bitcoin could have reversed many times due to exchanges being hacked where the core protocol developers lost money, but
they knew they would destroy the whole idea and so they kept the ledger immutable. Ethereum Foundation pushing a plan for a bailout divided the community
on forums in two parts, those that wanted to intervene and those that said nobody can intervene if the protocol was functioning correctly.

An important thing to note here is that there was nothing wrong with the Ethereum network itself, the flaw was scoped to the DAO smart contract.


## A plan to rescue the funds

Vitalik Buterin first proposed a Soft fork solution which was soon found out to not be a good enough due to
[a potential DOS vector](http://hackingdistributed.com/2016/06/28/ethereum-soft-fork-dos-vector/) [[1]](https://web.archive.org/web/20181003135340/http://hackingdistributed.com/2016/06/28/ethereum-soft-fork-dos-vector/) [[2]](http://archive.is/y8qEd).

Ethereum Foundation started planning a Hard fork. The Hard fork would make an **irregular state change** that would return the funds taken
by the DAO hacker. Many of course found this to be a controversial move and thought that the developers should not play judges, otherwise
you show that you have in fact a centralized entity that can push such things through.


### A last minute decision

Ethereum Foundation announced that `Carbon vote` will play the role what will be the default setting for node clients. This was an unofficial voting system
that was first said to not play any role in the decision. This reversal of the decision was announced ~12 hours before the voting was over. There's quite a few strange things here:
1. Historically Hard forks have been **opt-in** meaning that you had to explicitly show you support the hard fork by changing a setting. Instead
here we had a different situation. The DAO bailout was the default option and if you were against it, you had to **opt-out** and run your client
with the `--oppose-dao-fork` flag. You can imagine that many people simply didn't care about changing it.
2. 12 hour notice means half of the world was sleeping or working.
3. People that had money in the DAO contract had higher incentive to vote - bailing themselves out


[The Carbon Vote results](http://v1.carbonvote.com/) [[1]](https://web.archive.org/web/20181107050846/http://v1.carbonvote.com/) [[2]](http://archive.is/wlmuf) showed that ~85% voted for Hard fork and ~15% against it.
From the results we can observe that:
1. Less than 6% Ether holders voted
2. One single address voted with so much Ether that it amounted to 25% of all votes - remember, there was a 72 million premine

Having this many flaws and breaking the social contract of immutability for the rest of the 95% that didn't even cast a vote - out of which many
couldn't due to the 12 hour notice - didn't make sense for many but Ethereum Foundation decided to continue with the results from the vote and
implemented the DAO hard fork in their clients. The DAO hard fork was set to trigger at block 1920000 which was mined on July 20th 2016.


## The Hard fork implementation

What does `irregular state change` mean? Under the hood, the wallet balances are stored in a database. Moving money to some other wallet always requires
signing a transaction with that wallets Private Key. This can't be done for the case of the DAO contract because it is locked in a contract and
once the 28 days pass, the hacker will have the money on his own wallet for which only he knows the Private Key.

The magic here is that the developers (people who write Ethereum client code) have super powers. It's pretty easy to see what can be done. If you've ever written a `SQL` statement you should be able to understand this:
`UPDATE wallets SET balance = 0 where id = DAO_HACKER_ID; UPDATE wallets SET balance = 4000000 WHERE id = MY_NEW_WALLET_ID'`.
It essentially erases the balance of the DAO hacker wallet directly from the database and increases the amount of money (again directly in the database)
of some wallet where we want the money. The issue with this solution is that we skipped the Private Keys altogether. Hooray. Am I really my own bank?
Clearly not.

#### Below is the source code of the actual implementation of the go-ethereum client:

Call a function for custom state changes if the block is the DAO hard fork block  - [https://github.com/ethereum/go-ethereum/blob/master/core/state_processor.go](https://github.com/ethereum/go-ethereum/blob/cf33d8b83ce78d1e79cd8c43a21070b2050d5c7e/core/state_processor.go#L64-L67)
```go
// Mutate the block and state according to any hard-fork specs
if p.config.DAOForkSupport && p.config.DAOForkBlock != nil && p.config.DAOForkBlock.Cmp(block.Number()) == 0 {
    misc.ApplyDAOHardFork(statedb)
}
```

Takes the money from predefined addresses and moves it to some other account - [https://github.com/ethereum/go-ethereum/blob/master/consensus/misc/dao.go](https://github.com/ethereum/go-ethereum/blob/061889d4ea13b23d777efbe005210ead8667e869/consensus/misc/dao.go#L71-L85)
```go
// ApplyDAOHardFork modifies the state database according to the DAO hard-fork
// rules, transferring all balances of a set of DAO accounts to a single refund
// contract.
func ApplyDAOHardFork(statedb *state.StateDB) {
	// Retrieve the contract to refund balances into
	if !statedb.Exist(params.DAORefundContract) {
		statedb.CreateAccount(params.DAORefundContract)
	}

	// Move every DAO account and extra-balance account funds into the refund contract
	for _, addr := range params.DAODrainList() {
		statedb.AddBalance(params.DAORefundContract, statedb.GetBalance(addr))
		statedb.SetBalance(addr, new(big.Int))
	}
}
```
For those of you that are not familiar with the code these two calls are important:
```go
statedb.AddBalance(params.DAORefundContract, statedb.GetBalance(addr)) // makes money appear magically at DAO Refund Contract address
statedb.SetBalance(addr, new(big.Int)) // sets the balance of account stored in variable 'addr' to 0.
```


One issue that came with this was the fact that the Ethereum Foundation was sure that the old chain would die, and they didn't make all the
necessary things to avoid possible issues in case the old chain survives. Having two chains with the same signing of transactions meant
that one could do a [replay attack on the other chain](https://vessenes.com/hard-fork-and-replay-concerns/) [[1]](https://web.archive.org/web/20180909063418/https://vessenes.com/hard-fork-and-replay-concerns/) [[2]](http://archive.is/F9uCt)

## Motivations for interventions

One thing to note is that some members of the Slock.It team were ex Ethereum Foundation members, including Slock.It CEO Stephan Tual.
This makes it suspicious, no? I mean, who wouldn't bail out his friends?

There were many that did not agree with the idea of a Hard Fork, but every one thought the old untampered chain would die.


## A new hope

An anonymous reddit user **whatisgravity** announces that he is working on restoring the client without the Hard Fork.
[He starts encouraging others](https://github.com/ethereumproject/go-ethereum/issues/1) [[1]](https://web.archive.org/web/20170312071416/https://github.com/ethereumproject/go-ethereum/issues/1) [[2]](http://archive.is/Ris2t) to contribute their free time in keeping the
non modified version of the chain alive. Many volunteers decided to contribute including the ETCDEV founder Igor 'splix' Artamonov,
Arvicco, Charles Hoskinson, Cody 'DontPanicBurns' Burns and Elaine Ou. Whatisgravity restored the old version of the client and the non modified chain
was up and running supported by the nodes from the volunteers in the community that felt the hard fork betrayed their belief in the system.
At this point in time, nobody was thinking about the name of the unmodified chain, it was simply Ethereum for everyone in the community. In fact
it was actually closer to the Ethereum definition than the forked chain from Ethereum Foundation. Even though there were people supporting the unmodified
chain, everyone still thought it will die because:
1. Ethereum Foundation supported DAO hard fork,
2. volunteers had no funding resources so it had to be done in their spare time
3. the fact that you could replay the transactions between the chains wasn't helping the chain survive

Things were not looking good until a few days later when Poloniex exchange announced they will be listing the unmodified chain under the
ticker **ETC** and the Ethereum Classic name was born. For every Ether owned on the forked chain (Ethereum) you get an Ether on the non forked
chain (ETC).

The announcement was great news for ETC, but soon [a skype leak](https://busy.org/@thedailysteem/leaked-ethereum-foundation-skype-chat) [[1]](https://web.archive.org/web/20181203201841/https://busy.org/@thedailysteem/leaked-ethereum-foundation-skype-chat) [[2]](https://archive.is/4v1cw)
appeared on forums showing some of the Ethereum Foundation members talking about bringing the price to 0$ by selling their share of Ether.
Ethereum Classic price first started rising reaching 45% of the price of ETH. However during the course of the next week it feel to only 10%. Apart from individuals from Ethereum Foundation dumping their Ether to get the 'free money' or bring the price to 0$, the DAO hacker also tried sending some ETC to
exchanges and successfully sold around 100.000 ETC. However, this wasn't the main reason for the price dump. There was another thing that played a HUGE role in crashing the price. The so called white hat hackers *RHG* who hacked the DAO contract in the same way the original DAO hacker did still had 7 million ETC.
[The white hats dumped 1 million](https://medium.com/@WhalePanda/ethereum-chain-of-liars-thieves-b04aaa0762cb) [[1]](https://web.archive.org/web/20181107050849/https://medium.com/@WhalePanda/ethereum-chain-of-liars-thieves-b04aaa0762cb) [[2]](https://archive.is/l9oNL) (yes, it was literally a million) ETC in 1 week which crashed the price of ETC for good. At that point in time this amounted to more than 1% of all ETC in circulation. Regardless whether their
plan was to get rich or not, one thing is clear, they stole the money from the contract in the same way the original DAO hacker did and sold much more
on exchanges. This makes their actions 10 times worse compared to the original DAO hacker who sold only around 100.000 ETC.


So to summarize, the events **after** the DAO fork were also a mess in many ways because:
1. Some of the Ethereum Foundation members tried to bring the value to 0$ by dumping ETC on exchanges
2. The white hat DAO hacker group dumping 1 million ETC on an exchange
3. Hacker sending money to the exchanges


It does not end here. On July 28th - 8 days **AFTER** the DAO fork - a suspicious activity was happening with the DAO contract which was noticed by
one of the Ethereum reddit community members called *DeviateFish_*.
[You can read all about it here](https://www.reddit.com/r/ethereum/comments/805gcn/vitalik_said_doing_rescue_forks_in_exceptional/duveapx/?context=3) [[1]](https://web.archive.org/web/20180226224909/https://www.reddit.com/r/ethereum/comments/805gcn/vitalik_said_doing_rescue_forks_in_exceptional/duveapx/?context=3) [[2]](https://archive.is/idEAG) but to put it short
someone sent a lot of Ether to the DAO contract *AFTER* the DAO fork and they used another vulnerability in the DAO contract to get the funds out. This same vulnerability could have been used to steal the money back from the original DAO hacker meaning that no fork was ever necessary. And some people from
the Robin Hood Group seem to have known that. *DeviateFish_* also wrote an explanation of the vulnerability and why he thinks
[the DAO contract was engineered for failure](https://gist.github.com/DeviateFish/6035257f3424a8ea00e83447eaaa2f90#the-token-tainting-exploit) [[1]](https://web.archive.org/web/20181203202326/https://gist.github.com/DeviateFish/6035257f3424a8ea00e83447eaaa2f90) [[2]](http://archive.is/wGJfe).


## Summary
1. There were two DAO hackers - along with the original DAO hacker there was a group of white hat hackers hacked the DAO by exploiting the same vulnerability as the original DAO hacker and later doing more damage to ETC than DAO hacker to this date
2. Ethereum decided to hard fork and bail out DAO investors based on a very poor voting result with 12 hour notice for voting
3. The DAO hard fork did not revert the transactions from the hacker. It deleted account balances from around 100 wallets and increased some other wallet balances, doing all that without private keys of the wallets which goes against everything blockchain fight for and breaks its most important rule **You are your own bank**
4. It seems that it was actually possible to rescue the hacked funds without the fork due to another vulnerability in the DAO contract that was known to at least some RHG members

[Continue to next chapter](recovery.md)


## Resources

[I think TheDAO is getting drained right now - ledgerwatch](https://www.reddit.com/r/ethereum/comments/4oi2ta/i_think_thedao_is_getting_drained_right_now/) [[1]](https://web.archive.org/web/20181202194923/https://www.reddit.com/r/ethereum/comments/4oi2ta/i_think_thedao_is_getting_drained_right_now/) [[2]](http://archive.is/byj7G)

[Stick a fork in Ethereum - Elaine Ou](https://elaineou.com/2016/07/18/stick-a-fork-in-ethereum/) [[1]](https://web.archive.org/web/20181126234338/https://elaineou.com/2016/07/18/stick-a-fork-in-ethereum/) [[2]](http://archive.is/oegkX)

[Hard fork and replay concerns - Peter Vessenes](https://vessenes.com/hard-fork-and-replay-concerns/) [[1]](https://web.archive.org/web/20180909063418/https://vessenes.com/hard-fork-and-replay-concerns/) [[2]](http://archive.is/F9uCt)

[Ethereum chain of liars and thieves - Whale Panda](https://medium.com/@WhalePanda/ethereum-chain-of-liars-thieves-b04aaa0762cb) [[1]](https://web.archive.org/web/20181107050849/https://medium.com/@WhalePanda/ethereum-chain-of-liars-thieves-b04aaa0762cb) [[2]](https://archive.is/l9oNL)

[The Robin Hood Group and ETC - Jack Sparrow](https://medium.com/@jackfru1t/the-robin-hood-group-and-etc-bdc6a0c111c3) [[1]](https://web.archive.org/web/20170722034214/https://medium.com/@jackfru1t/the-robin-hood-group-and-etc-bdc6a0c111c3) [[2]](http://archive.is/G7lma)

[ROBIN HOOD GROUP SOLD DAO FUNDS IN AN ALLEGED ATTEMPT TO TANK ETC](https://bitcoinist.com/robin-hood-group-attempted-to-sell-dao-funds-in-an-apparent-attempt-to-tank-etc/) [[1]](http://archive.is/rl30U)

[A DAO drain after the hard fork that nobody knows about - DeviateFish_](https://www.reddit.com/r/ethereum/comments/805gcn/vitalik_said_doing_rescue_forks_in_exceptional/duveapx/?context=3) [[1]](https://web.archive.org/web/20180226224909/https://www.reddit.com/r/ethereum/comments/805gcn/vitalik_said_doing_rescue_forks_in_exceptional/duveapx/?context=3) [[2]](https://archive.is/idEAG)