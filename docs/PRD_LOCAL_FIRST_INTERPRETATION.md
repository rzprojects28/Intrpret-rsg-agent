# Product Requirements Document: Local-First Interpretation

**Product:** Interpreet for Windows
**Phase:** v3 — Local-First Interpretation (variant)
**Document status:** Draft for product approval
**Version:** 1.0
**Prepared:** 29 July 2026
**Owner:** Product
**Primary audience:** Product, engineering, security, operations, and pilot stakeholders

> This document is an alternative to `PRD_MANAGED_INTERPRETATION_SERVICE.md` v1.0
> (19 July 2026). It keeps that document's interpretation quality policy, its
> local recognition design, and most of its operational rigour. It replaces its
> central architectural decision: that Interpreet operates the interpretation
> and the customer controls none of it.
>
> Section 20 lists every change against the managed-service PRD so the two can
> be compared clause by clause.
>
> Legal, privacy, data-protection, and commercial terms must be reviewed by
> qualified advisers before external launch.

---

## 1. Executive summary

Interpreet v3 will deliver live English–Chinese meeting captions where **the
customer chooses where interpretation happens**, and the default choice keeps
it on the user's own machine.

The desktop application captures system audio and performs speech recognition
locally, unchanged from the managed-service design. What changes is the second
stage. Instead of a single mandatory path to an Interpreet-operated backend,
interpretation runs in one of four deployment modes, selected by the customer's
administrator:

| Mode | Interpretation runs | Transcript leaves the device |
|---|---|---|
| **A — On-device** | Local model on the user's PC | No |
| **B — Bring your own key** | Customer's own AI provider account | To the customer's own vendor contract |
| **C — Self-hosted** | Interpreet backend inside the customer's network | No |
| **D — Interpreet-managed** | Interpreet's cloud service | Yes |

Mode A is the default where hardware and measured quality permit. Mode D is the
managed-service design from the companion PRD, retained for customers with no
privacy constraint.

The product's differentiating claim becomes one that is literally true and
verifiable by packet inspection: in Modes A and C, meeting content does not
leave the customer's control boundary. That claim is the reason to choose
Interpreet over the translated captions already bundled with Zoom, Teams, and
Meet, all of which are cloud-processed by architecture.

---

## 2. Problem statement

### 2.1 Why the managed-service design does not fit the observed buyer

The managed-service PRD promises that "raw meeting audio is captured and
recognized on the user's device" and "raw audio is not uploaded" (§3). That
promise answers an objection about **recording and biometrics**. Discovery with
the initial target user established that the actual objection is about
**content**: the meetings discuss sensitive employee matters, and the team is
unwilling to send that material to a third-party processor.

For a content-sensitivity objection, the audio/text boundary is not a privacy
boundary. The compensation figure, the allegation, the employee's name, and the
performance assessment are all in the words. Under the managed design every one
of them transits Interpreet's backend and then an external AI provider. The
architecture reduces exposure surface — meaningfully, since no recording is
retained and no voiceprint is transmitted — but it does not remove the exposure
the buyer is objecting to.

The managed-service PRD is internally consistent about this. PRIV-02 forbids
claiming that all processing is local, and §17 lists transcript privacy as a
risk with customer rejection as its impact. This document takes the position
that the risk is the deciding purchase factor rather than one entry in a table
of ten.

### 2.2 The stated customer preference

The buyer's ranked preference, established in discovery:

1. Bring-your-own-key or on-premise deployment — most favourable
2. Managed cloud with a DPA and zero-data-retention — possibly acceptable
3. Managed cloud without customer-side controls — rejected

The managed-service PRD forecloses option 1 explicitly. §5 lists "support
customer-supplied AI-provider keys" and "support a bring-your-own-model
configuration" as non-goals. DESK-07 requires removing all provider key
resolution from the desktop. §19 decision 4 makes it permanent.

The commercial reasoning behind that foreclosure was sound but addressed a
different scenario, examined in §2.3.

### 2.3 What the managed-service PRD got right, and the distinction it missed

The managed-service PRD's §2 correctly identifies that "a shared provider key
distributed to desktop installations would be extractable and could expose the
startup to uncontrolled usage and cost," and its principle 4 states that "no
shared secret placed in the desktop app is considered protected." Both are
correct and are retained in this document.

The distinction the PRD does not draw is between:

| | Shared Interpreet key on the desktop | Customer's own key on the desktop |
|---|---|---|
| Who can extract it | Any customer | The key's own owner |
| Who bears the cost of abuse | Interpreet | The customer |
| Who bears the blast radius | All tenants | One tenant |
| Whose contract governs the data | Interpreet's | The customer's |

A shared key is indefensible. A customer-supplied key is a different object: the
party who can extract it is the party who owns it, pays for it, and already has
it. The threat model that justifies removing the first does not justify removing
the second.

### 2.4 Remaining v2 problems this document still fixes

The following managed-service PRD problem statements are accepted in full and
carried forward:

- Translation quality varying by machine according to files and credentials
  present — fixed by the mode-independent quality gate in §8.
- Interpreet's inability to control interpretation prompts and policy — fixed by
  shipping the policy with the client and versioning it, §12.
- Silent fallback to a lower-quality path — fixed by §7 principle 3, restated.
- Privacy language not explaining where transcript text goes — fixed by §9.6,
  which makes the disclosure mode-specific.

---

## 3. Product vision

**Interpreet gives English- and Chinese-speaking teams live, professionally
rendered captions without requiring them to send meeting content to anyone.**

### 3.1 Product promise

- Meeting audio is captured and recognized on the user's device.
- Raw audio is never uploaded, in any mode.
- In the default mode, recognized text is never uploaded either.
- Where a customer chooses a mode that transmits text, the product states
  exactly where the text goes, before first use, in specific terms.
- The customer's administrator, not Interpreet, chooses the trust boundary.
- Interpretation quality meets the same measured bar in every shipped mode.
- Uncertainty is shown briefly and professionally, never as an AI explanation.

### 3.2 What this is not

This is not a claim that local processing is inherently better. It is a claim
that the choice belongs to the customer, and that a product which forces the
choice loses buyers whose constraint is non-negotiable.

---

## 4. Goals and success criteria

### 4.1 Goals

1. Make the customer's administrator the party who selects the trust boundary.
2. Ship a default mode in which no meeting content leaves the device.
3. Hold every shipped mode to one measured interpretation quality bar.
4. Never transmit meeting content without the mode having been explicitly
   configured and disclosed.
5. Keep raw audio on the customer computer in all modes.
6. Protect Interpreet-owned credentials absolutely; never ship one to a desktop.
7. Support customer-supplied credentials safely, with the cost and blast radius
   falling on their owner.
8. Deliver consistent professional interpretation behaviour across modes.
9. Provide transparent failure states without silently changing the trust
   boundary or the quality level.
10. Make the privacy claim accurate, specific, mode-dependent, and auditable by
    packet inspection.

### 4.2 Pilot success measures

The phase is successful when:

- Zero Interpreet-owned AI-provider credentials are present in shipped desktop
  artifacts.
- In Mode A, packet-level verification confirms that no audio **and no
  transcript text** leaves the device during a live session.
- In Modes B and C, packet-level verification confirms transcript text reaches
  only the customer-configured endpoint.
- 100% of mode changes are administrator-initiated, logged, and surfaced to the
  end user before the next session starts.
- At least 98% of accepted transcript segments receive either an interpreted
  caption or an explicit error; no segment silently disappears.
- No AI diagnostic essay, apology, or clarification request appears as a live
  caption in the pilot evaluation set.
- Human reviewers rate at least 90% of the defined English–Chinese evaluation
  set as acceptable for meaning, grammatical person, and tone — **measured
  separately per shipped mode**, per §8.
- Mode A end-to-end interpretation latency: p50 ≤ 2.5 s, p95 ≤ 5.0 s on the
  reference hardware in §13.5.
- Modes B, C, D backend interpretation latency: p50 ≤ 2.0 s, p95 ≤ 4.5 s.
- Pilot users can install, complete first-run setup, and start a session without
  developer assistance.
- A model, provider, or network failure produces an honest, recoverable UI state
  and never silently changes mode.

Latency and quality targets are initial product targets and must be validated
against real pilot meetings before becoming contractual commitments.

---

## 5. Non-goals

This phase will not:

- Upload or perform cloud recognition on raw audio.
- Provide certified human interpretation.
- Produce interpreted speech or voice dubbing.
- Record meetings or store audio.
- Provide a searchable transcript archive.
- Add macOS or mobile clients.
- Add microphone capture, microphone-channel separation, or speaker
  diarization. Discovery established that the target user is rarely
  co-located with the other-language participants, so loopback capture covers
  effectively all of their meetings. The unsupported case is handled by an
  honest UI state, DESK-11, not by a capture feature.
- Support arbitrary LLM prompts or general-purpose chat in any mode.
- Expose provider names, model names, or prompts to end users. Administrators
  see them where they must in order to configure Modes B and C.
- Guarantee operation on hardware below the §13.5 minimum in Mode A.
- Build a complete self-service billing portal.
- Promise languages beyond the approved English and Chinese workflows.

---

## 6. Target users and jobs to be done

### 6.1 Primary persona: HR participant

An HR employee joins a mixed-language interview, employee discussion, or
training session and needs to understand the conversation without managing
technical settings, and without worrying that the discussion is being sent
somewhere.

**Job:** "Let me read a reliable interpretation while I stay focused on the
person, without wondering where these words are going."

### 6.2 Secondary persona: organization administrator

An HR or IT administrator selects the deployment mode, satisfies their own
organization's data-handling expectations, and needs to be able to explain the
data flow to colleagues and, where relevant, to legal.

**Job:** "Let approved employees use Interpreet in a configuration I can
defend without a procurement review I cannot win."

This persona gains substantial power relative to the managed-service PRD, where
the administrator manages seats but has no control over the data path.

### 6.3 Internal persona: Interpreet operations

The startup needs to onboard customers, monitor quality and availability where
it operates the service, support configurations it does not operate, and
investigate failures without access to meeting content.

**Job:** "Support four deployment modes without needing to see customer data in
any of them."

---

## 7. Product principles

1. **Audio stays local.** Raw audio must not cross the device boundary, in any
   mode. Unchanged from the managed-service PRD.
2. **The customer owns the trust boundary.** Where interpretation happens is an
   administrator decision, not a product decision. This replaces the
   managed-service principle that all semantic routing belongs to Interpreet.
3. **No silent boundary crossing.** Meeting content must never reach a network
   destination the administrator has not configured and the user has not been
   told about. This is the safety property of this document, and it is the
   inverse of the managed-service PRD's principle 3.
4. **No hidden degradation.** Failure must be visible. A mode must never
   silently substitute another mode, in either direction — not to preserve
   quality, and not to preserve privacy. Retained from the managed-service PRD
   and extended to cover both directions.
5. **Quality is enforced by measurement, not by architecture.** The
   managed-service PRD forbade local interpretation because the available local
   option was poor. This document forbids *unmeasured* interpretation. Any mode
   that passes §8's bar may ship; any that does not, does not.
6. **The client is untrusted with Interpreet's secrets.** No Interpreet-owned
   shared secret is ever placed in the desktop application. Retained verbatim in
   intent from the managed-service PRD.
7. **Collect less.** Do not retain transcript content anywhere unless a clearly
   defined, consented product feature requires it.
8. **Professional voice.** Preserve grammatical person, tone, register, names,
   and technical terms; never narrate "the speaker says." Unchanged.

---

## 8. The gating unknown: local interpretation quality

**This section gates the rest of the document. Mode A must not be built before
it is resolved.**

### 8.1 The open question

The managed-service PRD's implicit premise is that local interpretation means
Argos, and that Argos cannot produce professional zh↔en business output. The
second half of that premise is almost certainly correct. The first half is a
2023 assumption carried into a 2026 document.

Whether a quantized multilingual model running on a business laptop can meet
the §4.2 quality bar for HR-register English–Traditional Chinese interpretation
is **unknown and must be measured**. It is not assumed here. This document is
structured so that the answer, either way, produces a shippable product.

### 8.2 Required evaluation, Stage 0

Before any mode is built:

1. Build the evaluation set defined in §12.1 — the managed-service PRD's set,
   adopted unchanged. Licensed, synthetic, or public content only; no customer
   meeting material.
2. Produce interpretations of the full set through each candidate path:
   - Candidate local models, quantized, on the §13.5 reference hardware
   - A frontier cloud model via the managed backend, as the quality ceiling
   - Argos, as the historical floor and a sanity check on the comparison
3. Score blind. Reviewers must not know which path produced which output.
4. Score on the §4.2 criteria: meaning, grammatical person, tone. Record
   latency and, for local candidates, memory footprint and thermal behaviour
   over a 60-minute session.

### 8.3 Decision rule

| Evaluation outcome | Consequence |
|---|---|
| A local model reaches ≥ 90% acceptable | Mode A ships as the default. Build order: A, B, C, D. |
| A local model reaches 75–89% | Mode A ships as an administrator-selectable option with the measured figure disclosed, not as the default. Mode B becomes the default. |
| No local model exceeds 75% | **Mode A is not built.** Mode B becomes the default and the privacy claim rests on customer-owned credentials rather than on-device processing. |

The third outcome is not a project failure. Mode B satisfies the buyer's stated
preference — bring-your-own-key was named as favourable — at frontier model
quality, with no quality compromise at all. The product's privacy
differentiation survives the loss of Mode A intact.

This is why the evaluation is safe to run before committing engineering effort:
no outcome invalidates the product thesis.

### 8.4 Traditional Chinese specifically

INT-02 requires English to **Traditional** Chinese. Many multilingual models
default to Simplified output. The evaluation must score Traditional output
specifically, not accept Simplified as a pass. If a candidate model is otherwise
strong but emits Simplified, local OpenCC conversion is an acceptable remedy and
must itself be evaluated for the term-mangling it can introduce.

Note that the managed-service PRD's DESK-06 removes "local Chinese script
conversion used for interpretation." This document reinstates it as a supporting
component of Mode A, not as an interpretation fallback.

---

## 9. Functional requirements

Priority definitions:

- **P0:** Required for an internal or paid pilot.
- **P1:** Required before broad commercial availability.
- **P2:** Valuable follow-up, not required for this phase.

### 9.1 Desktop recognition and packaging

| ID | Priority | Requirement |
|---|---:|---|
| DESK-01 | P0 | Capture Windows system audio locally through the selected loopback device. |
| DESK-02 | P0 | Perform resampling, speech detection, segmentation, recognition, and language detection locally. |
| DESK-03 | P0 | Use the approved SenseVoice recognition path for the supported pilot languages. |
| DESK-04 | P0 | Never upload raw audio, audio buffers, audio features, or recordings, in any mode. |
| DESK-05 | P0 | Download only assets required for the configured mode during first-time setup. |
| DESK-06 | P0 | Remove Argos translation and every unmeasured interpretation path. A local path may ship only if it has passed §8. |
| DESK-07 | P0 | Contain no Interpreet-owned AI-provider credential, and no resolver for one, in any shipped artifact. |
| DESK-08 | P0 | Never read or create `%APPDATA%\Interpreet\secrets.json`. Mode B credentials use Windows Credential Manager only, per SEC-03. |
| DESK-09 | P0 | Preserve the dashboard, transcript list, preferred reading language, and always-on-top overlay. |
| DESK-10 | P1 | Offer removal of obsolete v2 translation models after upgrade to recover disk space. |
| DESK-11 | P0 | When no loopback audio is detected for a configured interval during an active session, show "No meeting audio detected" with guidance, rather than an indefinitely empty caption view. Covers the co-located-meeting case that loopback cannot serve. |
| DESK-12 | P0 | Verify the integrity of any downloaded local interpretation model before first use, and refuse to load a model that fails verification. |

### 9.2 Deployment modes and configuration

| ID | Priority | Requirement |
|---|---:|---|
| MODE-01 | P0 | Support Mode A on-device interpretation, subject to §8. |
| MODE-02 | P0 | Support Mode B customer-supplied provider credentials. |
| MODE-03 | P1 | Support Mode C self-hosted Interpreet backend, deployable by the customer from a published container image. |
| MODE-04 | P1 | Support Mode D Interpreet-managed backend, per the companion PRD. |
| MODE-05 | P0 | Make the active mode visible in the desktop UI at all times during a session, in plain language describing where content goes. |
| MODE-06 | P0 | Require explicit administrator configuration to select any mode that transmits meeting content off the device. |
| MODE-07 | P0 | Never change mode automatically. A mode change requires administrator action and takes effect no earlier than the next session. |
| MODE-08 | P0 | Show the user the active mode and its data flow before their first session, and again after any mode change. |
| MODE-09 | P1 | Allow an administrator to lock the mode so end users cannot alter it. |
| MODE-10 | P2 | Allow per-meeting mode selection where an organization runs mixed-sensitivity meetings. |

### 9.3 Interpretation

Requirements INT-01 through INT-12 of the managed-service PRD are adopted with
one structural change: the party performing interpretation varies by mode, and
every requirement below binds whichever party that is.

| ID | Priority | Requirement |
|---|---:|---|
| INT-01 | P0 | Route every finalized transcript segment to the configured interpretation path; that path owns semantic routing and determines whether model interpretation is required. |
| INT-02 | P0 | Support Chinese-to-English and English-to-Traditional-Chinese interpretation. |
| INT-03 | P0 | Preserve the speaker's grammatical person, tone, register, names, numbers, and technical terms. |
| INT-04 | P0 | Return only caption-ready text, with no preamble, model explanation, apology, or request for clarification. |
| INT-05 | P0 | Mark only an unreliable span with the target-language uncertainty marker; if the entire input is unusable, return only that marker. |
| INT-06 | P0 | Use recent session context to resolve pronouns and short fragments without inventing meaning. |
| INT-07 | P0 | Keep prompt, model selection, sampling, token limits, routing, and output validation under Interpreet's version control in every mode, including Mode A where they ship with the client. |
| INT-08 | P0 | Preserve request sequence identity so captions display in source order. |
| INT-09 | P0 | Reject unsupported language pairs and oversized inputs with a typed, customer-safe error. |
| INT-10 | P1 | Support organization-specific terminology and proper-name glossaries, applied identically in all modes. |
| INT-11 | P1 | Support versioned prompt and model rollout with controlled rollback. In Mode A this is bound to client releases. |
| INT-12 | P0 | Maintain a de-identified quality-evaluation suite containing no customer meeting content. Raised from P1: §8 makes it a gating dependency rather than a follow-up. |
| INT-13 | P0 | Validate model output for empty results, meta-commentary, excessive length, unsupported scripts, and known refusal patterns before display, in every mode including on-device. |

### 9.4 Identity and entitlement

Licensing still requires accounts. The change from the managed-service PRD is
that authentication gates **the product licence**, not the interpretation
request, so Modes A and C keep functioning through an Interpreet outage.

| ID | Priority | Requirement |
|---|---:|---|
| AUTH-01 | P0 | Require an authenticated Interpreet account to activate the product. |
| AUTH-02 | P0 | Support invite-based email one-time-code or magic-link sign-in for the pilot. |
| AUTH-03 | P0 | Represent organization, user, membership, device, seat, and account status separately. |
| AUTH-04 | P0 | Issue short-lived access tokens and revocable refresh credentials; never issue AI-provider credentials. |
| AUTH-05 | P0 | Store the refresh credential in Windows Credential Manager, not JSON configuration. |
| AUTH-06 | P0 | Allow Interpreet operations to revoke a user, device, or organization. |
| AUTH-07 | P0 | In Modes A and C, permit continued operation through a licence grace period when Interpreet's licensing service is unreachable, so an Interpreet outage does not stop a customer's meeting. Grace period length set in §19. |
| AUTH-08 | P1 | Support Microsoft Entra ID single sign-on for enterprise customers. |
| AUTH-09 | P1 | Provide an administrator-facing user, seat, and mode management experience. |

AUTH-07 has no counterpart in the managed-service PRD, where an Interpreet
outage necessarily ends interpretation. In Modes A and C, tying a local
capability to a remote licence check would reintroduce the dependency the
architecture exists to remove.

### 9.5 Security

| ID | Priority | Requirement |
|---|---:|---|
| SEC-01 | P0 | Ship no Interpreet-owned provider credential, shared secret, or privileged key in any desktop artifact. |
| SEC-02 | P0 | Verify by static inspection that the desktop cannot reach an Interpreet-owned provider account. |
| SEC-03 | P0 | Store Mode B customer credentials in Windows Credential Manager, never in configuration files, logs, crash reports, or telemetry. |
| SEC-04 | P0 | Transmit Mode B requests only to the provider endpoint the administrator configured, over TLS, with certificate validation that cannot be disabled from the UI. |
| SEC-05 | P0 | Warn the administrator during Mode B setup that the supplied credential's usage and cost fall to their own account, and require acknowledgement. |
| SEC-06 | P0 | Scope Mode B credentials to the narrowest permission the provider supports, and document the recommended scoping per provider. |
| SEC-07 | P1 | Publish the Mode C container image with reproducible builds and signed manifests. |
| SEC-08 | P0 | Sign all desktop releases; verify signature and integrity on update. |
| SEC-09 | P0 | Run dependency and container vulnerability scanning before production deployment. |
| SEC-10 | P0 | Emit audit events for security-sensitive administrative actions, including every mode change. |

### 9.6 Privacy and disclosure

| ID | Priority | Requirement |
|---|---:|---|
| PRIV-01 | P0 | State, before first use and in mode-specific terms, exactly where audio is processed and where transcript text goes. |
| PRIV-02 | P0 | Claim fully-local processing **only** in Mode A, and only after §8 confirms Mode A ships. Never claim it in Modes B, C, or D. |
| PRIV-03 | P0 | Do not store raw audio anywhere, in any mode. |
| PRIV-04 | P0 | Do not persist transcript or interpreted content in logs, analytics, or any product database by default, in any mode. |
| PRIV-05 | P0 | Keep live context in volatile storage and delete it on session end or TTL expiry, whichever is first. |
| PRIV-06 | P0 | Publish an accurate privacy notice and a mode-specific subprocessor disclosure before external pilot. Modes A and C have no subprocessor for meeting content; the notice must say so plainly rather than generically. |
| PRIV-07 | P0 | Define retention, incident response, deletion handling, and support access before processing external HR content in any mode that transmits it. |
| PRIV-08 | P0 | Ensure Interpreet telemetry from Mode A and C installations contains no meeting content and no derived content signal — no segment text, no length distributions fine enough to fingerprint speech, no term frequencies. |
| PRIV-09 | P1 | Complete a DPA template and security overview for customer procurement of Modes B and D. |
| PRIV-10 | P1 | Assess and, if commercially appropriate, obtain a zero-data-retention arrangement from the AI provider for Mode D. |

PRIV-08 is new. It exists because a local-first product that phones home with
content-derived telemetry would defeat its own claim in a way that is easy to
introduce accidentally and hard for a customer to detect.

### 9.7 Usage, cost, and operations

Metering obligations differ sharply by mode, because Interpreet bears provider
cost only in Mode D.

| ID | Priority | Requirement |
|---|---:|---|
| OPS-01 | P0 | Meter licence activation, seat usage, and client version in all modes. |
| OPS-02 | P0 | Meter requests, tokens, latency, failures, and estimated provider cost by organization and day **in Mode D only**. |
| OPS-03 | P0 | Enforce per-user, per-organization, and global request and concurrency limits in Mode D. |
| OPS-04 | P0 | Alert Interpreet operations on abnormal cost, error rate, latency, and authentication failures for services Interpreet operates. |
| OPS-05 | P0 | Provide a kill switch for the Mode D interpretation service and for individual organizations within it. |
| OPS-06 | P0 | Use separate development, staging, and production credentials and environments for Interpreet-operated services. |
| OPS-07 | P0 | Store Interpreet provider credentials only in an approved cloud secret manager, rotatable without a desktop release. |
| OPS-08 | P0 | Use a dedicated production provider workspace with configured spend and rate limits for Mode D. |
| OPS-09 | P1 | Surface Mode A and Mode C health to the administrator — model load failures, latency, hardware shortfalls — without transmitting content. |
| OPS-10 | P1 | Provide customer administrators with aggregate usage reports containing no transcript text. |

---

## 10. System design

### 10.1 Mode A — on-device

```text
┌──────────────────────── Customer Windows PC ────────────────────────┐
│                                                                     │
│  System audio → VAD/segmentation → SenseVoice recognition           │
│                                      │                              │
│                                      ▼                              │
│                     Local interpretation model                      │
│                   (versioned policy + prompt shipped                │
│                        with the client)                             │
│                                      │                              │
│                                      ▼                              │
│                      Output validation → caption                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
        Network use: licence activation and updates only.
        No meeting content crosses this boundary.
```

### 10.2 Mode B — bring your own key

```text
┌──────────────────────── Customer Windows PC ────────────────────────┐
│  System audio → recognition → transcript text                       │
│                                      │                              │
│                        customer-held credential (Credential Manager)│
└──────────────────────────────────────┼──────────────────────────────┘
                                       ▼
                    Customer's own AI provider account
                 (customer's contract, DPA, and retention terms)
```

### 10.3 Mode C — self-hosted

Identical to the managed-service PRD's architecture, with the Interpreet cloud
block deployed inside the customer's network from a published container image.
The customer supplies the provider credential and operates the secret manager.

### 10.4 Mode D — Interpreet-managed

As specified in `PRD_MANAGED_INTERPRETATION_SERVICE.md` §10, unchanged.

### 10.5 Trust boundaries

- The desktop and its network traffic must be treated as inspectable and
  modifiable by the person controlling the computer. Unchanged.
- No Interpreet-owned shared secret exists on the desktop in any mode, so
  desktop compromise cannot expose Interpreet credentials.
- In Mode B the customer's credential is on the customer's machine, owned by
  the customer, billed to the customer. Extraction risk and cost risk are borne
  by the same party.
- In Modes A and C, Interpreet operates no component that can observe meeting
  content, and is therefore not a subprocessor for that content.

That last property is the commercially significant one: it changes what
Interpreet must represent in a procurement review.

---

## 11. Commercial model

The managed-service PRD's economics — subscription revenue funding metered AI
spend — apply only to Mode D. The other modes change the shape of the business.

| Mode | Who pays AI cost | Interpreet marginal cost per seat | Pricing |
|---|---|---|---|
| A | Nobody; local compute | Effectively zero | Per-seat licence |
| B | Customer, own account | Effectively zero | Per-seat licence |
| C | Customer, own account | Support only | Per-seat licence plus support |
| D | Interpreet | Provider tokens | Per-seat plus metered allowance |

Consequences worth stating plainly:

- Modes A through C have materially better gross margin than Mode D, because
  Interpreet is not reselling inference.
- Mode D's cost controls — quotas, idempotency, concurrency limits, spend
  alerts — remain necessary but protect a smaller share of the business.
- Mode A removes the unit-economics risk that dominates the managed-service
  PRD's §17 risk table.
- The competitive position improves: bundled captions in Zoom, Teams, and Meet
  are cloud-processed and cannot offer Modes A or C at all. Interpreet stops
  competing on caption quality against platform incumbents and starts competing
  on a property they cannot match without re-architecting.

---

## 12. Interpretation quality policy

Adopted from `PRD_MANAGED_INTERPRETATION_SERVICE.md` §12 **without change**. The
policy governs interpretation behaviour, which is independent of where
interpretation executes.

The server-managed policy must require the model to interpret as a professional
simultaneous interpreter for a business meeting; preserve grammatical person,
intent, certainty, emotion, politeness, and register; remove only non-meaningful
disfluencies; preserve meaningful hesitation, qualification, and disagreement;
use recent context for pronouns and fragments; avoid inventing missing meaning;
keep names, brands, acronyms, and technical terms accurate; return caption text
only; never explain that the source was garbled; and use a short localized
uncertainty marker for only the unreliable portion.

Two changes to how the policy is enforced:

1. The policy ships with the client in Mode A and is versioned with client
   releases, rather than being changeable server-side. Prompt iteration
   therefore requires a client update in Mode A, accepted as a cost.
2. Output validation (INT-13) runs on-device in Mode A. The validation logic is
   identical across modes and must be a single shared implementation, not two
   that can drift.

### 12.1 Quality evaluation

The evaluation set defined in the managed-service PRD §12.1 is adopted
unchanged, and is promoted from a pilot-approval prerequisite to the Stage 0
gating artifact described in §8.

It must include Mandarin-to-English and English-to-Traditional-Chinese speech;
mixed-language segments; business, HR, interview, compensation, policy, and
workplace terminology; names, numbers, dates, percentages, product terms;
fragments and interrupted speech; recognition errors and partially unclear
source text; first-, second-, and third-person examples; and polite
disagreement and emotionally sensitive workplace language.

Evaluation data must be licensed, synthetic, public, or explicitly approved for
testing. Customer meeting content must not be added by default.

---

## 13. Non-functional requirements

### 13.1 Security

Section 9.5 carries the requirements. The material difference from the
managed-service PRD is that Interpreet holds no credential that a desktop
compromise could reach, in any mode.

### 13.2 Privacy and retention

| | Mode A | Mode B | Mode C | Mode D |
|---|---|---|---|---|
| Raw audio retention | None | None | None | None |
| Transcript leaves device | No | To customer's provider | To customer's server | To Interpreet |
| Interpreet is a subprocessor for content | No | No | No | Yes |
| Content in Interpreet logs | None | None | None | None by default |
| Live context retention | Volatile, session only | Volatile, session only | Customer-configured, default 30 min | 30 min after last activity |

For Mode D, Anthropic's stated commercial terms as of 19 July 2026 — API inputs
and outputs not used for training unless the customer opts in, and generally
deleted within 30 days subject to documented exceptions — apply as described in
the managed-service PRD §13.2, and must be revalidated during legal review
rather than treated as a permanent guarantee.

For Mode B, the equivalent terms are whatever the **customer's** agreement with
their provider specifies. Interpreet must document this clearly rather than
implying it has made a representation about it.

### 13.3 Performance

| Metric | Mode A target | Modes B/C/D target |
|---|---|---|
| Interpretation latency p50 | ≤ 2.5 s | ≤ 2.0 s |
| Interpretation latency p95 | ≤ 5.0 s | ≤ 4.5 s |
| Non-model processing overhead p95 | ≤ 250 ms | ≤ 250 ms |
| Sustained 60-minute session | No thermal throttling causing p95 breach | n/a |

Mode A's looser targets acknowledge local generation on business hardware. If
§8's evaluation shows a candidate model cannot hold these under a sustained
session, that model fails regardless of its quality score — a correct caption
that arrives after the topic has changed is not usable in a live meeting.

Captions must be correlated and displayed in source sequence order in all modes.
Requests exceeding the interactive latency budget must fail visibly rather than
remain pending.

### 13.4 Availability and resilience

- Modes A and C have no dependency on Interpreet availability during a meeting,
  subject to the AUTH-07 licence grace period.
- Mode D service objective: 99.5% monthly availability, excluding announced
  maintenance.
- A single failed interpretation request must never stop local recognition or
  close the application.
- Retryable failures use bounded retries; non-retryable errors fail immediately
  with an actionable UI state.
- No failure in any mode may cause an automatic mode change. This is the
  §7 principle 3 and 4 pair, and it is the property most likely to be violated
  by a well-meaning reliability patch.

### 13.5 Hardware and capacity assumptions

Mode A introduces a hardware floor the managed-service PRD does not have.

- Reference hardware for §8 evaluation and §13.3 targets must be a
  representative business laptop, not a workstation. The specification must be
  fixed before evaluation begins and stated in the results.
- The installer must detect hardware below the floor and refuse to configure
  Mode A on it, explaining why and offering the alternative modes, rather than
  configuring a mode that will underperform.
- Modes B, C, D: up to 50 licensed pilot users and 20 concurrent sessions,
  growing to 200 concurrent without redesign, as per the managed-service PRD.
- Load tests must use realistic segment frequency and token sizes before pilot.

### 13.6 Observability

Measure without recording meeting content, in all modes:

- Licence activations, seats in use, client versions
- Mode distribution across the install base
- Success, retry, and typed failure rates
- Latency percentiles, reported per mode
- Mode A: model load success, inference latency, memory headroom, thermal state
- Modes B/C/D: token counts, and for Mode D estimated provider cost
- Authentication and entitlement failures
- Recognition-to-interpretation queue depth on the desktop

All of the above must satisfy PRIV-08. Any metric whose granularity could
reconstruct content is out of scope regardless of its diagnostic value.

---

## 14. UX states and customer messages

Retained from the managed-service PRD §14 where applicable, with mode-aware
additions.

| State | Primary message | Available action |
|---|---|---|
| Signed out | "Sign in to use Interpreet" | Sign in |
| Invitation invalid | "This invitation is no longer valid" | Contact admin |
| Preparing local recognition | "Preparing captions…" | View progress |
| Preparing local interpretation (Mode A) | "Preparing the interpretation model…" | View progress |
| Hardware below Mode A floor | "This computer cannot run on-device interpretation" | View alternatives |
| Ready | "Ready to listen" | Start listening |
| Listening, Mode A | "Listening — interpreting on this computer" | Stop / open overlay |
| Listening, Mode B | "Listening — interpreting via your organization's AI account" | Stop / open overlay |
| Listening, Mode C | "Listening — interpreting on your organization's server" | Stop / open overlay |
| Listening, Mode D | "Listening — interpreting via Interpreet" | Stop / open overlay |
| No meeting audio detected | "No meeting audio detected" | Check audio device |
| Short interruption (B/C/D) | "Reconnecting to interpretation…" | Continue / stop |
| Prolonged interruption (B/C/D) | "Interpretation is temporarily unavailable" | Retry / stop |
| Mode A model failure | "On-device interpretation stopped" | Retry / contact admin |
| Usage allowance reached (D) | "Your organization's interpretation allowance has been reached" | Contact admin |
| Account suspended | "Interpreet is unavailable for this account" | Contact admin |
| Client update required | "Update Interpreet to continue" | Download update |
| Unsupported language | "This language is not supported yet" | Change reading language |
| Mode changed by admin | "Your administrator changed where interpretation runs" | View details |

The listening-state messages are deliberately different per mode. A user should
be able to tell, at a glance mid-meeting, where their words are going. The
managed-service PRD's rule that messages must not expose provider names, model
names, keys, or infrastructure details is retained for end users; the mode
description is not an infrastructure detail but the product's central promise.

---

## 15. Upgrade and migration from v2

v2 is architecturally closer to this document than to the managed-service PRD,
since v2 already performed local translation. Migration is correspondingly
simpler in one respect and more delicate in another.

1. v3 must never load a legacy provider key from environment variables or
   `%APPDATA%\Interpreet\secrets.json`. A Mode B credential must be re-entered
   deliberately through the administrator flow, into Windows Credential Manager.
   Silently adopting a key found on disk would violate MODE-06.
2. Pilot administrators must be instructed to rotate any provider key previously
   used on a desktop computer, since its handling before v3 is unknown.
3. Argos model directories are obsolete in all modes; offer one-click cleanup
   stating the disk space recovered.
4. Whisper directories are obsolete for interpretation; recognition moves to
   SenseVoice per DESK-03.
5. Existing audio-device and overlay preferences should be retained where the
   schema remains compatible.
6. The upgrade must not start a session until the administrator has selected a
   mode and the user has seen the corresponding disclosure.
7. Rollback to v2 must not be offered as an automatic reliability fallback,
   because v2's interpretation quality is unmeasured against §12.1 and its key
   handling predates SEC-03.

---

## 16. Rollout plan

### Stage 0 — Evaluation and architecture review

- Build the §12.1 evaluation set. This is the critical path item and should
  start before any architectural work.
- Run the §8.2 evaluation and apply the §8.3 decision rule.
- Fix the reference hardware specification.
- Approve this PRD and the mode-specific data-flow designs.
- Threat model focused on credential handling per mode, tenant isolation in
  Modes C and D, telemetry leakage under PRIV-08, and Mode B credential misuse.
- Define retention, privacy copy, incident handling, and pilot terms per mode.

### Stage 1 — Internal alpha

- Build the default mode selected by §8.3, plus Mode B.
- Replace v2 interpretation paths.
- Verify shipped artifacts contain no Interpreet provider credential.
- Verify by packet inspection that the default mode transmits no meeting
  content.
- Test with synthetic and approved public content.

### Stage 2 — Controlled company pilot

- Invite named internal users, including the discovery participant whose
  constraint motivated this variant.
- Exercise every UX state in §14, particularly mode change and hardware refusal.
- Monitor latency, failure rate, and quality daily.
- Confirm with pilot users that the disclosure copy matches what they believed
  they were agreeing to. A privacy claim that users misread is a defect.

### Stage 3 — Assisted paid pilot

- Onboard a small number of customer organizations manually.
- Lead with the mode the customer's constraint requires, not with Mode D.
- Sign pilot terms and mode-appropriate data-processing documentation.
- Build Mode C for the first customer that requires it, not speculatively.

### Stage 4 — Commercial-readiness gate

- Meet P0 and required P1 items.
- Complete security, privacy, licensing, code-signing, support, and incident
  response readiness.
- Validate pricing against measured cost per mode.
- Establish production SLOs for Interpreet-operated components only.

---

## 17. Risks and mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| Local model quality below the bar | Mode A unshippable | §8 measures this before build; Mode B provides frontier quality within the privacy constraint; product thesis survives |
| Local model too slow on business hardware | Captions arrive after the moment | §13.3 sustained-session latency is a pass/fail criterion, not a target; hardware floor enforced at install |
| Hardware floor excludes much of the market | Mode A adoption limited | Detect and redirect to Mode B at install; measure mode distribution via OPS |
| Mode B credential mishandled by customer | Customer-side cost or exposure | SEC-03 through SEC-06; scoping guidance; explicit acknowledgement at setup |
| Reliability patch adds silent mode fallback | Privacy promise silently broken | §7 principle 3 and 4; MODE-07; packet-level acceptance test in §18; audit event on every mode change |
| Content-derived telemetry leaks from Mode A | Privacy claim defeated invisibly | PRIV-08; telemetry schema review as a release gate |
| Four modes multiply support and test burden | Slower delivery, more defects | Single shared interpretation policy and validation implementation; build modes in the §8.3 order, not in parallel |
| Prompt iteration requires client release in Mode A | Slower quality improvement | Accepted; batch prompt changes into scheduled releases; Modes B/C/D unaffected |
| Interpreet outage blocks a customer meeting | Trust damage in a local-first product | AUTH-07 licence grace period; Modes A and C have no runtime dependency |
| Buyer's constraint is unrepresentative | Wrong architecture for the market | Constraint confirmed with one buyer only; validate against Stage 3 customers before deprecating Mode D |
| Recognition error causes bad interpretation | Loss of trust | Unchanged from managed PRD: segmentation quality, uncertainty policy, contextual interpretation, evaluation set |

The second-to-last row is the honest one. This document is built on a single
discovery conversation. It is a well-evidenced bet, not a validated market
finding, and Mode D is retained partly as insurance against it being wrong.

---

## 18. Pilot acceptance criteria

### Credential and security acceptance

- The packaged desktop contains no Interpreet-owned provider key, resolver,
  secrets file, or privileged shared secret.
- Static inspection confirms the desktop cannot reach an Interpreet-owned
  provider account.
- Mode B credentials are present only in Windows Credential Manager, and appear
  in no log, crash report, or telemetry payload.
- Token revocation and organization suspension are tested.
- Tenant isolation is tested in Modes C and D.

### Trust-boundary acceptance

- **Packet-level verification confirms that in Mode A, neither audio nor
  transcript text leaves the device during a live session.**
- Mode B traffic reaches only the administrator-configured provider endpoint.
- No failure, retry, timeout, or degraded condition causes an automatic mode
  change. Tested by fault injection against each mode.
- Every mode change produces an audit event and a user-visible notice.
- Telemetry payloads are inspected and confirmed free of content-derived signal.

### Functional acceptance

- A newly invited user can install, sign in, complete first-run setup, and start
  listening in the configured mode.
- Chinese speech is interpreted into English; English into Traditional Chinese.
- Mixed-language meetings route correctly in every shipped mode.
- Captions preserve ordering when responses complete out of order.
- Hardware below the Mode A floor is detected at install and redirected.
- A session with no loopback audio surfaces DESK-11 rather than an empty view.

### Quality and performance acceptance

- Every shipped mode independently meets the §4.2 quality threshold on the
  §12.1 evaluation set.
- Meta-commentary and clarification essays never appear in accepted outputs, in
  any mode.
- Mode A holds its §13.3 latency targets across a sustained 60-minute session on
  reference hardware.
- Modes B/C/D meet their latency targets under realistic load.

### Privacy acceptance

- First-use copy accurately describes the active mode's data flow, and pilot
  users can restate it correctly when asked.
- Production logs and analytics contain no transcript or interpreted content.
- Live context expires and is deleted per the §13.2 table.
- The subprocessor disclosure correctly states that Interpreet is not a
  subprocessor for meeting content in Modes A and C.

### Operational acceptance

- Operations can see health, quality, and error rates per mode without viewing
  meeting content.
- Alerts are exercised successfully.
- Production deployment and rollback are documented and tested for
  Interpreet-operated components.
- The installer is signed, versioned, and upgrade-tested from the pilot v2
  build.

---

## 19. Product decisions made by this document

1. The customer's administrator selects the trust boundary; Interpreet does not.
2. Audio recognition remains local, and raw audio is never uploaded, in all
   modes.
3. On-device interpretation is the default where §8 and hardware permit.
4. Bring-your-own-key is a supported, first-class configuration. This reverses
   the managed-service PRD's §19 decision 4 and its §5 non-goal.
5. No Interpreet-owned AI-provider credential is ever placed in a desktop
   artifact. This retains the managed-service PRD's intent exactly.
6. No mode may ship without passing the §12.1 evaluation. Quality is enforced by
   measurement, not by prohibiting local execution.
7. Mode changes are administrator-initiated, disclosed, audited, and never
   automatic.
8. Interpreet is not a subprocessor for meeting content in Modes A and C, and
   the privacy notice says so specifically.
9. Transcript content is not persisted by Interpreet by default in any mode.
10. The Interpreet-managed service is retained as one mode among four, not
    removed, so customers without a privacy constraint keep the
    lowest-friction path.

---

## 20. Changes against the managed-service PRD

| Managed-service PRD | This document | Reason |
|---|---|---|
| §3 promise: audio local, text to Interpreet | §3 promise: default sends nothing | The buyer's objection is content, not recording |
| §5 non-goal: customer-supplied keys | MODE-02: BYOK is P0 | Buyer named BYOK as favourable; §2.3 distinguishes it from a shared key |
| §5 non-goal: bring-your-own-model | §8: local model is the default path | The Argos-era premise no longer holds without measurement |
| §7 principle 2: all routing belongs to Interpreet | §7 principle 2: boundary belongs to the customer | Core inversion |
| §7 principle 3: no silent quality degradation | §7 principles 3 and 4: no silent boundary crossing, no silent mode change | Safety property flips direction and widens |
| DESK-06: remove all local interpretation | DESK-06: remove all *unmeasured* interpretation | Prohibition replaced by a quality gate |
| DESK-07: remove all provider key resolution | SEC-01/SEC-03: no *Interpreet* key; customer keys in Credential Manager | Preserves the real threat model, drops the overreach |
| §19 decision 6: local fallbacks removed | §19 decision 3: local is the default | Direct reversal, gated on §8 |
| INT-12 evaluation suite, P1 | INT-12, P0 and Stage 0 gating | It decides the architecture, so it cannot be a follow-up |
| §13.2: single retention policy | §13.2: per-mode retention table | Retention differs by who holds the data |
| §17: unit economics a top risk | §11: risk confined to Mode D | Interpreet stops reselling inference in three of four modes |
| — | DESK-11 no-audio state | Co-located meetings loopback cannot serve |
| — | AUTH-07 licence grace period | A local mode must survive an Interpreet outage |
| — | PRIV-08 telemetry content ban | Local-first claim is defeatable by telemetry |
| §12 quality policy | Adopted unchanged | Independent of where execution happens |
| §12.1 evaluation set | Adopted unchanged, promoted to gating | Well-specified already |
| §10 managed architecture | Retained as Mode D | Still correct for unconstrained customers |

---

## 21. Open product questions

| Question | Proposed default |
|---|---|
| Which local model family for §8 evaluation? | Evaluate at least three quantized multilingual candidates with demonstrated Traditional Chinese capability; do not pre-commit |
| What is the reference hardware specification? | A representative HR business laptop; fix before evaluation and publish with results |
| What is the Mode A minimum hardware floor? | Derived from §8 results, not chosen in advance |
| What is the AUTH-07 licence grace period? | 14 days offline before the product requires reactivation |
| Does Mode A need a first-run model download, or ship in the installer? | Download, with size and time disclosed before it starts; installer size otherwise becomes prohibitive |
| Which sign-in method for pilot? | Invited email plus one-time code; Entra ID as P1 |
| Should source transcript remain visible during a Mode B/C/D outage? | Yes, clearly labelled "Transcript", never presented as interpreted text |
| Is Mode C built speculatively or on demand? | On demand, for the first customer that requires it |
| Does Mode D survive to commercial launch? | Yes, unless Stage 3 shows no customer selects it |
| What is the customer usage unit? | Per-seat licence in all modes; metered allowance in Mode D only |
| How much transcript history is used for context? | Minimum recent context needed for quality, held in volatile memory in Mode A |

---

## 22. Definition of done

This phase is complete when:

1. The §8 evaluation has been run and its decision rule applied on the record.
2. All P0 requirements for the shipped modes are implemented and verified.
3. The desktop contains no Interpreet-owned provider credential path.
4. Packet-level verification confirms the default mode transmits no meeting
   content.
5. Every shipped mode independently meets the approved quality and latency
   thresholds.
6. Failure, revocation, and outage flows have been tested against real packaged
   builds, including fault injection confirming no automatic mode change.
7. Privacy and pilot documentation accurately describe the data flow for each
   mode, and pilot users can restate it correctly.
8. Product, engineering, security, and operations approve the internal pilot.
