+++
title = "Disabling Sprout the Right Way"
authors = ["Sean Bowe"]

[extra]
page_justification = false
page_history = false
page_history_url = "https://github.com/ebfull/seanbowe.com/commits/master/content/blog/2026-09-04__deprecating_sprout_the_right_way"
+++

One of the five questions being asked as part of [sentiment gathering] for
Zcash's upcoming NU7 upgrade concerns the deprecation of _Sprout_, the original zk-SNARK protocol
that launched with Zcash back in 2016:

> **When should v4 transactions be disabled?**
>
> 1. Immediately at NU7 activation.
> 2. One year after this poll concludes.
> 3. Do not set a date to disable v4 transactions.
> 4. Abstain.

Transactions involving the Sprout shielded pool can only occur in the old
`v4` transaction format, so disabling `v4` transactions as part of a network upgrade
will effectively suspend the Sprout pool indefinitely.

**This is a long time coming.** Sprout is already deprecated; we long since disabled
transfers into the pool ([ZIP 211](https://zips.z.cash/zip-0211)) due to the [famous discovery]
of an undetectable counterfeiting vulnerability back in 2018, which I
played a part in remediating. And hardly any funds remain in it: [~22,600 ZEC](https://cipherscan.app/pools) at the time of this writing.

Protocol engineers throughout Zcash, including myself, want us to get rid of this
thing. It is pure technical debt and poses a needless security risk for the project.
Earlier this year, an _exploitable_ Sprout counterfeiting bug [was identified and fixed].
And just yesterday, a non-exploitable [bug] in Zebra was discovered that
similarly revealed Sprout's fragile, aging attack surface.

Keeping the pool around has other risks. It is still possible, theoretically, to
make payments _within_ the Sprout pool, even though no wallets support it. This
contradicts how Orchard was [recently deprecated](https://tachyon.z.cash/blog/auditing-orchard-supply/#the-new-approach)
in NU6.3: payments were made impossible so that (hypothetically counterfeit)
coins were forced to travel through a turnstile, allowing us to
establish a coherent definition of circulating supply. Due to technical limitations,
enforcing the same thing in Sprout without changing its circuit is tricky.

### What happens to the funds?

The question of disabling `v4` transactions and suspending Sprout is, on its surface,
completely orthogonal to what happens to Sprout user funds. The coinholder
vote makes this clear:

> The disposition of the affected funds is out of scope for this poll and is not specified here.

Some want to destroy the Sprout pool funds permanently, others
want to recycle the funds for future mining rewards, and yet more insist the funds
should live in the pool indefinitely, until they're claimed by their rightful owners or stolen by
a quantum attacker or other hack.

I can see why folks in the latter category are concerned about the pool being
disabled before a clear plan is established, as it appears to be a loss by default.

But it turns out that disabling `v4` transactions works for everyone involved!

### Sprout Recovery Protocol

In future network upgrades, to accommodate Tachyon and other kinds of improvements
for Zcash, we'll likely be using some kind of
extensible transaction format (such as [ZIP248], proposed by [ZODL])
that is designed to reduce wallet breakage at major network upgrade boundaries. The
technical details don't matter much here; what's important is that Sprout would
ideally interoperate with this new format instead, to reduce technical debt.

Of course, we also want to disable payments within the pool, for the reason
I mentioned before, and we definitely don't want to maintain Sprout's cryptography
forever either.

My suggestion works like this:

* Disable `v4` transactions in a network upgrade. (My preference is for NU7.)
* Once this happens, its commitment tree (and thus all spendable note commitments,
and thus the number of nullifiers that can be revealed) is frozen.
* Instead of making funds spendable via a shielded protocol, like today, we
replace it with a **recovery protocol** that lives in the new extensible
transaction format. Due to the nature of that format, wallets do not need to care
how these Sprout-related components work.
* Any funds extracted through this recovery protocol go through the turnstile as
normal; the draft ZIP 248 transaction format actually makes the turnstile a
first-class component of the transaction format itself.

The recovery protocol is simpler, which is part of what makes this elegant:
it only requires a zk-SNARK proving knowledge of a small number of SHA256
preimages. It does not require us to maintain the existing Merkle tree either; nodes
can immediately purge that tree and its roots (anchors) from their state databases
after `v4` transactions are disabled. The recovery protocol can use its own Merkle
tree or some other accumulator instead, for performance.

Interestingly, this makes it a limited kind of _quantum_ recovery
protocol, due to the heavy use of SHA256 in the original Sprout design. Something
like this for Sapling would not be quantum recoverable. And so regardless of what
the community decides we should do with `v4` transactions, we
need to start a conversation about [deprecating Sapling as well.](https://forum.zcashcommunity.com/t/sapling-withdraw-only-discussion-kickoff/55223)

### Community Sentiment

How do you feel about this plan? It's important to stress that disabling `v4`
does not mean we've made a decision about what happens with the coins; the polling
makes this explicit. But it may be a prerequisite even for the folks who want to keep
Sprout funds spendable indefinitely.

Ultimately, deciding what should happen with the Sprout pool funds is not up to
me, and we'll need to have more voting and debate about it in the coming months.
But I'm increasingly convinced that the store-of-value case for Zcash requires
us to make these funds recoverable for as long as possible, and **I will be
advocating for the recovery protocol path going forward.**

[ZIP248]: https://zips.z.cash/zip-0248
[ZODL]: https://zodl.com/
[bug]: https://github.com/ZcashFoundation/zebra/issues/11386
[sentiment gathering]: https://forum.zcashcommunity.com/t/nu7-coinholder-vote/56912
[was identified and fixed]: https://shieldedlabs.net/zcash-vulnerability-successfully-remediated/
[famous discovery]: https://electriccoin.co/blog/zcash-counterfeiting-vulnerability-successfully-remediated/
[Zakura]: https://zakura.com/
