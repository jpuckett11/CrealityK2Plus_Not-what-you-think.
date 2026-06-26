# CrealityK2Plus_sweep / OWG Creality Investigation Portfolio

Obsidian Watch Group investigation of Creality (Shenzhen Creality 3D
Technology Co., Ltd.) across three operational layers: technical, commercial,
and regulatory. Anchored on the original May 2026 K2 Plus network-forensic
sweep, expanded June 2026 with the umbrella structural case file, the
sibling Seel investigation surfaced by the active bank dispute, the dispute
affidavits documenting Creality's chargeback-defeat conduct, and the Affirm-
investigator briefing documenting the BNPL-lender prejudice pattern.

**Investigator:** Jay Puckett (Reckoner), Lead Investigator, Obsidian Watch
Group, investigations@obsidianwatch.org. PGP fingerprint
`9267A71E3F0A4EED2F973F9BA3E252F24635CC6C`. Key on `keys.openpgp.org` and
`keyserver.ubuntu.com`.

**Status (2026-06-24):** Active. Hardware-layer sweep in active planning.

---

## Consumer recommendation

Do not buy this device.

The K2 Plus exhibits a coordinated extraction disposition across technical, commercial, and regulatory layers that this investigation documents in detail. The disposition includes:

- Credential-collection clause in the Terms of Use enumerating password, passphrase, user ID, account ID, and network ID, with persistent default-on behavior regardless of the consumer's consent answer
- Account-identity-tagged restriction targeting individual consumers who document the product publicly
- Hardcoded foreign NTP IP that bypasses DNS-level blocking
- Chargeback-defeat conduct through redirection to Seel, Inc. (active class-action defendant in *Mickey v. Seel*, N.D. Cal. case 4:26-cv-01336)
- Refund payment-rail bypass requests defeating consumer chargeback protection and prejudicing BNPL lender recovery
- Parts unavailability for documented failure modes (F008_PC_FPC_V14 cable, anti-repair pattern observed across three components and corroborated by independent owner reports)
- Post-resolution one-directional retrieval-demand contact while remaining silent on consumer logistics questions, quantified at a roughly two-orders-of-magnitude responsiveness asymmetry
- Remote-brick capability demonstrated against the investigator's own device on or about 2026-05-30, with cam_app surveillance persistence through the bricked state

If you already own one, community paths to local-only operation are documented under `recovery/` and require root access plus network lockdown discipline at the gateway. Most consumers will not maintain that discipline indefinitely. The default factory firmware reverts mitigations on update.

The recommendation is unconditional: do not buy this device. Choose any of the open-firmware alternatives (Voron, Prusa with community firmware, Bambu with cloud disabled at the gateway) instead.

---

## Original sweep (Layer 1 technical evidence)

The Layer 1 technical evidence in the umbrella case file derives from this
repository's original work. The K2 Plus's setup flow asks the user to
consent to a Terms of Use clause that explicitly enumerates collection of
**password, passphrase, user ID, account ID, network ID, and password
again**. The investigator declined the ToU and documented what the device
actually does on the wire and in firmware regardless of the consent
answer.

- **[FINAL_REPORT.md](FINAL_REPORT.md)**: consolidated findings, observed
  facts, methodology, mitigations, and disclosure routes
- [METHODOLOGY.md](METHODOLOGY.md): exact commands, tools, and capture
  topology
- [CHAIN_OF_CUSTODY.md](CHAIN_OF_CUSTODY.md): artifact acquisition and
  handling
- [ARTIFACTS.md](ARTIFACTS.md): index of pcaps, firmware images, hashes,
  extracted files

Every observation is reproducible from an unmodified K2 Plus on the same
firmware. No exploits or vulnerabilities were used. All observations come
from watching the device's normal traffic or analyzing its publicly
downloadable OTA image. The default root password is publicly documented.

---

## Umbrella structural case file (the analytical lens)

The umbrella case file integrates the Layer 1 technical evidence above with
Layer 2 commercial evidence and Layer 3 regulatory evidence under a single
analytical framework: **coordinated extraction disposition maintained
regardless of consumer consent, consumer-protection-process friction, or
dispute-resolution-mechanism friction**.

The structural finding is that the same operational disposition is
observable across all three layers, with each layer's mechanism distinct but
the underlying disposition consistent. The disposition is the unit of
analysis.

- **[case-files/CREALITY - Coordinated Extraction Disposition Across Three
  Operational Layers.md](case-files/CREALITY%20-%20Coordinated%20Extraction%20Disposition%20Across%20Three%20Operational%20Layers.md)**

The case file cross-references the broader OWG portfolio (Atoto AI Box
investigation, Quectel compelled-cooperation framework, BlackCore / Rokh
Solis influence operations, October 7 Israeli political-leadership
decision pattern) and applies the same extraction-disposition framework
across operator-agnostic and state-alignment-agnostic contexts.

---

## Companion Seel investigation

Surfaced during the active Creality bank dispute when the merchant
redirected the investigator to Seel, Inc., a Silicon-Valley-venture-funded
e-commerce post-purchase-protection platform now the subject of an active
class action (*Mickey v. Seel, Inc.*, N.D. Cal. case 4:26-cv-01336)
alleging operation as an unadmitted insurer in California through a
wholly-owned captive subsidiary that does not actually insure consumers.

The OWG Seel case file documents the structural mechanism enabling both:

1. **Merchant-side chargeback defeat**: merchants redirect active
   chargebacks to Seel during the dispute window; Seel issues refund-
   confirmation messages (including documented fabricated transaction
   IDs, per Sitejabber-posted Venmo case) that merchants use to defeat
   the chargeback. Refunds processed via direct ACH or wire transfer
   outside original card-network rails.

2. **Consumer-side BNPL-default fraud**: bad-actor consumers purchase
   through BNPL lenders (Affirm, Klarna, Afterpay), purchase Seel
   coverage at checkout, file false claims, receive off-rail refund
   deposits to bank accounts rather than loan returns to lenders, and
   default on the BNPL loan. BNPL lender absorbs the full loss.

The same operational characteristics that enable vector 1 enable vector 2.

- **[case-files/SEEL - Captive Insurance Structure, Bidirectional Fraud
  Vector, and Chargeback Defeat Mechanism.md](case-files/SEEL%20-%20Captive%20Insurance%20Structure%2C%20Bidirectional%20Fraud%20Vector%2C%20and%20Chargeback%20Defeat%20Mechanism.md)**

---

## Bank dispute documentation

The bank chargeback against Creality has produced sworn affidavits and a
briefing for Affirm's investigator. These documents constitute the Layer 3
operational evidence in the umbrella case file and the operational example
in the Seel case file.

| Document | Purpose | Signature |
|---|---|---|
| `dispute/Creality_Dispute_Affidavit_2026-06-17_SANITIZED.pdf` | Original sworn chargeback affidavit | `.pdf.asc` |
| `dispute/Creality_Dispute_Supplemental_Affidavit_2026-06-23_SANITIZED.pdf` | Supplemental affidavit documenting Seel redirection and ACH/wire request | `.pdf.asc` |
| `dispute/Affirm_Investigator_Briefing_2026-06-24.pdf` | Briefing for Affirm's investigator on BNPL-lender prejudice pattern | `.pdf.asc` |

Markdown source for each document is provided alongside the PDF. PGP
signatures verifiable against the published public key at
`keys.openpgp.org` under the fingerprint above.

The dispute affidavits are sanitized for public distribution (network
device identifiers replaced with placeholders). Original signed unsanitized
versions remain in the cardholder's records and the bank's dispute file.

The Affirm investigator briefing is provided here as the OWG record of the
material delivered to Affirm; the Affirm investigator received the PDF
plus signature directly via standard correspondence channel.

---

## Pending hardware-layer investigation

The umbrella case file §6 documents the methodology for the upcoming K2
Plus hardware-layer sweep. Findings will be incorporated as a Layer 1
subsection (§1.13). The Creality direct-store website implements
account-identity-tagged restriction on the Mainboard SKU category
specifically targeting the investigator's customer identity; comparative
hardware samples are being acquired through the Amazon retail channel,
which remains unrestricted.

---

## Reproducibility and methodology

Every observation in the Layer 1 sweep is reproducible from an unmodified
K2 Plus on the same firmware (V1.1.5.5, OEM code `CR0CN240110C10`). The
capture commands are in METHODOLOGY.md. The firmware download URL and
SHA-256 are in ARTIFACTS.md.

The Layer 2 (commercial) and Layer 3 (regulatory) evidence is sourced to
the sworn affidavits, the documented public-record complaint patterns
(BBB, Trustpilot, Sitejabber), the active class-action litigation, and
direct merchant correspondence preserved in the dispute documentation.

The OWG investigation methodology applies throughout: provenance
discipline, source-cited claims, single-source flagging, no allegation of
criminal conduct against specific living individuals absent court records
or charging-authority documentation, structural-finding framing
independent of intent.

---

## Disclosure routes

Per the umbrella case file §5.7:

- **FTC Bureau of Consumer Protection**: credential-collection clause in
  the Creality Terms of Use, persistent consent default, PRC mirror
  infrastructure, Layer 3 chargeback redirection pattern
- **California Department of Insurance**: documented Seel structural
  finding in the context of the Creality dispute as operational example
  (independent of *Mickey v. Seel*)
- **Mozilla "Privacy Not Included"**: full Layer 1 finding set for
  consumer-IoT privacy ratings
- **CISA**: supply-chain risk-management for PRC-domiciled connected-
  device manufacturers exhibiting documented extraction-disposition pattern
- **Coordinated disclosure to Creality**: full finding set offered with
  defined embargo and remediation timeline; status of merchant engagement
  observable

---

## Contact

investigations@obsidianwatch.org

PGP fingerprint: `9267A71E3F0A4EED2F973F9BA3E252F24635CC6C`

Available on `keys.openpgp.org` and `keyserver.ubuntu.com`.

---

*Status: 2026-06-24. Initial K2 Plus sweep complete (May 2026). Umbrella
structural case file integrated (June 2026). Seel sibling investigation
established. Bank dispute affidavits and Affirm briefing PGP-signed.
Hardware-layer Layer 1 work in active planning.*
