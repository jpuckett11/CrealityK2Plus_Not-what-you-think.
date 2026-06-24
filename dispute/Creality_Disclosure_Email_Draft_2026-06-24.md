# Creality Disclosure Email Draft

**Date drafted**: 2026-06-24
**To**: Creality customer service (current dispute thread)
**From**: investigations@obsidianwatch.org
**Cardholder/Investigator**: Jay Puckett (Reckoner), Lead Investigator,
Obsidian Watch Group

**Purpose**: Status update on bank dispute (concise, no concession) AND
an Obsidian Watch Group pre-disclosure offer covering the OWG Creality
investigation. The "separately and unrelated" framing maintains
legal/structural cleanliness while informing the merchant of the
publication trajectory.

---

## Email body

**Subject**: Status update on the open matter, and a related Obsidian Watch Group pre-disclosure offer

Hello,

A brief status update on the open matter. The bank chargeback dispute on
my order remains active. As documented in supplemental sworn affidavits
on the bank's record, my chargeback is limited to the contents of the
SpeedX-shipped package (tracking SPXBNA007982605180000011) that did not
arrive at my fenced and gated property despite the carrier marking it
"delivered to front door." I will continue to keep you advised on
bank-side developments.

Separately and unrelated to the present dispute, the organization I
serve as Lead Investigator (Obsidian Watch Group) has been continuing
its technical-audit work on the Creality K2 Plus printer, the
CrealityCloud cloud platform, the CrealityPrint slicer software, and
the bundled connectivity, telemetry, and over-the-air-update behavior
of the device. The work is part of an ongoing OWG investigation into
the connected-device conduct of PRC-domiciled consumer-hardware
manufacturers. As of this week, the investigation has reached a
substantial documentation state. Components of the case file are
already publicly available at
`github.com/jpuckett11/CrealityK2Plus_sweep`, and the umbrella
structural case file integrating the technical, commercial, and
regulatory findings is in active development at the same repository.

The audit categories include:

- Continuous device-level behavior that operates independently of
  user-interface consent decisions
- Persistent cloud-side registration and remote-access infrastructure
  on the device, including verified live testing under controlled
  conditions
- Network-egress destinations including PRC-jurisdiction infrastructure
  and the specific traffic profile generated under normal operation
- Hardware-layer architecture, with a structured component sweep
  methodology now documented
- Commercial conduct including order fulfillment patterns, customer-
  protection-process engagement, and observed account-identity-tagged
  restrictions on the direct-store website
- Regulatory layer including the dispute-handling pattern observed in
  the present case, comparative analysis against industry consumer-
  protection norms, and references to the active *Mickey v. Seel, Inc.*
  class action regarding the third-party insurance program your
  customer service team has been routing customers through

Would it be useful for Creality to review the full technical findings
in advance of broader circulation? OWG can share the work under
appropriate confidentiality terms if of value to your product team or
legal counsel. Manufacturers in your category sometimes find pre-
disclosure review valuable for product-roadmap planning, customer-trust
positioning, remediation of findings before they are widely circulated,
or referenced in regulatory contexts such as CISA supply-chain risk
management or FTC consumer-protection enforcement.

The OWG case file at this point includes:

- The K2 Plus firmware V1.1.5.5 network-forensic sweep (publicly
  available)
- An umbrella structural case file documenting three-layer
  extraction-disposition findings (publicly available; receiving
  active updates)
- A sibling investigation into the Seel third-party insurance program
  (publicly available; structurally relevant to your customer-service
  redirection pattern)
- The bank dispute affidavit set (sanitized public versions available
  at the same repository, with full versions held by the bank)
- A pending hardware-layer sweep with documented methodology
- Live-verification work confirming the architectural findings under
  controlled conditions

I am happy to coordinate disclosure timing, scope, and remediation
discussion if your team is interested in engaging substantively. The
present customer-service channel is not the appropriate venue for that
discussion; if you would like to refer the matter to product security,
legal, or executive contacts, I will be glad to engage through
whichever channel Creality prefers.

I will continue to keep you advised on the bank dispute as updates
arise.

Best regards,

Jay Puckett
Lead Investigator, Obsidian Watch Group
investigations@obsidianwatch.org

PGP fingerprint: `9267 A71E 3F0A 4EED 2F97 3F9B A3E2 52F2 4635 CC6C`

Available on `keys.openpgp.org` and `keyserver.ubuntu.com`.

---

## Tactical notes on the draft

### What the email signals to Creality without saying directly

1. **The dispute is going to win at the bank.** The supplemental
   affidavit with the gated-property fact and the SpeedX shifted-account
   evidence is in the bank file. Creality's customer-service team has
   no answer to that. The investigator is being courteous in the email
   but is operating from a position of expected chargeback victory.

2. **The merchant is being investigated as a structural matter, not as
   a one-customer grievance.** The OWG investigation framing positions
   Creality as the subject of investigative work that includes a public
   case file portfolio. The merchant's legal team will recognize this
   as a substantively different situation from a typical consumer
   complaint.

3. **The audit is real, documented, public-available-in-part, and
   reaching publication.** Creality's legal team can pull up the
   github.com/jpuckett11/CrealityK2Plus_sweep repository in real time
   and read the network-forensic sweep with the documented continuous
   camera capture, the cleartext MQTT to Alibaba, the hardcoded
   Chinese-NTP IP, the Tencent Hunyuan AI integration, and the live
   verification confirming the architectural posture under controlled
   conditions.

4. **The account-identity-tagged blocking finding is documented.** The
   email's reference to "observed account-identity-tagged restrictions
   on the direct-store website" tells Creality's team that the
   investigator has documented their merchant-side engineering of
   anti-investigator measures. They will recognize this as a
   substantively damaging public finding.

5. **The Seel pattern documentation is public.** The reference to the
   active class action against Seel signals that the investigator has
   documented the Seel structural fraud pattern publicly, including
   Creality's redirection of the present dispute through it.

6. **The CISA / FTC reference is the implicit regulatory leverage.**
   The email mentions both as possible reference contexts for the
   findings. Both are real, active, and accept consumer-hardware
   supply-chain submissions. Creality's legal team will read this as
   "this might end up at federal regulators."

7. **The offer is real.** OWG would, in good faith, share the work
   under appropriate confidentiality terms if Creality engaged
   substantively. This is the standard responsible-disclosure framing.
   The investigator is not blackmailing; the investigator is offering
   the standard pre-disclosure cooperation any security researcher
   would offer.

### What Creality's likely responses are, and how to handle each

1. **Silence**: most likely outcome. Their team has no template for
   this kind of communication and the legal review will take days.
   Continue with the bank dispute and the public-case-file work
   independently.

2. **Hostile cease-and-desist**: if Creality's legal team responds with
   takedown demands or threats, the investigator's posture should be:
   the public case file is sourced to first-party Creality documents
   (firmware, network captures of the device's own behavior, public
   BBB records, public court filings), is conducted under standard
   security-research norms, and is published in good faith. OWG is not
   defaming Creality; OWG is documenting Creality's own conduct.
   Cease-and-desist letters from this category of party are routinely
   ignored by security researchers under the well-established US
   doctrine that documenting and publishing facts about product
   behavior is protected.

3. **Substantive engagement**: low probability but ideal. If Creality
   refers the matter to product security or legal counsel and engages
   substantively, OWG should engage in good faith, accept a reasonable
   confidentiality framework, work toward remediation, and credit
   their engagement positively in the public case file.

4. **Settlement-of-dispute offer**: medium probability. Creality may
   offer to credit the disputed transaction (without admitting the
   merit of the dispute) in exchange for "ending the matter." This is
   not a remediation of the structural findings; the investigator
   should accept any chargeback resolution but maintain the OWG
   investigation publication trajectory independently.

5. **Quiet remediation**: low probability but worth watching. If
   Creality silently changes their behavior (e.g., removes the
   account-identity-tagged blocking, fixes the consent-flag default,
   moves the AI endpoint routing) without public engagement, OWG
   should document the changes in the case file as positive remediation
   while preserving the historical finding.

### When to send

The email is ready as-is. Optimal send timing:

- **After the bank chargeback resolves in the cardholder's favor** -
  removes the dispute-related uncertainty from the disclosure context
- **After the §1.13.9 MITM analysis is documented** - the audit
  reference is even stronger with the 30-min MITM capture documented

But sending now is also reasonable; the case file is already
substantial, and the audit is approaching publication-ready under any
reasonable definition of that phrase.

### What to NOT do

- **Do not threaten directly.** The "separately and unrelated" framing
  is critical. Any direct linkage between the disclosure and the bank
  dispute creates legal exposure.
- **Do not negotiate the chargeback through this channel.** The bank
  is the chargeback adjudicator; Creality has no authority over the
  outcome.
- **Do not provide the unsanitized affidavits or the unsanitized
  device identifiers via this channel.** Those are in the bank's
  hands; Creality's contact channel does not need them.
- **Do not commit to a specific publication timeline.** "Approaching
  a substantial documentation state" is the right framing. Specific
  timelines create pressure on both sides that doesn't help the
  investigation.

---

*Draft ready 2026-06-24. PGP-signature on the final sent version
recommended (same key as the dispute affidavits).*
