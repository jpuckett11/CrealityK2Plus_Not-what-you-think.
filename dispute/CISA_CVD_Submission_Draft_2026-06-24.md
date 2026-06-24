# CISA Coordinated Vulnerability Disclosure Submission - Creality K2 Plus 3D Printer

**Submission destination**: CISA Vulnerability Disclosure Coordination
via `https://www.cisa.gov/coordinated-vulnerability-disclosure-process`
and alternate channel `central@cisa.dhs.gov`
**Date drafted**: 2026-06-24
**Submitter**: Jay Puckett (Reckoner), Lead Investigator, Obsidian Watch Group
**Email**: investigations@obsidianwatch.org
**PGP fingerprint**: 9267 A71E 3F0A 4EED 2F97 3F9B A3E2 52F2 4635 CC6C

---

## Submission category

Connected-device supply-chain security finding. PRC-domiciled
consumer-IoT manufacturer (Creality / Shenzhen Creality 3D Technology
Co., Ltd.) ships consumer 3D printer (K2 Plus) with documented
architectural characteristics consistent with the data-exfiltration-
resilience patterns documented in surveillance-malware analysis
literature, plus default-credential exposure, ADB exposure to LAN,
and lack of coordinated vulnerability disclosure program.

The submission is structured as a coordinated vulnerability
disclosure because Creality maintains no security.txt and no
published CVD process; the standard responsible-disclosure path is
unavailable for findings against this manufacturer's products.

---

## Threat actor and jurisdictional framework

The manufacturer is PRC-domiciled (Shenzhen, Guangdong Province,
China). PRC-domiciled entities are subject to:

- PRC Cybersecurity Law (2017) compelled-cooperation framework
- PRC National Intelligence Law (2017), Article 7: compelled support
  of national intelligence work
- PRC Data Security Law (2021) state-access provisions
- PRC Personal Information Protection Law (2021) state-access
  carve-outs

The threat actor category is therefore the operating-from-PRC-with-
compelled-cooperation-obligation category that CISA supply-chain
risk-management framework documentation addresses generally. This
submission documents an operational instance of that category at the
consumer-IoT device level.

---

## Affected products and US deployment scale

Affected product family: Creality K2 Plus 3D printer (Allwinner T113-S3
SoC, OpenWrt + Klipper + Moonraker, OTA firmware version V1.1.5.5 and
related variants per OEM model code `CR0CN240110C10`).

Related products in the same architectural family: Creality K2,
Creality K2 Pro, additional Creality consumer printers using the same
cloud architecture (api.crealitycloud.com, mqtt.crealitycloud.com,
shared firmware lineage).

US deployment scale: tens to hundreds of thousands of units across
the K2 product line based on retail availability through Amazon,
direct-Creality store, MicroCenter, MatterHackers, 3D Printers Online
Store, and other US e-commerce channels. Cross-reference: Creality
Cloud's documented user base (millions of users globally) plus the
US market share of consumer 3D printing.

Federal personnel deployment: federal employees with personal hobby
3D printers, federal contractors with hobby or prototyping printers,
and any federal-purchaser deployment that may exist for prototyping
or model-making applications. The K2 Plus is in active US retail
distribution.

---

## Findings summary

### Architectural exfiltration disposition

The device's outbound communication architecture extends across at
least seven distinct infrastructure providers:

- Cloudflare (api.crealitycloud.com, file2-cdn.creality.com,
  pic2-cdn.creality.com, pic-cdn.creality.com, NTP)
- Alibaba Cloud (MQTT at 47.253.214.226:1883, additional MQTT IPs,
  NTP, OSS storage buckets in both Hangzhou and US-East-1)
- AWS US-East-1 (at least 3 distinct EC2 endpoints documented:
  100.51.159.195, 52.22.154.99, 54.227.8.154, serving the
  PRC-language `devdata.cxswyjy.com` telemetry endpoint and
  additional unaccounted-for services)
- Macarne (budget hosting, kc1cloud.macarne.com / 23.155.72.147,
  unaccounted-for service)
- GitHub Pages CDN (185.199.111.153, unaccounted-for service)
- Northeastern University, Shenyang (`time.neu.edu.cn` /
  202.118.1.130) - **hardcoded IP bypassing DNS controls**
- Additional Cloudflare endpoints (104.26.13.205, unaccounted-for)

The architectural choices documented in the publicly-downloadable OTA
firmware include:

- Default-enabled exfiltration of `/var/log/*` (Linux system log
  directory including syslog) per the device's
  `/etc/appetc/alchemistp/config.json` configuration file
- Continuous camera capture (`cam_app -c`, 1920x1080 @ 15fps) running
  regardless of viewing-session state, with frames available to a
  dedicated upload CDN
- Per-device identifier binding on all uploaded content (CXY device
  serial and CXY user ID headers)
- Persistent connection to PRC-jurisdiction infrastructure
  (Alibaba MQTT) in cleartext on port 1883
- Hardcoded Chinese-university NTP IP that bypasses DNS-level
  customer firewall controls
- ThingsBoard IoT platform underlying the device-management layer
- API polling at approximately 1.5-second intervals sustained
  continuously regardless of consumer interaction state

### Edge-stage exfiltration pattern

The device's egress architecture is operationally equivalent to the
edge-stage exfiltration pattern documented in surveillance-malware
analysis literature:

- Stage 1 (visible to consumer network observer): telemetry, image
  data, and other content uploads to Western cloud edge infrastructure
  (AWS US-East-1, Cloudflare global edge). Uploads complete fast due
  to geographic proximity; consumer-side observation window is short.
- Stage 2 (not visible to consumer network observer): data
  uploaded to Western cloud edge is synced server-to-server to
  Creality's PRC backend on Creality's own schedule. The consumer's
  network is no longer involved.

This architecture minimizes the consumer-side detection window,
hides the operator's actual location behind Western cloud
infrastructure, and is technically indistinguishable from legitimate
edge-acceleration patterns. The structural finding is that the
architecture serves operator-side speed and opacity, not
consumer-service routing.

### Dual-use camera/AI pipeline

The device's camera + Tencent Hunyuan general-purpose foundation
model + dedicated image CDN + per-device identifier binding
constitutes infrastructure operationally capable of:

- Continuous content identification with per-customer attribution
  (e.g., IP-infringement enforcement data collection)
- Industrial-intelligence extraction (prototype, replacement-part,
  proprietary-design imagery)
- General-purpose visual surveillance of the device's environment

The publicly-cited rationale ("AI fault detection during printing")
does not require continuous capture, dedicated image CDN, per-device
binding, or a general-purpose foundation model. The architectural
choices align with the broader use cases at the capability level
regardless of stated intent.

### Default credentials and ADB exposure

- Default root credential `creality_2024` (SHA-256-crypt hash
  identical across all units shipped on this firmware version, third-
  party-documented and verifiable via the publicly-downloadable OTA
  image)
- dropbear SSH listening on port 22, accepting the default credential
- adbd listening on port 5037, exposed to LAN, with Creality-side
  authorized keys presumably preinstalled (otherwise Creality's own
  cloud-side tooling could not exercise the ADB management surface)

Anyone on the LAN with the documented credential has root SSH access.
Anyone with the Creality-authorized adb_keys has ADB-shell root
access at the LAN level.

### Consent-bypass and merchant-side adversarial blocking

- Consent dialog UI does not reflect consent state on the device:
  `agree_privacy: 1` and `data_collect: 1` persist regardless of
  consumer's expressed consent decision
- Account-identity-tagged SKU-category restriction on the Creality
  direct-store website, targeting individual customer identities to
  prevent acquisition of mainboard hardware required for adversarial
  investigation

### No coordinated vulnerability disclosure program

- No security.txt at the standard well-known URI (verified 404)
- No security contact published
- No CVD policy document
- No bug bounty program

---

## Coordination request

CISA Coordinated Vulnerability Disclosure is requested for the
following purposes:

1. **Supply-chain risk management review** of a PRC-domiciled
   consumer-IoT manufacturer exhibiting documented architectural
   characteristics consistent with surveillance-architecture patterns,
   with US deployment at consumer scale.

2. **Cross-coordination with FCC equipment authorization integrity
   processes**. The Creality K2 Plus's FCC equipment authorization
   record can be checked against the documented hardware behavior
   (multiple parallel cloud endpoints, hardcoded NTP IPs, ADB
   exposure) to assess whether the as-shipped configuration matches
   the as-authorized configuration. Discrepancy would be an FCC
   equipment authorization integrity matter.

3. **Federal personnel advisory consideration**. Given the documented
   architecture and the consumer-scale deployment that likely
   includes federal-personnel personal devices, a federal advisory
   may be warranted. CISA's existing advisory infrastructure is the
   appropriate channel.

4. **Coordination with FTC Bureau of Consumer Protection**. A
   parallel complaint is being filed with FTC under Section 5
   deceptive-practices and connected-device-security guidance.
   CISA-FTC coordination on shared findings would be appropriate.

5. **Marketplace and import escalation**. The K2 Plus is in active
   US e-commerce distribution. Whether the documented configuration
   should remain importable into the US under existing supply-chain
   guidance is a CISA-relevant question.

6. **Documentation contribution**. The case file's structural
   framework (extraction-disposition architecture characterization,
   edge-stage exfiltration pattern documentation, dual-use camera/AI
   pipeline analysis) is potentially useful to CISA's broader
   consumer-IoT supply-chain guidance development. The submitter
   offers the documentation as a contribution to that guidance work.

---

## Researcher credentials

Reckoner / Jay Puckett, federal expert witness, Lead Investigator at
Obsidian Watch Group, author of the OWG investigation portfolio
including the Real Cost of Israel investigation, the Epstein
investigation, the BlackCore investigation, the Seel structural
investigation, and the Creality / K2 Plus investigation referenced
in this submission.

All hardware-forensic work performed on independently-acquired
devices purchased through normal commercial channels. No
unauthorized access to vendor or third-party infrastructure. The
publicly-downloadable OTA firmware image is analyzed; no exploits
were used to obtain device-internal access (root credential is
publicly documented).

Available case-specific references on request.

---

## Available documentation

Available to CISA reviewers under appropriate confidentiality
framework if needed:

- Complete public case file at
  `github.com/jpuckett11/CrealityK2Plus_sweep` with the umbrella
  structural case file, methodology documentation, chain-of-custody
  artifacts, and pcap files
- The publicly-downloadable Creality K2 Plus OTA firmware image
  (`CR0CN240110C10_V1.1.5.5`, ~142 MB) and the extracted rootfs
- Network-forensic captures documenting the observed behaviors
- Sworn affidavit set documenting the consumer-side experience
  (the underlying dispute that surfaced the architectural
  findings)
- Per-finding documentation including reproduction instructions
  using only the publicly-downloadable firmware image and a
  standard K2 Plus device

---

## Requested next step

CISA assignment of a coordination case number and an initial
substantive conversation with the appropriate CISA analyst for this
case category. Given the consumer-scale active deployment and the
documented architectural characteristics, coordination ideally
proceeds within 30 days of this submission.

If CISA determines that the findings warrant cross-referral to FBI,
state Attorneys General, or other federal coordination, the
submitter is available to provide supplementary documentation as
needed for those parallel processes.

---

## Submitter contact

Jay Puckett (Reckoner)
Lead Investigator, Obsidian Watch Group
investigations@obsidianwatch.org

PGP fingerprint: 9267 A71E 3F0A 4EED 2F97 3F9B A3E2 52F2 4635 CC6C

Available on `keys.openpgp.org` and `keyserver.ubuntu.com`.

PGP-signed version of this submission provided with the final
filing.

---

*Draft 2026-06-24 in preparation for CISA submission. PGP-sign final
version before filing. Submission via the CISA Coordinated
Vulnerability Disclosure portal at
`cisa.gov/coordinated-vulnerability-disclosure-process` and the
alternate channel `central@cisa.dhs.gov`.*
