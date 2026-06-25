# FTC Consumer Protection Complaint - Creality (Shenzhen Creality 3D Technology Co., Ltd.)

**Date drafted**: 2026-06-24
**Submitter**: Jay Puckett (Reckoner), Lead Investigator, Obsidian Watch Group
**Email**: investigations@obsidianwatch.org
**PGP fingerprint**: 9267 A71E 3F0A 4EED 2F97 3F9B A3E2 52F2 4635 CC6C

**Filing destination**: Federal Trade Commission, Bureau of Consumer
Protection - via ReportFraud.ftc.gov (the standard public-facing
consumer complaint portal) AND a separate filing to the FTC
Division of Privacy and Identity Protection via
privacy@ftc.gov where supported by complaint policy.

---

## Summary

Creality (Shenzhen Creality 3D Technology Co., Ltd., US presence in
City of Industry, California) markets and sells the Creality K2 Plus
3D printer to US consumers with marketing materials describing
optional cloud features and AI-enabled fault detection. The actual
operational architecture documented in the device's publicly-
downloadable firmware and in network-forensic observation of an
operational unit constitutes:

1. Continuous data collection beyond what is disclosed to consumers
2. Persistent connection to PRC-jurisdiction infrastructure regardless
   of consumer cloud-feature engagement
3. Consent-bypass architecture (consent UI does not reflect consent
   state)
4. Account-identity-tagged restriction targeting investigative
   consumers
5. Coordinated chargeback-defeat conduct routing disputes through an
   unadmitted-insurer intermediary (Seel, Inc., subject of active
   class action *Mickey v. Seel, Inc.*, N.D. Cal. case 4:26-cv-01336)
6. Request for refund processing outside the original card-network
   payment rail, prejudicing both consumer chargeback protection and
   BNPL-lender (Affirm) loan recovery
7. No coordinated vulnerability disclosure program (no security.txt,
   no security contact, no published CVD policy)

These conduct categories implicate FTC Section 5 (deceptive and
unfair acts and practices) and consumer-privacy enforcement
authority. The submitter respectfully requests FTC review and
appropriate enforcement action.

The structural findings are documented in detail in the public
investigative case file at
`github.com/jpuckett11/CrealityK2Plus_sweep`. This complaint
summarizes the findings relevant to FTC enforcement scope.

---

## Submitter credentials

The submitter serves as Lead Investigator at Obsidian Watch Group
(OWG), an independent investigative organization with a public case
file portfolio. The submitter has federal expert witness testimony
experience including matters involving consumer hardware supply
chain and connected-device security. The submitter's investigator
profile is verifiable through standard channels.

The submitter is the cardholder of record for the disputed Creality
transaction that forms the operational basis of this complaint. The
submitter has personal-experience standing to document the conduct
described in this complaint.

---

## Section 1: Continuous data collection beyond disclosed scope

The K2 Plus is marketed as a 3D printer with optional cloud features
and AI-enabled fault detection. The operational reality documented
in the device's publicly-downloadable OTA firmware
(`CR0CN240110C10_V1.1.5.5`) and in network-forensic observation:

1. **Continuous camera capture**: the `cam_app` process runs
   continuously with the `-c` continuous mode flag, capturing
   1920x1080 at 15fps from `/dev/v4l/by-id/main-video0` regardless
   of whether any user is viewing the camera or any print is in
   progress. Verified live on the device.

2. **System log exfiltration**: per the device's configuration file
   `/etc/appetc/alchemistp/config.json`, the device is configured by
   default (`UploadFileFlag: 1`) to upload `/var/log/*` (the entire
   Linux system log directory including `/var/log/messages`, the
   syslog) to Creality cloud infrastructure. The uploaded files
   include kernel messages, authentication events, USB connect/
   disconnect events, network events, and every process that logs
   to syslog. This is operationally distinct from "printer
   telemetry."

3. **Image-frame upload pipeline**: dedicated CDN infrastructure
   (`pic2-cdn.creality.com` and `pic-cdn.creality.com`) configured
   for image data upload, with per-device identifier binding
   (`__CXY_DUID_` device serial and `__CXY_UID_` user identifier
   headers).

4. **Behavioral analytics SDK**: Sensors Analytics SDK (Chinese
   behavioral-analytics platform) integrated for event tracking at
   the `sa/data` ingest path.

5. **Persistent MQTT to Alibaba Cloud**: continuous connection to
   `47.253.214.226:1883` (Alibaba Cloud), cleartext, transmitting
   full device state continuously regardless of user activity.

6. **API polling at approximately 1.5-second intervals continuously**:
   per network observation across a 4.5-hour monitor window,
   ~10,800 DNS queries to `api.crealitycloud.com` (~40/minute, one
   every 1.5 seconds) sustained regardless of whether printing was
   occurring or user-app interaction was happening.

Creality's marketing materials do not disclose the scope of data
collected (system logs, continuous camera frames, behavioral
analytics events), the persistence model (continuous regardless of
user engagement), or the multiple parallel collection pipelines.
The disclosure-to-reality gap is material and consumer-facing.

---

## Section 2: Persistent connection to PRC-jurisdiction infrastructure

Creality is a PRC-domiciled entity headquartered in Shenzhen,
Guangdong Province, People's Republic of China. The device's
cloud-side communication routes to infrastructure subject to PRC
data-access law:

- PRC Cybersecurity Law (2017) compelled-cooperation framework
- PRC National Intelligence Law (2017), Article 7: "All
  organizations and citizens shall, in accordance with the law,
  support, assist, and cooperate with national intelligence efforts"
- PRC Data Security Law (2021) state-access provisions
- PRC Personal Information Protection Law (2021) state-access
  carve-outs

Documented destinations include:

- `mqtt.crealitycloud.com` -> 47.253.214.226 (Alibaba Cloud LLC,
  cleartext MQTT on port 1883)
- `time.neu.edu.cn` -> 202.118.1.130 (Northeastern University,
  Shenyang, China) - **hardcoded IP bypassing DNS controls**
- `api.crealitycloud.com` -> Cloudflare-fronted, with operator
  backend in PRC
- `devdata.cxswyjy.com` -> 100.51.159.195 (AWS US-East-1, but the
  domain is owned by Creality / Chinese-language acronym
  `cxswyjy` = ChuangXiang SanWei + YJY)
- `oss-cn-hangzhou.aliyuncs.com` - Alibaba OSS storage, Hangzhou
- `oss-us-east-1.aliyuncs.com` - Alibaba OSS storage, US-East-1
- `pre-tb-iot.crealitycloud.com:1883` - ThingsBoard IoT platform
  endpoint

The hardcoded NTP IP (202.118.1.130, Chinese university) is
operationally significant: it bypasses DNS-level customer firewall
controls. Standard practice is `pool.ntp.org` with normal DNS
resolution. The choice to hardcode a Chinese university NTP IP is
not justified by any technical requirement and is consistent with
defeating customer network-control mechanisms.

---

## Section 3: Consent-bypass architecture

Per FINAL_REPORT §1.1 of the public case file and verified live on
the device:

- The first-boot setup flow presents a consent dialog asking the
  user to accept the privacy / Terms of Use clause that explicitly
  enumerates collection of "password, passphrase, user ID, account
  ID, network ID, and password again"
- The submitter declined the consent dialog during initial setup
- The device's on-disk consent state file
  (`/mnt/UDISK/creality/userdata/config/system_config.json`)
  contains `agree_privacy: 1` and `data_collect: 1` regardless of
  the consumer's expressed consent decision
- The consent UI does not reflect consent state on the device

The conduct constitutes consent UI theater: the consent button
exists for the consumer to feel a choice has been offered, while the
underlying device state remains at the firmware default. This is a
deceptive practice in the FTC Section 5 sense if the user-facing
representation ("decline to consent") does not produce the user-
expected outcome (data collection ceases or is reduced).

---

## Section 4: Account-identity-tagged restriction

Per case file §2.5 and verified via differential testing 2026-06-24:

- Logged in to the submitter's normal Creality customer account on
  `creality.com`, the Mainboard SKU category in the parts navigation
  is greyed out and not selectable
- Other SKU categories (Hotend, Extruder, Nozzle, Build Plate,
  Camera, Enclosure, Screen, Dry Box, Maker Toy Kits, CFS) remain
  selectable in the same session
- In a Firefox Private Browsing window routed through a VPN exit
  (no cookies, no account login, distinct IP and browser
  fingerprint), the Mainboard category is fully selectable and the
  underlying inventory is purchasable

The differential rules out IP-level blocking, browser-fingerprint
blocking, and session-cookie blocking. The remaining mechanism is
account-identity-tagged SKU-category restriction at Creality's
account-database or merchant-side block-list layer.

The targeted SKU category (Mainboard) is the category containing the
hardware required for adversarial investigation of the device's
architecture. The restriction is engineered behavior, not default
merchant fraud-prevention behavior.

The conduct is documented merchant-side engineering effort to
prevent the submitter specifically from acquiring hardware for
investigation. This implicates FTC concern about retaliation against
consumers exercising their statutory consumer-protection rights
including investigation of merchant conduct.

---

## Section 5: Chargeback-defeat conduct through unadmitted-insurer
intermediary

The submitter initiated a bank chargeback dispute against Creality
on 2026-06-17 for an undelivered $700 product (Creality CR-Scan
Otter 3D scanner). The sworn affidavit set documents the underlying
dispute including the SpeedX-shipped package that did not arrive at
the submitter's fenced and gated property, the merchant's shifting
delivery account from "front door" to "driveway" after the gated-
property fact was raised, and the lack of carrier-side proof of
delivery.

On 2026-06-22, during the active period of the chargeback dispute,
Creality routed the dispute to Seel, Inc. (a Silicon-Valley-venture-
funded e-commerce post-purchase-protection platform) by sending the
submitter the message: "Hello, we have contacted Seel Insurance to
extend your case. Please submit your claim as soon as possible."

Seel, Inc. is the subject of an active class action,
*Mickey v. Seel, Inc.*, N.D. Cal. case 4:26-cv-01336, alleging Seel
operates as an unadmitted insurer in California in violation of the
California Insurance Code, through a wholly-owned captive insurance
subsidiary (Seel Insurance, Inc.) that does not actually insure
consumers but rather insures its parent against contractual exposure.

Seel's documented public-record claim-handling concerns relevant to
the consumer-protection complaint include:

- Documented case (Sitejabber consumer review) of Seel providing a
  fabricated Venmo transaction ID for a refund that did not actually
  occur; the customer verified with Venmo that the transaction ID
  was not associated with any real transaction
- Documented requirement for biometric face scans as a precondition
  for claim adjudication
- Documented pattern of indefinite "under review" status without
  resolution
- BBB complaint cluster matching the documented pattern

Creality's routing of the chargeback dispute through this
intermediary, during the active dispute window, is consistent with
operational chargeback-defeat conduct rather than genuine remediation.

---

## Section 6: Refund payment-rail bypass request

In subsequent correspondence regarding refund processing, Creality
requested that the submitter provide direct ACH, wire, or SWIFT
transfer information for the deposit of a refund outside the
original card-network payment channel. The submitter declined this
request in writing.

The request is operationally significant:

- Standard card-network refund-handling practice requires that
  refunds for card-funded purchases be processed back through the
  original payment method
- A refund processed via direct banking transfer outside the
  card-network rail does not reverse the original card-network
  transaction
- The merchant could subsequently represent to the consumer's bank
  that "a refund has been processed," using the direct-banking
  transfer (or a fabricated reference to it) to defeat the active
  chargeback
- The off-rail refund pathway also prejudices the BNPL lender
  (Affirm) whose loan principal was disbursed to the merchant: a
  refund deposited to the consumer's bank account does not return
  to Affirm and does not satisfy Affirm's loan recovery

The conduct is FTC-relevant because it implicates Section 5
deceptive practice and because it potentially affects consumer credit
through the BNPL relationship.

### Independent BNPL-lender validation of the underlying merchant-misconduct claim

On 2026-06-24 at approximately 07:34 CT, Affirm Inc. (NASDAQ: AFRM),
the BNPL lender of record for the underlying purchase,
independently reviewed the documented evidence supporting the
customer's claim and refunded the entire loan principal of
**$1,800.93** to the customer. The Affirm-side merchant of record
on the refund notification is identified as "**SP CREALITY USA**,"
which is the United States corporate presence of Shenzhen Creality
3D Technology Co., Ltd., located in City of Industry, California.

The refund was processed within approximately three hours of the
customer's demand email to Creality (sent at approximately 04:30 CT
the same day) and approximately ninety minutes after Creality
wiped the customer's Creality.com account profile (documented
separately under the Account Wipe / Spoliation event).

Affirm is a publicly-traded BNPL lender with professional legal and
dispute-analysis teams and merchant-account management staff. Affirm
maintains internal review criteria for full-loan refund decisions
and the decision is contestable by the merchant. The decision to
refund the entire loan principal (rather than a partial refund
covering only the SpeedX-undelivered portion) reflects Affirm's
internal review conclusion that:

- The customer's merchandise-not-as-described claim was sufficient
  to flip the entire purchase, not only the non-delivered portion
- The documented evidence supporting the merchant-misconduct claim
  was sufficient to invoke FCBA § 1666i claims-and-defenses
- Affirm is positioned to (and presumably intends to) pursue
  Creality through their merchant-account dispute process for
  recovery

The Affirm decision is **third-party professional validation** that
the documented case meets the threshold for BNPL-lender intervention
under FCBA § 1666i. It is independent of the customer's own
assessment of the case and of any subsequent regulatory or judicial
proceeding. It is offered to the FTC as documentary support for the
Section 5 deceptive-practice finding the FTC is asked to consider.

The full Affirm-refund documentation is preserved in the case file
at `github.com/jpuckett11/CrealityK2Plus_sweep/case-files/` (Affirm
Full-Loan Refund - Independent BNPL Validation of Documented Case).

---

## Section 7: No coordinated vulnerability disclosure (CVD) program

Creality does not maintain a documented CVD program:

- No `security.txt` file at the standard well-known URI per RFC
  9116 (verified 2026-06-24: returns HTTP 404)
- No security contact email surfaced on consumer-facing properties
- No CVD policy document
- No bug bounty program (HackerOne, Bugcrowd, etc.)
- No vendor security advisory process documented

The absence of a CVD program is industry-substandard per CISA's
own published guidance recommending manufacturers maintain such
programs. The absence pairs with the architectural findings to
establish that security researcher findings against Creality have
no vendor-side responsible-disclosure channel and default to public
publication.

---

## Section 8: Requested FTC action

The submitter respectfully requests FTC consideration of:

1. **Investigation under Section 5 deceptive practices**: the
   disclosure-to-reality gap between Creality's consumer-facing
   marketing and the actual operational architecture; the consent
   UI theater pattern; the chargeback-defeat conduct.

2. **Privacy enforcement**: per-device identifier binding on
   continuous data collection without specific disclosure; system
   log exfiltration default-enabled; behavioral analytics SDK
   integration without specific disclosure.

3. **Connected-device security guidance application**: per FTC's
   own guidance on IoT security, default credentials, persistent
   cloud-side connectivity, and lack of CVD program represent
   substandard manufacturer practice.

4. **Cross-referral to CISA** for supply-chain risk management
   review of a PRC-domiciled connected-device manufacturer
   exhibiting documented architectural extraction disposition.

5. **Cross-referral to state Attorney General consumer-protection
   divisions** (California for Creality's US presence; submitter's
   residential jurisdiction for the underlying dispute).

6. **Public consumer advisory** about K2 Plus and related Creality
   products to inform other consumers of the documented architecture
   so consumers can make informed purchase decisions.

---

## Available documentation

The following documentation is available to FTC investigators:

- The public case file at `github.com/jpuckett11/CrealityK2Plus_sweep`
  with complete network-forensic documentation, firmware extraction
  findings, and chain-of-custody artifacts
- The sworn affidavit set documenting the underlying dispute
  (2026-06-17 original, 2026-06-23 first supplemental documenting
  Seel redirection and ACH/wire request, 2026-06-24 second
  supplemental documenting SpeedX delivery account and gated-
  property facts) - all PGP-signed by the submitter's published
  key
- The original publicly-downloadable Creality K2 Plus OTA firmware
  image (`CR0CN240110C10_V1.1.5.5`, ~142 MB) and the extracted
  rootfs for verification
- Pcap files from the network-forensic captures documenting the
  observed behaviors
- The Affirm investigator briefing documenting the BNPL-lender
  prejudice pattern

---

## Contact

Jay Puckett (Reckoner)
Lead Investigator, Obsidian Watch Group
investigations@obsidianwatch.org

PGP fingerprint: 9267 A71E 3F0A 4EED 2F97 3F9B A3E2 52F2 4635 CC6C

Available on `keys.openpgp.org` and `keyserver.ubuntu.com`.

PGP-signed version of this complaint available on request and will
be provided with the final filing.

---

*Draft 2026-06-24 in preparation for FTC submission. PGP-sign final
version before filing. Submission via ReportFraud.ftc.gov public
portal and privacy@ftc.gov direct channel where supported.*
