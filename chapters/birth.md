# Birth - Ethereum network early days

Ethereum was an idea crafted by a young man named Vitalik Buterin in late 2013. The idea of the Ethereum network was that along the
transactions between people you could also make transactions to an automated software whose source is available to read. This piece of code
you're transacting with is called a **Smart Contract** (term was [invented by Nick Szabo](https://nakamotoinstitute.org/the-idea-of-smart-contracts/) [[1]](http://archive.is/voVlL) [[2]](https://web.archive.org/web/20180808133426/https://nakamotoinstitute.org/the-idea-of-smart-contracts/))
and gets executed by clients running the node.
This way, people don't have to trust each other to transact in a more complex way than just transfering value.
They can agree on any contract and know with 100% certainty how it will behave. A lot of people started
using the term **Code is Law** to describe that the contract (in this case code) will get executed in the exact way it was written,
regardless of possible inconveniences or even flaws in it.

After successfully raising 18 million from the ICO (72 million of Ether was premined), Ethereum gets its first implementation of the blockchain and
[gets launched successfully](https://blog.ethereum.org/2015/07/30/ethereum-launches/) [[1]](https://web.archive.org/web/20181202012716/https://blog.ethereum.org/2015/07/30/ethereum-launches/) [[2]](http://archive.is/OQbuY).


**Note:** Computer code can have flaws and in vast majority of programs, it has many. The way we are used to dealing with software is releasing
it to public and patching defects as they are reported. This can't fly in the world of Smart Contracts because once the money gets stolen from a
smart contract you can't get it back due to the wallets being protected by
[Public-key cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography). Only the owner of the private keys can move the money and once
the money gets in the hacker hands, it's gone forever.


## Slock and The DAO

There have been many contracts written on the Ethereum blockchain but one played a huge role in Ethereum history called
[The DAO contract](https://en.wikipedia.org/wiki/The_DAO_(organization)) [[1]](https://web.archive.org/web/20180629084649/https://en.wikipedia.org/wiki/The_DAO_(organization)).
The contract was written by a company named `Slock.It` started by a former Ethereum CCO `Stephan Tual`. The DAO was launched with a 28 day
crowdsale that helped fund the company. By May 21 2016 the DAO token sale managed to raise over 11.5 million Ether (~$150 million USD at the time).



Shortly after the crowd-sale ended a new exploit is found, termed [race to empty](https://vessenes.com/more-ethereum-attacks-race-to-empty-is-the-real-deal/) [[1]](https://web.archive.org/web/20181202194926/https://vessenes.com/more-ethereum-attacks-race-to-empty-is-the-real-deal/) [[2]](http://archive.is/Sz7cT).
> In Brief: Your smart contract is probably vulnerable to being emptied if you keep track of any sort of user balances and were not very, very careful.

A few days later an official statement from Stephan Tual - CEO of Slock - comes claiming that
[no DAO funds are at risk](https://blog.slock.it/no-dao-funds-at-risk-following-the-ethereum-smart-contract-recursive-call-bug-discovery-29f482d348b) [[1]](https://web.archive.org/web/20180823103440/https://blog.slock.it/no-dao-funds-at-risk-following-the-ethereum-smart-contract-recursive-call-bug-discovery-29f482d348b?gi=38eed1977174) [[2]](http://archive.is/fmYHI) because of this new `recursive call bug` discovery.

> We promptly circumvented this so-called “recursive call vulnerability” or “race to empty” from the DAO Framework 1.1 

Sadly, as it turned out, they could not have been more wrong.

## Summary

1. Ethereum network launches
2. Soon after, a company named *Slock.It* deploys a smart contract that ends up holding 150 million dollars
3. A new bug is described that can be used to empty a lot of contracts that follow bad practices
4. Slock CEO claims they are not vulnerable to this attack

[Continue to next chapter](struggle.md)


## Resources

[Launching the ether sale - Vitalik Buterin](https://blog.ethereum.org/2014/07/22/launching-the-ether-sale/)

[Ethereum launches - Vitalik Buterin](https://blog.ethereum.org/2015/07/30/ethereum-launches/) [[1]](https://web.archive.org/web/20181202012716/https://blog.ethereum.org/2015/07/30/ethereum-launches/) [[2]](http://archive.is/OQbuY)

[The problem with censorship - Vitalik Buterin](https://blog.ethereum.org/2015/06/06/the-problem-of-censorship/) [[1]](http://archive.is/cOqBY) [[2]](https://web.archive.org/web/20180329023550/https://blog.ethereum.org/2015/06/06/the-problem-of-censorship/)

[The DAO - Wikipedia](https://en.wikipedia.org/wiki/The_DAO_(organization)) [[1]](https://web.archive.org/web/20180629084649/https://en.wikipedia.org/wiki/The_DAO_(organization))

[Analysis of the DAO exploit - Phil Daian](http://hackingdistributed.com/2016/06/18/analysis-of-the-dao-exploit/) [[1]](http://archive.is/UkyDr) [[2]](https://web.archive.org/web/20181128191052/http://hackingdistributed.com/2016/06/18/analysis-of-the-dao-exploit/)

[No DAO funds at risk following the Ethereum smart contract recursive call bug - Stephan Tual](https://blog.slock.it/no-dao-funds-at-risk-following-the-ethereum-smart-contract-recursive-call-bug-discovery-29f482d348) [[1]](https://web.archive.org/web/20180823103440/https://blog.slock.it/no-dao-funds-at-risk-following-the-ethereum-smart-contract-recursive-call-bug-discovery-29f482d348b?gi=38eed1977174) [[2]](http://archive.is/fmYHI)

[Deconstructing the DAO attack a brief tour - Peter Vessenes](https://vessenes.com/deconstructing-thedao-attack-a-brief-code-tour/) [[1]](https://web.archive.org/web/20181202194926/https://vessenes.com/more-ethereum-attacks-race-to-empty-is-the-real-deal/) [[2]](http://archive.is/Sz7cT)

[The idea of Smart Contracts - Nick Szabo](https://nakamotoinstitute.org/the-idea-of-smart-contracts/) [[1]](http://archive.is/voVlL) [[2]](https://web.archive.org/web/20180808133426/https://nakamotoinstitute.org/the-idea-of-smart-contracts/)
