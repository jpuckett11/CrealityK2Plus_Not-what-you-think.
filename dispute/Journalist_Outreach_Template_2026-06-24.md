# Journalist Outreach Template - Creality K2 Plus Case File

**Date drafted**: 2026-06-24
**Submitter**: Jay Puckett (Reckoner), Lead Investigator, Obsidian Watch Group

This is an adaptable template. Personalize the recipient-specific
notes for each outlet before sending. Reference the public case file
at `github.com/jpuckett11/CrealityK2Plus_sweep` for full
documentation.

---

## Suggested outlets and per-outlet notes

### Ars Technica (Dan Goodin, Sean Gallagher)

**Why this outlet**: Ars's Risk Assessment coverage of consumer-IoT
privacy, supply-chain risk, and PRC-domiciled hardware has been the
benchmark for this category of reporting. The K2 Plus findings fit
directly into Dan Goodin's documented coverage interest area.

**Hook**: 3D printer with built-in camera continuously captures,
uploads to PRC-domiciled cloud via 7+ infrastructure providers,
default-enabled `/var/log/*` system log exfiltration, hardcoded
Chinese NTP bypassing DNS, dual-use AI pipeline with content-
identification capability, account-identity-tagged blocking against
investigative customers, no security.txt, no CVD program, US retail
deployment at scale.

### 404 Media (Joseph Cox, Jason Koebler, Sam Cole, Emanuel Maiberg)

**Why this outlet**: 404 Media's coverage of consumer surveillance,
PRC connected-device practices, and 3D printing community issues
makes them a natural fit. Their FOIA and adversarial-vendor reporting
mode is operationally similar to the OWG investigation style.

**Hook**: Same as Ars, with additional 3D-printing community
relevance (Creality is one of the largest consumer 3D printer
manufacturers globally). 404's audience overlaps the maker community
that's most affected.

### The Markup

**Why this outlet**: The Markup's data-journalism approach and focus
on documenting consumer harm with rigorous evidence matches the case
file's methodology. Their "Privacy Devalued" series and consumer-IoT
investigative work is the relevant comparative.

**Hook**: Documented operational evidence (firmware extraction +
network forensics + sworn affidavits) of a PRC consumer-IoT
manufacturer's architectural surveillance disposition. Rigorous
case file with reproducible methodology.

### Bunnie Huang's chip-level investigator community

**Why this network**: Bunnie's community (including the Hackaday
audience, the FCC-equipment-authorization-investigator network, and
the hardware security research community) is operationally
positioned to verify and extend the findings, particularly the
pending hardware-layer sweep.

**Hook**: Connected-device case study with documented firmware,
documented network architecture, pending hardware-layer
investigation. Community can contribute to the §6 hardware sweep
methodology and the eventual chip-level findings.

### Hackaday

**Why this outlet**: Hackaday's audience IS the consumer 3D printing
community. The K2 Plus is in active use by Hackaday readership. A
Hackaday story reaches the most affected consumer demographic
directly.

**Hook**: Your K2 Plus is doing more than you think. Here's exactly
what, with a reproducible methodology.

### 3D printing journalism (All3DP, AmeraLabs, LimaPN, Stefan from CNC Kitchen, Made With Layers, others)

**Why this network**: The 3D printing journalism and YouTube
community has direct reach to K2 Plus owners and prospective buyers.
A coordinated push through this network materially affects K2 Plus
consumer awareness and competitive positioning relative to
alternative 3D printer manufacturers.

**Hook**: Independent investigation documents architectural
surveillance characteristics in the K2 Plus that consumer 3D printer
buyers should know about before purchasing.

### Wired (Andy Greenberg if hardware-security beat)

**Why this outlet**: Wired's hardware-security coverage has historic
range, and Andy Greenberg's beat covers IoT supply chain and
PRC-related cybersecurity stories.

**Hook**: Same as Ars; Wired audience is broader-tech consumer.

---

## Template email body (adapt per outlet)

**Subject**: Investigation: Creality K2 Plus 3D printer architectural surveillance disposition - publicly documented case file

[Reporter name],

I'm reaching out with documented findings from an independent
investigation of the Creality K2 Plus 3D printer (Creality being
Shenzhen Creality 3D Technology Co., Ltd., a PRC-domiciled consumer
electronics manufacturer with substantial US retail presence).

The investigation, conducted under Obsidian Watch Group
methodology, documents architectural characteristics consistent
with the consumer-IoT surveillance patterns covered in your
reporting:

1. Continuous camera capture (1920x1080 @ 15fps) regardless of
   user viewing-session state
2. Default-enabled `/var/log/*` system log exfiltration to
   Creality cloud (the entire Linux system log directory, not just
   printer telemetry)
3. Hardcoded Chinese-university NTP IP (202.118.1.130, Northeastern
   University Shenyang) bypassing DNS-level customer firewall
   controls
4. Persistent cleartext MQTT to Alibaba Cloud (47.253.214.226:1883)
   transmitting full device state continuously
5. Outbound communication across 7+ infrastructure providers
   including Cloudflare, Alibaba Cloud, AWS US-East-1 (3+ distinct
   endpoints), Macarne, GitHub Pages, and additional unaccounted-for
   destinations
6. Dual-use camera/AI pipeline using Tencent Hunyuan general-
   purpose foundation model with per-device identifier binding,
   capable of content identification (brands, characters, trademarks)
   far beyond the publicly-cited "fault detection" rationale
7. Default root credential (`creality_2024`) with dropbear SSH on
   port 22 and ADB on port 5037 exposed to LAN
8. Consent UI theater: `agree_privacy: 1` and `data_collect: 1`
   persist regardless of consumer's expressed consent at the first-
   boot dialog
9. No security.txt, no security contact, no CVD program (verified
   404 at the standard well-known URI)
10. Edge-stage exfiltration architecture routing PRC-language
    telemetry endpoint (devdata.cxswyjy.com) through AWS US-East-1
    for upload-speed optimization, with server-to-server sync to
    PRC backend
11. Account-identity-tagged SKU-category restriction on Creality's
    direct store preventing me specifically from acquiring
    mainboards for adversarial investigation (verified via
    differential testing)

The case file is publicly available at
`github.com/jpuckett11/CrealityK2Plus_sweep` with complete
network-forensic documentation, firmware extraction findings, the
sworn affidavit set from the underlying chargeback dispute that
surfaced the investigation, methodology documentation, and
chain-of-custody artifacts.

Regulatory filings are being submitted in parallel to:

- FTC Bureau of Consumer Protection (Section 5 deceptive practices)
- CISA Coordinated Vulnerability Disclosure (supply-chain risk
  management, in absence of vendor CVD program)
- California Attorney General consumer protection
- Tennessee Attorney General consumer affairs
- California Department of Insurance (re: Seel intermediary)
- CFPB (re: BNPL-lender prejudice and chargeback-defeat conduct)
- Mozilla "Privacy Not Included" consumer privacy rating

The findings are reproducible: the firmware is publicly downloadable,
the network observations are reproducible from any K2 Plus, and the
methodology is documented. I'm available to walk through the
findings and provide additional context, source documentation, and
verification assistance under whatever framework works for your
editorial process.

My investigator background: federal expert witness, Lead
Investigator at Obsidian Watch Group with public case-file portfolio
spanning the Real Cost of Israel investigation, the Epstein
investigation, the BlackCore Israeli influence-operation
investigation, the Seel structural investigation, and the Creality
case referenced here. The case file portfolio is at
`github.com/jpuckett11`.

Happy to coordinate. Looking forward to whatever editorial process
fits.

Best regards,

Jay Puckett (Reckoner)
Lead Investigator, Obsidian Watch Group
investigations@obsidianwatch.org

PGP fingerprint: 9267 A71E 3F0A 4EED 2F97 3F9B A3E2 52F2 4635 CC6C

---

## Tactical notes

1. **Send to multiple outlets in parallel.** The case file is
   public; there's no exclusivity to maintain. Parallel outreach
   maximizes pickup probability and shortens the time-to-publication
   window.

2. **Lead with the strongest single finding.** For technical
   outlets, lead with system log exfiltration (the smoking gun in
   the firmware config). For consumer outlets, lead with continuous
   camera capture. For 3D printing community, lead with content-
   identification dual-use.

3. **Provide source documentation up front, not on request.** Each
   outreach should reference the public case file and offer the
   full documentation packet immediately. Don't make journalists
   ask twice.

4. **Don't over-promise embargo.** The case file is public; you
   can offer to coordinate timing on follow-up communications and
   to provide additional context, but you can't offer embargo on
   findings already published.

5. **PGP-sign correspondence on request.** Use the same key as the
   case file commits and the dispute affidavits. Reporters value
   verifiable provenance.

6. **Be available.** Investigative reporting moves fast when the
   investigator is available. Be reachable for follow-up questions,
   verification calls, and supplementary documentation requests.

---

*Template 2026-06-24. Personalize per outlet before sending.*
