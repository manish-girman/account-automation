# Account Automation — what to settle before we build

**For:** Manish, Girish, Sumit
**Date:** 27 October 2026
**Source:** [AI-native accounting end-to-end flow and wishlist](https://docs.google.com/document/d/1uWzh6Ov8US0zWEZAq7XTh2dfehqcWid5UgmmnDiSzww/edit) (Sumit)

This is a response to Sumit's document. It does not propose a design. It lists
the decisions that must be made first, because the answer to "what do we build
first" changes completely depending on them.

**Start at section 9** if you have already read this once. Three documents now
describe this business — this memo, the published landing page and the
27 October planning meeting — and they disagree with each other. Section 9
reconciles them and names what has to be fixed this week.

---

## 1. What the source document actually contains

Two different documents are joined together, and they should be read
differently.

**Sumit's part** — the Raw Flow and the Wishlist. This is real. Eleven steps
from a working practice, each one a task performed every month. Treat it as
evidence.

**The ChatGPT part** — Steps 1 to 9, the three review outcomes, the seven
screens under "Practical first build". This is a generic template. It contains
no client count, no document volume, no hours spent, no error rate, and no
statement of which of the eleven steps hurts most. It reads well and it is
useful as shared vocabulary, but it is not evidence of what to build.

Taken at face value, the second part describes about two years of work.

**Proposal:** scope from Sumit's eleven steps. Use the ChatGPT section for
naming things, not for sequencing them.

---

## 2. The finding that should change the build order

Sumit's raw flow mentions chasing documents twice:

- Step 4 — bank statements arrive "usually after follow-ups".
- Step 7 — traders are "reminded to submit all sales invoices before the
  internal cutoff date, typically the 2nd or 3rd of the following month".

Every other step in the eleven is work the practice can already do competently.
The month-end delay is not caused by the speed of accounting. It is caused by
documents not arriving.

The Wishlist and the ChatGPT flow put almost all their weight on extraction,
matching, reconciliation and close — that is, on automating the part the
practice is already good at. Document collection gets a single row.

**If the bottleneck is intake, the build order should start at intake.**

This should be confirmed with the measurement in section 4 before it is acted
on, but it is the single most important thing the document reveals.

---

## 3. Three questions to settle first

### 3.1 Which business are we in? — DECIDED, 27 October 2026

Three options were on the table: (a) a CA practice that runs cheaper, (b) a
product sold to other CAs and traders, (c) an ERPNext add-on sold into Girman's
implementation base.

**Decision: (a).** An AI-native CA firm. Services as software. Very high
revenue per employee, achieved through a high degree of automation. We sell the
outcome — books kept, returns filed — not the tool.

#### What this rules out

(b) and (c) are off the table, and we should say so rather than calling them
phase two. The consequences are large and mostly favourable:

- The code is a **cost centre, not a product.** It needs to be correct and
  fast, not saleable.
- No multi-tenancy, no onboarding flows, no configurability, no pricing page,
  no software sales motion.
- We may hard-code our own firm's rules. Internal tools are allowed to be
  opinionated and specific. This is a significant speed advantage.

The cost: extracting a product later from an internal tool rarely works, because
the tool comes out shaped by one firm's habits. We should assume we are not
doing (b).

#### The metric this commits us to

Revenue per employee is now the north star. Not documents processed, not
extraction accuracy.

    revenue per employee = clients per employee × annual fee per client

There are only two levers, and **automation moves only the first one.**

Illustrative arithmetic, to be redone with our real numbers:

| Target revenue / employee | Fee per client | Clients per employee required |
| --- | --- | --- |
| ₹50 lakh | ₹10,000 / month | 42 |
| ₹50 lakh | ₹25,000 / month | 17 |

These describe two different companies. Forty-two clients per accountant is a
volume business whose survival depends on client acquisition. Seventeen is a
premium practice whose survival depends on traders agreeing to pay more than
twice the going rate.

**Open: which lever are we pulling?** This matters more than any build
decision and should be settled next.

#### Two risks this choice creates

**Distribution becomes the bottleneck, not delivery.** If one accountant moves
from twenty-five clients to a hundred, we need four times the clients to keep
that accountant occupied. Sumit's existing book is finite. The common failure
for an AI-native services firm is to solve delivery well and then starve: the
automation arrives before the clients do, and senior people sit idle. We need
an owner and a plan for clients fifty through three hundred.

**Sumit's signature may be the real ceiling.** More clients per accountant
means one chartered accountant signs many more returns. If reviewing one
GSTR-3B responsibly takes T minutes, monthly capacity is available hours
divided by T, no matter how good the software becomes. If that number is, for
example, eighty returns a month, the firm caps at eighty clients per signing
CA. Growing past it means adding partner-level CAs, which pushes revenue per
employee back down unless they are added purely as signers. **Sumit should
compute T this week.** It is the hard arithmetic of this choice.

#### What it changes in the build

The build order already proposed stands, now for a stated reason: everything we
build must reduce **accountant-minutes per client-month**, because that is the
only quantity that moves clients-per-employee.

- Intake first. Chasing documents is pure accountant time and it scales
  linearly with client count.
- Review queue second. Supervision time is the metric.
- The measurement in 3.3 now has a precise purpose: establish the baseline
  accountant-minutes per client-month, then watch it fall every month.

If that number is not tracked from the first client, "services as software" is
narrative rather than a plan.

### 3.2 Who carries the compliance risk? — DECIDED, 27 October 2026

**Decision: a partner chartered accountant, not Sumit, paid per signature at a
rate that varies by work type.**

The underlying constraint is unchanged. Somebody signs the return and carries
the exposure, and no amount of model quality removes that. Every cell in the
Wishlist marked "Fully automate" that touches a filing, a payment or a tax
judgement remains unreachable by design.

What the decision does change is that signing capacity becomes **purchasable
rather than fixed.** Add signers, add capacity. Sumit's time goes to judgement,
supervision and encoding rules rather than to signature volume, and the signer
is a variable cost rather than an employee — which keeps revenue per employee
high. That is the right shape.

Three problems come with it.

#### Per-signature payment inverts the incentive on review

The entire compliance control rests on the signer actually examining what they
sign. Paying per signature makes review time a pure cost to the signer, so the
rational signer signs quickly and does not look. This is structural; choosing a
conscientious person delays the problem rather than solving it. It is worst on
cheap work types, where a small per-signature fee buys a correspondingly small
amount of attention.

**Change the payment shape** — a retainer plus volume, or payment for review
time with a volume floor — and instrument the control:

> **Signer query and rejection rate:** the percentage of submissions the signer
> sends back. If it trends towards zero, the control has become theatre, and we
> will discover that during a notice rather than before one.

A signer who never rejects anything is a warning sign, not an efficient one.

#### Whose engagement is it?

Not yet answered, and load-bearing. Two structures, which are opposite
businesses:

- **(i) The client engages the new firm,** which subcontracts the signature.
  The firm owns the relationship and the revenue. Presumably the intent.
- **(ii) The client engages the signing CA's firm,** and the new firm is a
  back-office provider to it. Cleaner on professional-conduct grounds, but we
  do not own the client and can be swapped out.

Choose (i) or (ii), then verify that the chosen structure survives ICAI rules.
Specific items for Sumit and a lawyer to confirm — these are flagged for
checking, not stated as settled law:

- **Fee sharing with non-members.** ICAI's First Schedule restricts a member
  from sharing professional fees with non-members. A non-CA entity collecting
  the client fee and paying a CA per signature sits close to this. Likely the
  binding constraint on structure (i).
- **Certifying without sufficient examination** is explicit professional
  misconduct. Per-signature economics are precisely the fact pattern a
  disciplinary committee would examine.
- **Whether a non-CA entity may hold out as providing attest or compliance
  work at all,** which depends on how the new firm is registered.
- **Tax audit ceilings per member** apply if scope ever widens from returns to
  audits. Not binding today; binding the day it changes.

#### Concentration risk

One partner CA signing hundreds of returns is a single point of failure:
illness, disagreement, disciplinary action, or simply repricing once they
realise they are the bottleneck. **Name a second signer before we need one.**

#### Indemnity and unit economics

If a return is wrong, the signer is exposed first and will pursue the preparer.
Settle in the signer agreement: who carries professional indemnity insurance,
and a written scope of what the signer is relying on. Note that if the firm
indemnifies the signer, the firm has reabsorbed the compliance risk and the
benefit of externalising it is partly lost.

Signature cost is now a per-client variable cost and belongs in the 3.1
arithmetic. At a ₹10,000 monthly fee, every rupee of monthly signature cost
comes directly off margin.

#### What this requires us to build

This is the first concrete product requirement the decisions have produced.

An external signer, paid per signature and working under time pressure, cannot
be asked to log into ERPNext and investigate. The system must hand them a
**self-contained review packet**:

- what is being filed, on one screen
- what changed against the previous period
- what the system flagged
- what the accountant overrode, and the reason given
- the open exceptions list
- a generated statement of what the signer is relying on

Everything shown must be **logged against the signer's identity** — what they
were shown, when, and what they did. That record is their defence and ours.

The packet needs a **reject or query path cheap enough that they will use it.**
If raising a query costs the signer money, queries stop, and with them the
control.

### 3.3 What does one real client month look like, measured?

Nobody can answer "what first" without this, and we do not have a single number
today.

For one real client, over one month, record:

- documents received, by type and by arrival channel (WhatsApp, email, paper, courier)
- count per month
- minutes per document, for keying and for checking
- days lost waiting, by document type
- errors currently caught at review, and how

This is two weeks of tally sheets. It is the cheapest and highest-leverage
thing available to us right now.

---

## 4. Proposed first 30 days

No production code.

1. **Decide (a), (b) or (c).** One sitting, three partners.
2. **Instrument one client for one month.** Tally sheets, not software.
3. **Sumit builds a tax-treatment test set.** One hundred real documents already
   processed, each with the correct ledger, GST treatment and TDS answer. This
   is the asset nobody else can produce. Every later claim about accuracy is
   measured against it, and it doubles as the acceptance criteria.
4. **Run one spike:** feed thirty real handwritten trader invoices to current
   vision models and measure field-level accuracy, especially on line items.
   One day of work.

On item 4: handwritten extraction is listed in the Wishlist as a given. It is
the hardest item on the list. Indian trader invoices have no template, frequent
overwriting, regional script and are photographed on phones. If line-item
accuracy comes back low, the review burden may exceed the cost of keying by
hand, and the shape of the first build has to change. We should find this out
in week one, not month six.

---

## 5. What we should not build

- **GSTR-2B and purchase reconciliation.** `india_compliance` is already
  installed on our bench — free, open source, actively maintained. ClearTax,
  IRIS and Cygnet hold the commercial end of this. There is no differentiation
  available here. Evaluate `india_compliance` before writing a line of it.
- **Inventory, the month-end close dashboard, the audit trail.** ERPNext has a
  version of each. Steps 6, 7 and 9 of the source document largely re-describe
  ERPNext.
- **Seven screens.** The first build is one inbox and one review queue. Match
  Center, Reconciliation Dashboard and Compliance Calendar are month six and
  later.

---

## 6. Where differentiation is actually available

In rough order of strength:

1. **Intake over WhatsApp, with chasing tied to the filing calendar.** This
   attacks the real bottleneck and it is not commoditised.
2. **TDS applicability checked at bill entry.** Rules-based, genuinely
   automatable, real exposure when missed, and thin competition inside the
   ERPNext ecosystem. The source document raises it twice — Sumit clearly feels
   this one.
3. **Sumit's judgement, encoded.** The test set from item 3 above, grown over
   time. This is the only part a competitor cannot buy.

---

## 7. Firm-level items to settle in writing

- **IP ownership.** Girman engineers will write the code. The repository sits
  under a Girman account. The firm is Manish, Girish and Sumit. Who owns the
  codebase? This is ambiguous today and ugly later. The licence file in this
  repository currently records the ambiguity rather than resolving it.
- **ICAI constraints — Sumit to confirm.** A member in practice faces
  restrictions on engaging in other business or occupation, and on
  solicitation. A chartered accountant partnered into a software firm that
  sells to his own accounting clients touches both. Sumit knows the current
  position; this is on the list so it is not discovered in month nine.
- **Client data.** Financial records belonging to Sumit's clients will move
  into a system operated by a separate firm. Engagement letters and a data
  processing agreement need to cover this before the first document is
  uploaded.
- **Engineering cost.** Does Girman bill the new firm for engineering time, or
  is it equity in kind?

---

## 8. Open questions for the three of us

**Settled**

- ~~(a), (b) or (c)?~~ → **(a), an AI-native CA firm. See 3.1.**
- ~~Who signs?~~ → **A partner CA, not Sumit, paid per signature by work
  type. See 3.2.**

**Open**

1. **Volume or premium?** High clients-per-employee at a commodity fee, or
   fewer clients at a fee well above the going rate? See the table in 3.1.
2. **Who owns distribution,** and where do clients fifty through three hundred
   come from?
3. **Structure (i) or (ii)** — does the client engage us, or the signing CA's
   firm? See 3.2. Needs a lawyer and an ICAI check, not an opinion.
4. **What does a signature cost per work type, and how many minutes of review
   does that buy?** Replaces the earlier question about Sumit's capacity.
5. **Who is the second signer?**
6. Which client is the instrumented pilot, and will they agree to it?
7. Who carries professional indemnity insurance — the signer, the firm, or
   both?
8. Does Girman bill the new firm for engineering, or is it equity in kind?
9. **Khatā or Munim?** Two names are in flight and one is already published.
   See 9.3.
10. **Who owns the CA-knowledge work** the engineers asked for on 27 October?
    Four action items are unassigned and all of them need a chartered
    accountant. See 9.4.
11. **Does the tax and rulings knowledge base belong in scope at all,** and if
    so who builds it? See 9.2.

---

## 9. Reconciliation: this memo, the landing page, the planning meeting

Three documents now describe this business and they do not agree. The gap
between them is currently the largest risk in the project — larger than any
technical question — because work is already being assigned against the
version that has had the least scrutiny.

The three:

- **This memo**, sections 1–8.
- **The landing page**, "Munim AI Accountancy", already published at
  <https://claude.ai/artifact/NamW6MxD4MxGToJfEjVSWE>.
- **The planning meeting**, 27 October 2026, 1h08m — Manish, Girish, Jai.
  <https://app.fireflies.ai/view/AI-Native-CA-firm-planning::01M5SJVBFBN04XJMCZQFM2EBFC>

### 9.1 Where all three agree

Reached independently, so treat these as settled:

- Document collection is the hardest part and the place to start
  (meeting 14:23 and 14:39; section 2 of this memo).
- WhatsApp beats a web app as the intake channel (meeting 33:09; section 6).
- Human review comes before automation (meeting 53:35; section 3.2).
- AI should sit behind the scenes rather than lead the pitch (meeting 58:15,
  and the same conclusion from an outside review of the landing page).

### 9.2 Scope expanded when it should have narrowed

The meeting added inventory accounting with expiry, damage and weight loss;
income tax and ITR; statutory audit; Companies Act audit reports; a tax
knowledge base grounded in rulings and case law; model benchmarking; Hindi
support; and company formation services.

Section 5 of this memo argues against building inventory and the close
dashboard, and against the seven-screen scope. The meeting went the other
way. Each addition is defensible alone. Together they are several years of
work for a firm with an empty repository and no clients.

**The tax knowledge base is the one to stop and think about.** The meeting
itself notes that rulings require validation and that case law is often
ambiguous (51:38). That is a legal retrieval product with the worst failure
mode available to us: a confidently wrong tax position, signed by a CA who is
paid per signature. It has no build owner.

### 9.3 Five conflicts to resolve

1. **None of the eight open questions in section 8 were discussed.** The
   meeting was entirely product. No pricing lever, no distribution owner, no
   signature cost, no second signer, no pilot client, no indemnity, no
   engineering billing.
2. **Decision 3.2 did not come up at all.** The external signing CA — the
   arrangement that makes capacity purchasable and the compliance model
   work — is absent from the product plan built on top of it.
3. **GSTR-2B reconciliation was assigned for in-house build** (meeting 37:23,
   45:46). Section 5 says `india_compliance` is already on our bench, free and
   maintained, and that the commercial end belongs to ClearTax, IRIS and
   Cygnet. Nobody has evaluated it. Evaluate before building.
4. **The published page contradicts a decision taken the same day.** The
   meeting concluded that AI should stay in the background; the live page
   leads with "Your books, closed by AI."
5. **Two brand names are in flight.** The meeting explored "Khatā"
   (01:01:08). The published page says "Munim AI Accountancy". Neither is
   decided and one is already public.

### 9.4 The gap that constrains everything else

Four action items from the meeting are unassigned, and they are precisely the
ones that need a chartered accountant:

- collect audit report forms and tax rules
- build GST reconciliation checks
- validate GST numbers
- develop customer and entity matching logic

At 27:31 the product team explicitly asked for audit-report and tax context in
order to build correctly. Sumit does not appear in the action items.

The engineers have asked for domain knowledge and nobody owns supplying it.
That is the binding constraint on every other item on the list, and it is the
contribution the partnership is built around.

### 9.5 Benchmarking without a baseline

Model benchmarking is owned (meeting 01:05:20), but no one is measuring what
an accountant currently spends per client-month. Without that baseline a
benchmark cannot say whether we are winning, and accountant-minutes per
client-month is the metric decision 3.1 commits us to. This is item 3 of
section 4 and it is still not assigned.

### 9.6 Fixable this week

Three of these cost hours, not weeks, and should not wait for the next
session:

1. Assign the four unassigned action items to Sumit.
2. Decide Khatā or Munim.
3. Take the landing page down, or change the headline so it matches 58:15.

---

## Current state of this repository

Nothing has been built against the product. What exists:

- Frappe site `ca.localhost` on `frappe-bench-v16`, with ERPNext installed.
  The setup wizard has deliberately **not** been run: company, country, fiscal
  year and chart of accounts all depend on which client becomes the pilot.
- This app, `account_automation`, scaffolded and empty.
- Licence set to proprietary, pending the IP decision in section 7.

No product code should be written until the remaining questions in section 8
are answered.
