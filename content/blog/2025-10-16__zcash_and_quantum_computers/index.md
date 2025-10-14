+++
title = "Zcash and Quantum Computers"
authors = ["Sean Bowe"]

[extra]
page_justification = true
+++

Zcash’s protocol designers and cryptographers differ about how _soon_ quantum computers will become a real threat, but we broadly agree that preparing for their potential impact on user privacy and security is crucial. This careful attitude has existed since the project's inception: we have always anticipated future cryptographic breakthroughs—quantum or otherwise—and worked to compartmentalize the parts of our protocol that rely on vulnerable assumptions. As an extreme example of our attention to detail, [ZIP 212](https://zips.z.cash/zip-0212) identified and fixed an issue where a (possibly even _non_-quantum) adversary that could break zk-SNARK soundness would have had the ability to compromise privacy in a somewhat contrived situation. As mentioned in the ZIP, **"we have attempted to avoid any instance in the protocol where privacy (even against interactive attacks) depended on strong cryptographic assumptions"** and we have been cognizant of these threats throughout our project's history.

**Thanks to this careful protocol design, Zcash’s shielded transactions already achieve post-quantum privacy in many common cases.**[^paymentaddr] Notably, quantum adversaries cannot compromise on-chain _anonymity_ whatsoever.[^anonymity] This advantage stems from Zcash’s use of zero-knowledge proofs, (perfectly) hiding commitment schemes, blinded signing keys, and strong symmetric primitives used in various parts of our cryptography. **Despite this, we have never claimed to have post-quantum privacy because the contexts in which these guarantees hold are not intuitive to users.** We would much rather wait until we have perfected the situation to avoid misleading our users.

As part of our ongoing work to scale Zcash with [Project Tachyon](@/blog/2025-04-02__tachyon_scaling_zcash_oblivious_synchronization.md), we plan to remove [in-band secret distribution](@/blog/2025-04-02__tachyon_scaling_zcash_oblivious_synchronization.md#shielded-notes-and-commitments) from Tachyon transactions to **fully defend against all quantum attacks on privacy** in on-chain transactions. This step is essential, because [harvest now, decrypt later](https://en.wikipedia.org/wiki/Harvest_now,_decrypt_later) threats are perilous for blockchain protocols.

## Soundness

The remaining concern about quantum computers in our protocol’s shielded components arises from the potential vulnerability of [elliptic curve cryptography](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography). These cryptographic breakthroughs would put the _soundness_ of our protocol in jeopardy, which would possibly allow counterfeiting or (somewhat equivalently) theft of user funds, though not necessarily pose a risk to user privacy. **Our users are currently protected under the same cryptographic assumptions that almost every other cryptocurrency relies on.**[^pairings]

Although we must make every effort to improve the _privacy_ guarantees of our protocol in the face of quantum adversaries, soundness breaks are less of a priority because of our ability to adapt and react to quantum developments.[^priorities] There is a huge opportunity cost for changing our protocol to _fully_ defend against future quantum computers.[^opportunity_cost] In my view, there are many risks for our project's survival that are more imminent.[^other_risks]

**However, we _can_ minimize the tail risk.** In other cryptocurrencies there is a burgeoning study of quantum robustness techniques that would allow funds to be immune to quantum adversaries (under plausible assumptions) if the community flipped a switch to replace or augment spending conditions with stricter post-quantum tests.[^pqb] Protocol designers at the [Electric Coin Company](https://electriccoin.co/) have spent some time over the last year developing techniques in Zcash's shielded transactions (specifically, for Orchard) for what is referred to as _quantum recoverability_. **After a change to the way wallets work, likely to be integrated over the next year, if quantum computers _do_ suddenly appear then users can safely recover their funds through a special mechanism that prevents quantum adversaries from stealing funds.** This mechanism would also protect user privacy.

There are other improvements in the pipeline. For one thing, the design of Tachyon inherits many of the philosophies that make this proposed recovery protocol possible. And a general purpose _long-term storage protocol_ that allows users to fortify their (cold storage) funds is being considered for those who are concerned about quantum computers or bugs in our cryptography more generally. Finally, we've worked hard to ensure that our dependence on pre-quantum cryptographic assumptions is isolated to primitives that _can_ be replaced with quantum-resistant technology in the future.

## Today's Best Practices

**The best practice for protecting your shielded funds from a quantum apocalypse is to just shield your coins and await upcoming improvements to the software.** Even after the aforementioned changes, the main _soundness_ threat from quantum computers remains the ability for adversaries to counterfeit shielded coins, and while Zcash's cryptographers will stay ahead of developments to protect users, **the final defense remains [turnstiles.](https://z.cash/turnstile-enforcement-against-counterfeiting/)**

-----

[^paymentaddr]: Unless a quantum adversary also has your payment address, Zcash's shielded transactions are **completely indistinguishable** on-chain.
[^anonymity]: In other privacy-focused blockchains (like Monero) the so-called "key images" are not post-quantum hiding, and so the transaction graph is [completely transparent to a quantum adversary](https://ccs.getmonero.org/proposals/research-post-quantum-monero.html). Zcash's nullifiers (an equivalent concept) are constructed using strong cryptography like [keyed PRFs](https://en.wikipedia.org/wiki/Pseudorandom_function_family) that are not vulnerable to quantum computers.
[^pairings]: Zcash's latest shielded protocol (Orchard) does not use pairing-friendly elliptic curves, and so we are less vulnerable to breakthroughs in that kind of cryptography.
[^priorities]: Quantum privacy breaks are usually retroactive, whereas soundness breaks are usually not.
[^opportunity_cost]: The post-quantum cryptography that we need in Zcash—recursive zk-SNARKs, signature schemes and possibly more—will result in large transactions and may cause disruptive changes in things like hardware wallets. Prematurely adopting this bleeding-edge cryptography may slow adoption of shielded transactions, put our project at odds with emerging standards, lock us into inefficient technology that is likely to be obsolete in the next few years, or expose us to risks involving understudied cryptographic assumptions.
[^other_risks]: Our protocol needs users, it needs to scale, it needs sustainable development and investment, it needs decentralization, and it must achieve all of this _very soon_ while the regulatory environment is still tolerable.
[^pqb]: See [eprint.iacr.org/2025/1307](https://eprint.iacr.org/2025/1307.pdf) and related discussions.
