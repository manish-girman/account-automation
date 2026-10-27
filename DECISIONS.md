# Account Automation — what to settle before we build

**For:** Manish, Girish, Sumit
**Date:** 27 October 2026
**Source:** [AI-native accounting end-to-end flow and wishlist](https://docs.google.com/document/d/1uWzh6Ov8US0zWEZAq7XTh2dfehqcWid5UgmmnDiSzww/edit) (Sumit)

This is a response to Sumit's document. It does not propose a design. It lists
the decisions that must be made first, because the answer to "what do we build
first" changes completely depending on them.

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

### 3.2 Who carries the compliance risk?

The system will draft GSTR-1 and GSTR-3B figures. Somebody signs the return. If
that is Sumit, his membership is exposed, not the software.

This is a liability ceiling, not an engineering gap. No amount of model quality
removes it. It means every cell in the Wishlist marked "Fully automate" that
touches a filing, a payment or a tax judgement is unreachable by design. The
document already half concedes this in its section 4.

**Write the ceiling into the scope now,** or the team will spend a year chasing
an automation target that was never available.

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

**Open**

1. **Volume or premium?** High clients-per-employee at a commodity fee, or
   fewer clients at a fee well above the going rate? See the table in 3.1.
2. **Who owns distribution,** and where do clients fifty through three hundred
   come from?
3. **What is T** — the minutes Sumit needs to review and sign one GSTR-3B
   responsibly? This sets the hard ceiling on clients per signing CA.
4. Which client is the instrumented pilot, and will they agree to it?
5. Does Sumit sign the returns the system drafts, or does the client?
6. Does Girman bill the new firm for engineering, or is it equity in kind?

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
