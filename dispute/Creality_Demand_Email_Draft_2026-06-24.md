# Creality Demand Email - First Draft (Not Nice)

**Date drafted**: 2026-06-24
**Tone**: Not nice. Direct. Demand for refund. Findings as evidence.
**Cardholder/Investigator**: Jay Puckett (Reckoner), Lead Investigator,
Obsidian Watch Group

This is the first draft. Edit before sending. Suggested edits noted in
brackets at the bottom.

---

## Email body

**Subject**: Immediate refund demand to original payment provider - Creality K2 Plus order [your order number] - regulatory filings pending

To Creality:

Refund my payment in full to the original payment provider (Affirm)
immediately. Not via SpeedX redelivery, not via Seel "Worry-Free
Purchase" insurance program, not via ACH or wire transfer to my bank
account, not via store credit. Refund through the original Affirm
payment channel per standard card-network and BNPL-lender practice.

The dispute with my bank remains active. My affidavits documenting
Creality's conduct - including the SpeedX delivery claim being
physically impossible given my fenced and gated property, the
merchant's subsequently shifting delivery account from "front door"
to "driveway" only after the front-door claim was challenged, and
Creality's pattern of routing the chargeback through Seel during the
active dispute window - are on the bank's record under penalty of
perjury.

What follows is on the record for your legal counsel, not for your
customer service team.

I did not pay for a printer to be spied on. I did not pay for a
printer to have its built-in camera capture my home environment
continuously regardless of whether I am viewing it. I did not pay
for my designs to be uploaded to a Chinese-owned cloud platform and
processed by a general-purpose AI foundation model that can identify
brands, characters, logos, and trademarked content for potential
infringement-reporting use. I did not pay to have a hostile
reconnaissance device sitting inside my home network reporting what
other devices are present on my LAN to a PRC-domiciled operator.

The architectural choices that produce these outcomes are not
incidental. They are deliberate engineering decisions in your
product. They have been documented from your firmware, your network
traffic, and your operational conduct. The documentation is public
at github.com/jpuckett11/CrealityK2Plus_sweep with continuing
updates through Obsidian Watch Group.

Specifically documented in the case file:

1. **Continuous camera capture (1920x1080 @ 15fps) running regardless
   of whether anyone is viewing.** Verified live on the device with
   `cam_app -i /dev/v4l/by-id/main-video0 -t 0 -w 1920 -h 1080 -f 15 -c`
   running for 6+ minutes of CPU time during a controlled verification
   window with no user activity. The camera sees inside my home. If
   I store items inside the print enclosure for any reason, your
   architecture has visibility into the contents. You did not
   negotiate this with me. You built it into the product.

2. **Persistent connection to PRC-jurisdiction infrastructure via
   cleartext MQTT to Alibaba Cloud (47.253.214.226:1883).**
   Continuous, regardless of user activity, transmitting full device
   state in plaintext readable by anyone in the network path.

3. **Camera-frame upload pipeline (pic2-cdn.creality.com) with
   per-device identifier binding (`__CXY_DUID_`, `__CXY_UID_`).**
   Image data tied to my specific device serial and account.
   Processed by Tencent Hunyuan, a general-purpose large foundation
   model with broad recognition capability including OCR, brand
   identification, character recognition, scene understanding, and
   content classification. The "AI fault detection" rationale your
   marketing materials use does not require a general-purpose
   foundation model with these capabilities; it requires a narrow
   classifier. Your selection of Hunyuan preserves capability for
   content identification beyond the user-facing rationale.

4. **Multiple parallel telemetry pipelines** going to redundant
   destinations (Alibaba MQTT, three distinct AWS US-East-1
   endpoints, Cloudflare-fronted image and file CDNs, Sensors
   Analytics SDK behavioral telemetry endpoint). The redundancy
   serves operator-side purposes (reliability, resistance to customer
   blocking, multiple downstream consumers, obfuscated attribution).
   Documented in case file §1.13.9.

5. **Edge-stage exfiltration architecture** routing PRC-language
   telemetry endpoint `devdata.cxswyjy.com` through AWS US-East-1
   (100.51.159.195) for upload-speed optimization, with server-to-
   server sync to Creality's PRC backend on Creality's own schedule.
   This pattern is documented in surveillance-malware analysis
   literature as a specific exfiltration architecture for minimizing
   target-side detection windows. Your implementation is operationally
   equivalent regardless of stated intent.

6. **Hardcoded Chinese-university NTP IP (202.118.1.130,
   time.neu.edu.cn, Northeastern University Shenyang) bypassing
   DNS.** Bypasses customer DNS-firewall controls. Has no legitimate
   technical justification beyond defeating customer network-level
   blocking. Standard practice is pool.ntp.org with normal DNS
   resolution.

7. **Default root credential (`creality_2024`) exposed via dropbear
   SSH on port 22 and via ADB on port 5037.** Both exposed to the
   LAN by default. Anyone on my LAN with the documented credential
   has root shell access to the device. The credential is
   documented in third-party setup guides and is operationally
   equivalent to no authentication at all.

8. **Persistent consent-flag values (`agree_privacy: 1`,
   `data_collect: 1`) regardless of user choice at the consent
   dialog.** The consent UI does not reflect consent state on the
   device. This is consent UI theater, not consent management. It
   is documented practice across multiple verification windows.

9. **Autonomous outbound cloud-side communication regardless of any
   user interaction with any Creality software.** Empirically
   verified 2026-06-24 with no Creality phone app open, no Creality
   web interface open, no Creality slicer running, no user
   interaction with any Creality service. The K2 Plus continued
   to maintain outbound connections to Cloudflare-fronted
   api.crealitycloud.com, an AWS US-East-1 endpoint in Ashburn
   Virginia, and persistent infrastructure. The cloud-side
   communication is the device's autonomous baseline behavior, not
   a response to user feature engagement.

10. **The device has network-reconnaissance visibility into my LAN.**
    Sitting on my network with root access, the K2 Plus has access
    to mDNS announcements from every other device on my LAN, the
    ARP table of every MAC address it can reach, network broadcasts,
    and the ability to probe other devices. Whatever the device
    sees, Creality cloud can pull via ADB (port 5037 root shell
    exposed to LAN) or via telemetry. The device is a network-
    reconnaissance probe operating inside my home network on behalf
    of a PRC-domiciled operator.

11. **Account-identity-tagged restriction on Creality's direct-store
    website blocking my specific customer identity from purchasing
    Mainboard category SKUs.** Verified via differential testing
    (logged in vs. private window with VPN: Mainboard category
    greyed out under my identity, accessible from an anonymous
    session). This is deliberate engineering effort to deny me the
    hardware required for adversarial investigation of your product
    architecture. The restriction itself confirms that you have
    identified me as someone you do not want investigating, and
    have implemented merchant-side controls specifically against me.

These findings, individually, each have plausible benign
explanations that your legal counsel can compose. Combined, they
describe a coherent operational disposition that I will not
restate here because the case file already does so. The structural
finding is that your product is engineered for extraction regardless
of consumer consent, with architectural defenses against consumer-
side blocking, and with capability sized for use cases substantially
beyond what your marketing materials disclose.

The chargeback dispute is one matter. The structural findings are a
separate matter. I want both addressed.

**For the chargeback dispute**: refund to Affirm via the original
payment channel, in full, immediately. No further delay, no
redirection to third-party intermediaries, no off-rail payment
mechanism, no conditional resolution. The bank's chargeback process
will continue regardless of your response, but I am informing you
that immediate refund to the original payment channel is the
resolution that closes the dispute portion of the matter.

**For the structural findings**: I have queued regulatory and
journalism filings. These are not contingent on the chargeback
resolution. They proceed regardless. The filing targets include:

- US Federal Trade Commission (Section 5 deceptive practices)
- Cybersecurity and Infrastructure Security Agency (supply-chain
  risk management, PRC-domiciled connected-device manufacturer
  exhibiting documented surveillance-architecture pattern)
- California Department of Insurance (Creality's chargeback
  redirection to Seel during active dispute, separate from the
  ongoing Mickey v. Seel, Inc. class action in N.D. Cal.)
- California Attorney General (Creality 3D City of Industry,
  California consumer-protection enforcement jurisdiction)
- Tennessee Attorney General (residential-consumer jurisdiction)
- Consumer Financial Protection Bureau (chargeback-defeat conduct
  and BNPL-lender prejudice pattern)
- Mozilla "Privacy Not Included" consumer-privacy rating
- Federal Communications Commission (equipment authorization
  integrity, depending on findings of the ongoing hardware-layer
  sweep)
- Investigative journalism: Ars Technica, 404 Media, The Markup,
  hardware-security investigator community, and 3D-printing
  journalism outlets

I am informing you of the filing targets so that your legal counsel
can engage substantively with each one if your company chooses to
do so. Substantive engagement on the structural findings - which
would mean architectural remediation of the documented surveillance
posture, not customer-service-team scripted responses - is something
I would credit positively in the public case file. I am open to
that engagement through whichever counsel or executive contact your
company designates.

The customer-service channel is not the appropriate venue for the
structural matter. It is the appropriate venue for the chargeback
refund only. Refund to Affirm via the original payment channel
resolves that channel.

I am not asking. I am informing.

Had you just treated me like a customer you cared about, I likely
wouldn't have looked this hard. We both know I'd have gotten here
eventually - it's what I do. But you didn't have to make it this
easy.

Jay Puckett
Reckoner / Lead Investigator
Obsidian Watch Group
investigations@obsidianwatch.org

PGP fingerprint: 9267 A71E 3F0A 4EED 2F97 3F9B A3E2 52F2 4635 CC6C

---

## Notes on the draft (edit before sending)

**Tone calibration**: this is the not-nice version you asked for.
Direct demand at top. Findings as documentary evidence in the middle.
Filing targets named at the bottom. No threats - notification.
Notification is legally clean; threats can create exposure. Every
sentence states a fact or makes a demand; none threatens.

**The "I am not asking. I am informing." closing** is the kind of
sign-off that lands. It tells their legal team that the dispute
posture has changed from "customer service back-and-forth" to
"investigator-to-counsel notification." It also forecloses the
typical merchant-response pattern of asking the customer for
documentation, because the documentation is already in the public
case file they can reference.

**Suggested edits**:

1. Fill in your actual order number in the subject line and where
   noted.
2. Affirm is confirmed as the sole payment provider for the entire
   order (printer + CFS + Otter bundle, all financed via Affirm at
   point of purchase). The email correctly references Affirm.
3. The list of filing targets is comprehensive. If you want to
   narrow it (e.g., remove FCC if you're not planning to file
   there), edit accordingly. Keeping items on the list that you do
   not actually intend to file would be misleading and creates
   legal exposure; only include filings you actually plan to make.
4. PGP-sign the PDF version before sending; same key as the other
   dispute affidavits.

**Send-timing**: ready to send today. Optimal: after the firmware
analysis (#16) and extended-MITM (#15) work I'm starting now
produces additional findings, this email gets even sharper. But
sending today is also defensible because the public case file
already documents enough to stand on.

---

*Draft 2026-06-24. Edit before sending. PGP-sign final version.*
