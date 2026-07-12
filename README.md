# lilai-decision-confidence
# Decision Confidence System
### A trust-sensitive escalation prototype for high-cost, long-cycle decisions

**Live demo → [arsha-hsia.github.io/lilai-decision-confidence](https://arsha-portfolio.github.io/lilai-decision-confidence/)**

Built for [哩來愛爾蘭 · Lilai Ireland](https://www.instagram.com/lilaiireland) — a study abroad consultancy I co-founded in Dublin, specialising in the Taiwan → Ireland market.

---

> *"AI choosing not to answer" is a trust signal, not a system failure.*
>
> Students don't lack information. They lack trust in the right source, at the right moment.
> This system is built on that distinction.

---

## What this is

A functional HTML prototype for a study abroad consultancy.

Users fill a 5-question diagnostic quiz → receive a personalised Blocker report → optionally complete a validation feedback loop → consultant receives a structured brief before any outreach happens.

The system routes differently depending on **trust state** — not problem complexity.

---

## Decision rationale

Every significant design choice in this prototype has a documented defense chain:
**fork → judgment criteria → decision → failure condition.**

### Decision 1 · Validation before email delivery

**The fork:**
- Path A: Send report immediately on form submission
- Path B *(chosen)*: Require validation feedback first, then deliver report

**Judgment criteria:** Validation responses collected after report delivery become social-courtesy answers (ceiling-effect scores, no signal). The feedback must be collected while the user is present and emotionally engaged.

**What I gave up:** Users who read the result and close the tab — their data is lost entirely.

**Failure condition:** Revert to Path A if scroll-to-validation rate drops below 40% over two consecutive weeks, or if 7 of 10 submitted responses show ceiling-effect scores across all questions.

---

### Decision 2 · Low-trust classification gate before escalation

**The fork:**
- Path A: Route all low-trust signals directly to immediate human escalation
- Path B *(chosen)*: Insert a classification question first — "Is this result inaccurate, or do you just not know what you want yet?"

**Judgment criteria:** Low trust has two structurally different causes:
- **Brand skepticism** → human needs to appear quickly and build credibility
- **Self-ambiguity** → user needs space and content first; pushing human contact prematurely increases pressure and reduces response rate

Conflating these two produces wrong interventions for half the low-trust population. The classification gate is what makes the system's core research question testable: does nurture-first actually outperform immediate escalation for self-ambiguous users?

**What I gave up:** Simpler logic, consistent consultant workflow, lower build cost.

**Failure condition:** Collapse back to single urgent escalation route if 15 consecutive low-trust cases show less than 10 percentage points difference in response rate between the two paths.

---

### Decision 3 · Consent checkbox defaults to unchecked

**The fork:**
- Path A: Opt-out (pre-checked — user must uncheck to decline contact)
- Path B *(chosen)*: Opt-in (unchecked — user must actively agree to be contacted)

**Judgment criteria:** The product's core claim is that it respects user agency at every step. A pre-checked box contradicts this at the moment the user is most likely to feel like a lead being captured rather than a person being helped. A user nudged into outreach starts the relationship on a weaker trust foundation than one who chose it.

**What I gave up:** More leads in the short term — some users will complete the flow and not check the box.

**Failure condition:** Re-examine the lead-gen proposition (not the checkbox default) if more than 70% of users who complete the full flow leave the checkbox unchecked. That signal would indicate the product is attracting users whose intent is to get the free diagnostic, not to find a consultant.

---

## System architecture

```
[5-question intake quiz]
        ↓
[Lead form: name + email + opt-in consent checkbox]
        ↓
[Diagnostic result page — displayed immediately]
        ↓
[Validation feedback: 3–4 questions depending on trust signal]
        ↓                               ↓
[High trust: full 4-question flow]  [Low trust: classification gate]
        ↓                               ↓                  ↓
                               [Brand skeptic]      [Self-ambiguous]
                                      ↓                    ↓
                              [Urgent notify]       [Nurture notify]
        ↓
[Apps Script: lead_notify → Google Sheets + consultant email brief]
        ↓ (triggered after validation submit)
[Apps Script: send_student_email → diagnostic report to student]
```

---

## Data collected per lead

| Field | Source | Used for |
|---|---|---|
| Blocker type (5 categories) | Quiz logic | Routing + consultant brief |
| Consent to contact | Opt-in checkbox | Determines outreach eligibility |
| Clarity before (1–5) | Validation Q1 | Baseline trust measurement |
| Felt helped | Validation Q2 | Diagnostic quality signal |
| Accuracy perception | Validation Q3 | Low-trust trigger |
| Low-trust type | Classification gate | Routing: urgent vs. nurture path |
| Confidence after (1–5) | Validation Q4 | Post-diagnosis trust delta |

---

## What I chose not to build (and why)

**Automated nurture sequence**
Sensitivity simulation showed the nurture vs. urgent revenue margin is ~NT$197K per 1,000 users under optimistic assumptions. That margin is too narrow to justify building automation infrastructure before empirical validation of the response rate differential. Running 10–15 manual cases first.

**Scroll and exit tracking**
Behavioural proxy signals (time-on-page, `beforeunload` events) were deliberately excluded. The system asks users directly how they feel rather than inferring it from behaviour. This is a design principle, not a technical limitation. When a user says the diagnosis doesn't feel accurate, the right response is to ask why — not to infer from how long they hovered.

---

## Validation status

| Metric | Target | Status |
|---|---|---|
| Lead → consultation booked | > 50% | **80%** (n=5, invited sample) |
| Consultation → enrollment | > 30% | In progress |
| Felt helped (validation Q2) | > 70% positive | Collecting |
| Confidence delta (Q1 vs Q4) | Positive shift | Collecting |
| Nurture vs. urgent response rate | > 10pp difference | Untested — manual phase first |

*n=5 beta sample. All recruited via direct invitation — not cold traffic. Conversion figures should not be extrapolated until cold traffic data is available. This is documented as a known limitation, not a footnote.*

---

## Stack

- Vanilla HTML / CSS / JS — single self-contained file, no framework dependency
- Google Apps Script — two-stage email logic, Sheets logging, branching by trust type
- GitHub Pages — deployment (this repo)
- Google Sheets — live lead database with consent and low-trust-type columns

---

## Related

- Live prototype: [arsha-hsia.github.io/lilai-decision-confidence](https://arsha-hsia.github.io/lilai-decision-confidence/)
- Company Instagram: [@lilaiireland](https://www.instagram.com/lilaiireland)
- Portfolio: [github.com/arsha-hsia](https://github.com/arsha-hsia)
