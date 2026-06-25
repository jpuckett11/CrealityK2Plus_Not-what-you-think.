# NEU Shenyang NTP Hardcoding - Endpoint Subject-Matter Analysis

**Investigation**: Creality K2 Plus Coordinated Extraction Disposition
**Section type**: Endpoint analysis (supplemental case-file section)
**Date drafted**: 2026-06-24
**Author**: Jay Puckett (Reckoner), Lead Investigator, Obsidian Watch Group
**Status**: Initial draft, pending PGP signature
**Parent case file**: CREALITY - Coordinated Extraction Disposition Across Three Operational Layers.md

---

## Executive observation

The Creality K2 Plus firmware contains a hardcoded IP address (202.118.1.130, `time.neu.edu.cn`) that the device contacts for time synchronization at boot and at recurring intervals. The IP belongs to Northeastern University in Shenyang, Liaoning Province, People's Republic of China. The connection bypasses DNS resolution, which means consumer-level firewall controls based on hostname blocking cannot intercept it.

The choice of this specific endpoint is not technically necessary. Standard NTP convention is to contact `pool.ntp.org` or a regional NTP pool, which uses DNS resolution and rotates across many servers. Creality is headquartered in Shenzhen, Guangdong Province, approximately 2000 miles south of Shenyang. Routine geographic-proximity NTP selection would have produced a Guangdong or Hong Kong server. The hardcoding of a Shenyang university IP is engineering choice, not convenience.

The endpoint receives Creality K2 Plus connection metadata regardless of customer firewall configuration. The institution at the receiving end has documented research programs in additive manufacturing, intelligent manufacturing, and materials science that are an exact subject-matter match for the data class the K2 Plus produces.

This section documents the institutional profile of Northeastern University Shenyang, its inclusion in international defense-universities tracking databases, the subject-matter overlap between its research portfolio and the K2 Plus data streams, and the operational implications of the hardcoded NTP arrangement.

---

## The technical finding

### Firmware reference

Firmware extraction from CR0CN240110C10_V1.1.5.5.img reveals the hardcoded IP literal `202.118.1.130` and the hostname string `time.neu.edu.cn` in the boot-time NTP client configuration. The hostname is present in the firmware but is not the field used for the connection. The IP literal is used directly, which means:

- DNS resolution is not invoked
- Consumer-deployed DNS firewall solutions (Pi-hole, AdGuard Home, OpenWRT-based filtering, Unifi DPI) cannot block the connection by name
- The hostname presence is residual or documentary, not operational
- IP-level blocking would be the only effective customer-side mitigation

### Network confirmation

Live packet capture from a customer-owned K2 Plus during normal operation confirms the device makes outbound UDP/123 (NTP) connections to 202.118.1.130 at boot and at recurring intervals consistent with chrony or ntpdate operation. The connection occurs regardless of customer cloud-feature engagement, regardless of consent state, and regardless of whether any other Creality cloud service is active.

### Comparison to industry-standard convention

The industry-standard NTP source for embedded devices is `pool.ntp.org`, a community-maintained server pool that uses DNS round-robin across thousands of geographically distributed servers. The default behavior is to query a regional sub-pool (e.g., `cn.pool.ntp.org` for China-resident devices, `us.pool.ntp.org` for US-resident devices) which rotates across a transparent set of servers operated by NTP volunteers worldwide.

By using `pool.ntp.org` convention, a vendor receives time synchronization without:
- Hardcoded dependency on any specific server's continued operation
- The privacy implication of a specific institutional endpoint being able to enumerate the vendor's deployed device base
- The geopolitical implication of a single foreign institution receiving the vendor's device's outbound time-query traffic

The Creality K2 Plus's hardcoded IP choice departs from this convention. The replacement is a single foreign-jurisdiction institutional endpoint chosen by the vendor at firmware compile time.

---

## Institutional profile - Northeastern University Shenyang

### Identity and status

Northeastern University (东北大学, Dōngběi Dàxué, "Northeast University"; abbreviated NEU) is a public research university located in Shenyang, Liaoning Province, People's Republic of China. The institution is distinct from the US-located Northeastern University in Boston, Massachusetts; the two universities share an English-translated name but no organizational or research relationship.

NEU Shenyang holds the following PRC-government designations:

- **Project 211** (originally announced 1995): membership in the People's Republic of China's first elite-universities funding program, covering approximately 116 institutions targeted for state investment
- **Project 985** (announced 1998): membership in a more selective second-tier elite-universities program, covering approximately 39 institutions
- **Double First Class University Plan** (announced 2017): membership in the current generation of PRC strategic-university designations
- **Directly affiliated with the PRC Ministry of Education**: the highest organizational tier for Chinese public universities

These designations indicate NEU Shenyang receives central-government strategic-priority funding and is positioned as a national research institution.

### Research portfolio

NEU Shenyang's research portfolio centers on heavy industry, materials science, automation, and metallurgy. This reflects the university's historical roots in the Shenyang industrial economy and its founding mission to support Chinese heavy-manufacturing capability development.

Documented research areas at the School of Materials Science and Engineering and adjacent departments include:

- **Additive Manufacturing (3D printing)**: process development, parameter optimization, multi-material printing, in-situ quality monitoring
- **Powder Metallurgy**: precursor science for industrial metal 3D printing
- **Aluminum Alloys**: alloy development for aerospace and industrial applications
- **Titanium Alloys**: alloy development with aerospace, medical, and defense applications
- **Metal Matrix Nanocomposites**: high-performance materials development
- **Intelligent Manufacturing**: AI and computer-vision applied to manufacturing processes, including process monitoring, defect detection, and parameter optimization
- **Rolling and Automation**: industrial process control and automation, hosted at the State Key Laboratory of Rolling and Automation

The institution hosts at least one **State Key Laboratory** (the State Key Laboratory of Rolling and Automation). State Key Laboratories are the top tier of PRC research institution, directly funded by the central government for strategic technologies. Inclusion at State Key Laboratory level positions the institution within the PRC's national strategic research infrastructure.

### Defense-universities tracker inclusion

NEU Shenyang is listed in the **Australian Strategic Policy Institute (ASPI) China Defence Universities Tracker** at unitracker.aspi.org.au/universities/northeastern-university. The ASPI Tracker is the canonical international database of PRC universities with documented research ties to the People's Liberation Army, the State Administration of Science, Technology and Industry for National Defense (SASTIND), or the broader PRC defense-industrial system.

Inclusion in the Tracker is the basis on which Western governments restrict participation in research collaborations with the listed institutions and on which export-control authorities scrutinize technology transfers. The Tracker is referenced by, among others:

- US Department of Commerce Bureau of Industry and Security (BIS) Entity List determinations
- US Department of State export-control assessments
- UK Foreign, Commonwealth and Development Office research-security guidance
- Multiple European national research-security frameworks

NEU Shenyang's Tracker inclusion does not, by itself, establish PLA control or direction over any specific research output. It does establish documented institutional links with the PRC defense-industrial system sufficient to warrant Western export-control attention.

### Geographic-jurisdictional context

Shenyang, the city in which NEU is located, is the historical and current center of the Chinese heavy defense industry. Defense-industrial entities headquartered in or adjacent to Shenyang include:

- **Shenyang Aircraft Corporation (SAC)**: producer of PLA Air Force and PLA Navy fighter aircraft, including the J-8 series, J-11, J-15 (the PLA Navy's carrier-based fighter), and J-16
- **Shenyang Liming Aero-Engine Company**: producer of jet turbofan engines for PLA aircraft, including the WS-10 series and related military propulsion systems
- **Shenyang Institute of Automation (SIA), Chinese Academy of Sciences**: PRC state research institution with documented PLA-applications research, including unmanned underwater vehicle systems with anti-submarine warfare relevance
- **Shenyang Aerospace University**: another university in the defense-universities tracking databases, focused on aerospace and aircraft technology

NEU Shenyang is geographically and institutionally embedded in this defense-industrial ecosystem. The university's research portfolio, the State Key Laboratory designation, and the surrounding defense-industry concentration are mutually reinforcing context.

---

## Subject-matter overlap between NEU research and K2 Plus data streams

The Creality K2 Plus generates and transmits four categories of data class. NEU Shenyang's research portfolio includes documented research programs in each category.

### Category 1 - Physical output artifact data

The K2 Plus produces 3D-printed physical objects from polymer (PLA, PETG, ABS, TPU, and engineering polymers). The K2 Plus reports back to its cloud the dimensions, material consumption, time-to-print, and process parameters of each print job.

NEU Shenyang has active research programs in **additive manufacturing** that study exactly this artifact class. Research papers from NEU's School of Materials Science and Engineering document parameter optimization, layer adhesion analysis, dimensional accuracy characterization, and material property mapping for 3D-printed artifacts.

The K2 Plus's process-parameter telemetry stream is the input-data class that NEU's additive manufacturing research consumes.

### Category 2 - Camera imagery of manufacturing process

The K2 Plus contains a built-in camera (`cam_app` process) that captures the print bed at 1920x1080 resolution and 15 frames per second, continuously, regardless of whether any user is viewing the camera or any print is in progress. The camera observes the printer's nozzle, the printed object as it forms, the bed surface, and any objects placed within the printer's enclosure.

Captured frames are uploaded to `pic2-cdn.creality.com` with HTTP request headers `__CXY_DUID_` (device serial) and `__CXY_UID_` (account identifier) binding each frame to a specific device and customer account.

NEU Shenyang has documented research programs in **intelligent manufacturing** and **computer-vision applied to manufacturing processes**, including in-situ quality monitoring, defect detection, and process-state classification from imagery of operating manufacturing equipment.

The K2 Plus's continuous camera imagery stream is the input-data class that NEU's intelligent manufacturing research consumes.

### Category 3 - Process telemetry

The K2 Plus transmits to its cloud via Alibaba Cloud MQTT (cleartext on port 1883) a continuous stream of process telemetry: toolhead position (X, Y, Z), fan speeds, hotend temperature, bed temperature, current G-code line number, print state, and the filename of the active print job.

NEU Shenyang has documented research programs in **rolling and automation**, **industrial process control**, and **AI-assisted parameter optimization for manufacturing processes**.

The K2 Plus's process-telemetry stream is the input-data class that NEU's automation and process-control research consumes.

### Category 4 - Materials behavior

The K2 Plus reports material consumption (filament used per print), implicit temperature-vs-extrusion data (deducible from temperature setpoint history plus extruder step counts), and any failed-print or quality-degradation events.

NEU Shenyang has documented research programs in **materials science**, including aluminum alloys, titanium alloys, and polymer materials behavior characterization.

The K2 Plus's implicit materials-behavior data is relevant to NEU's materials-science research portfolio.

### Summary of overlap

For each of the four data categories the K2 Plus produces, NEU Shenyang operates a documented research program that consumes data of that class. The overlap is exact across all four categories. A research entity could not have specified a more useful set of consumer-deployed data sources for its own research program than the K2 Plus's collective data streams.

The overlap does not establish that NEU is in fact receiving the K2 Plus data streams. It establishes that NEU is institutionally positioned to use them if delivered.

---

## Operational analysis - NTP as side-channel telemetry

### What an NTP query reveals

A single NTP query from a K2 Plus device to 202.118.1.130 transmits, at minimum:

- The **source public IP address** of the device, which geo-resolves to a city or region with commercial geo-IP services
- The **query timestamp**, which establishes the device was powered and network-connected at that moment
- The **query cadence** over time, which characterizes the device's operational schedule (idle hours, active hours, time-of-day usage patterns)

If the receiving institution operates the NTP server (or has access to its query logs), the institution obtains the following dataset across the K2 Plus deployment globally:

- **Global geographic distribution** of K2 Plus devices: which cities, regions, and countries contain operating units
- **Density mapping**: which neighborhoods and which industrial parks contain multiple units
- **Usage patterns**: when devices are powered, for how long, on what schedules
- **Real-time inventory**: which devices are currently online at any given moment

### Why this matters when other telemetry is blocked

The K2 Plus's other telemetry channels (MQTT to Alibaba Cloud, image upload to pic2-cdn, system log upload to Creality Cloud) can be blocked at the consumer firewall level by:
- Blocking the destination IPs at the router
- DNS-level hostname blocking (since these channels use DNS-resolved hostnames)
- VLAN isolation of the printer

The hardcoded NTP IP **bypasses DNS-based blocking** and requires the customer to know the specific IP literal to block. A customer who has otherwise locked down their printer's outbound connections will still permit the NTP query unless they are sophisticated enough to find and block 202.118.1.130 specifically.

The NTP channel therefore functions as a **resilient inventory and timing side-channel** that operates **independently** of the device's other telemetry pipelines. Even a customer who has aggressively firewall-isolated their K2 Plus is, by default, still transmitting their device's presence and operational schedule to the institution at the receiving end of the hardcoded NTP IP.

### End-to-end pipeline integration

The hardcoded NTP IP does not exist in isolation. Combined with the other documented K2 Plus telemetry channels, the data pipeline is:

```
Camera capture (cam_app, continuous)
    -> pic2-cdn.creality.com (image upload, identifier-tagged)
        -> Creality Cloud backend (PRC-jurisdiction)

Process telemetry (Klipper/Moonraker)
    -> mqtt.crealitycloud.com (Alibaba Cloud, cleartext port 1883)
        -> Creality Cloud backend (PRC-jurisdiction)

System logs (/var/log/*, default-enabled)
    -> Creality Cloud upload (PRC-jurisdiction)

Edge-stage exfiltration
    -> devdata.cxswyjy.com (AWS US-East-1)
        -> server-to-server sync to PRC backend

Boot-time and recurring NTP queries
    -> 202.118.1.130 (NEU Shenyang)
        -> hardcoded IP, bypasses DNS-level customer blocking
```

The NTP channel is the **lowest-friction, highest-resilience** member of this pipeline. It produces less data per query than the image or telemetry channels, but it operates under conditions where the higher-data channels can be blocked. The NTP channel is the channel of last resort that confirms the device's presence even under aggressive customer-side isolation.

---

## Implications across regulatory frameworks

The combined finding (hardcoded NTP + subject-matter overlap + defense-universities tracker inclusion + end-to-end pipeline integration) has different relevance to different regulatory frameworks.

### FTC Bureau of Consumer Protection (Section 5)

The hardcoded NTP arrangement is a material undisclosed product characteristic. A consumer purchasing a "3D printer" does not expect that the device contains firmware-level hardcoded connections to a specific foreign university, bypasses standard firewall controls to maintain those connections, and routes telemetry across an integrated multi-channel pipeline to a foreign-jurisdiction backend. The undisclosed nature of the characteristic supports Section 5 unfairness and deception claims.

### US Trade Representative Section 301 (Unfair Trade Practices, China)

Section 301 of the Trade Act of 1974 authorizes USTR action against foreign government practices that are unjustifiable or unreasonable and burden US commerce. PRC state-supported acquisition of US-origin intellectual property and industrial data via deployed consumer devices is within the historical scope of USTR Section 301 attention. The K2 Plus finding documents a specific, operational, deployed-at-scale mechanism for the data acquisition class Section 301 addresses.

### CFIUS / Outbound Investment Review (Treasury Department)

The Committee on Foreign Investment in the United States (CFIUS) and the parallel Outbound Investment Security Program (Executive Order 14105, August 2023) address foreign-investment and -technology-transfer transactions involving sensitive technologies. The K2 Plus finding documents a deployed-product mechanism by which US-resident consumer and small-business 3D printing activity (including 3D printing by inventors, prototypers, and small-batch manufacturers) is transmitted to a PRC defense-universities-tracker-listed institutional endpoint. The finding is relevant to outbound-investment review of any subsequent US investment in Creality, in NEU collaborations, or in adjacent PRC additive-manufacturing technology entities.

### Department of Justice / FBI Counterintelligence

The FBI's Economic Counterintelligence Unit addresses state-sponsored economic espionage including industrial-intelligence collection via technology deployment. The K2 Plus finding is within the historical scope of FBI counterintelligence attention, particularly given the defense-universities-tracker listing of the receiving institution.

### Congressional oversight (Select Committee on Strategic Competition)

The House Select Committee on Strategic Competition Between the United States and the Chinese Communist Party (the "China Select Committee") has held hearings on PRC industrial-intelligence collection methods and PRC consumer-product surveillance characteristics. The K2 Plus finding fits the documentary record the Committee has established and is the kind of finding the Committee has historically invited expert testimony on.

### CISA Coordinated Vulnerability Disclosure (parallel filing already documented)

The CISA CVD submission (parallel filing referenced in the umbrella case file) addresses the K2 Plus as a supply-chain-risk-management concern. The NEU Shenyang endpoint analysis strengthens the supply-chain framing by establishing a specific, named, defense-universities-tracker-listed institutional recipient of the supply-chain data flow.

---

## What the finding does not establish

This section deliberately scopes the finding within evidentiary boundaries. The finding establishes:

- The hardcoded NTP IP is operational
- The receiving institution is NEU Shenyang
- The receiving institution has documented research portfolio overlap with the K2 Plus data streams
- The receiving institution is listed in the ASPI Defence Universities Tracker
- The technical mechanism of the side-channel is verified
- The geographic and institutional context (Shenyang defense-industrial cluster) is documented

The finding does not establish:

- That NEU Shenyang is in fact receiving K2 Plus NTP query logs (the NTP server may or may not maintain query logs)
- That NEU Shenyang has any agreement with Creality for data transfer
- That NEU Shenyang is using any K2 Plus data in its research
- That the PLA or any other PRC government entity has access to K2 Plus data via NEU

Establishing those further questions requires additional evidence beyond the device-side firmware extraction and network forensics within the scope of this investigation. They are referrable questions for federal-level investigation with subpoena and intelligence-collection capabilities.

The finding establishes that the **operational capability and institutional positioning** for the further conduct exist. The further conduct itself is a question for authorities with collection mechanisms outside the scope of this investigator's mandate.

---

## Source documentation

### Firmware and network artifacts

- K2 Plus firmware: CR0CN240110C10_V1.1.5.5.img (extracted, in the public case file repository at `firmware/extracted/`)
- Network capture: live packet capture demonstrating NTP query to 202.118.1.130 (in the public case file repository at `network-captures/`)
- Configuration files: `/etc/appetc/alchemistp/config.json` and related, documenting the hardcoded IP

### NEU Shenyang institutional documentation

- [Northeastern University Shenyang - ASPI China Defence Universities Tracker](https://unitracker.aspi.org.au/universities/northeastern-university)
- [The China Defence Universities Tracker (ASPI report)](https://www.aspi.org.au/report/china-defence-universities-tracker/)
- [Northeastern University Shenyang - Shanghai Ranking institution profile](https://www.shanghairanking.com/institution/northeastern-university-shenyang)
- [Prof. Deliang Zhang - School of Materials Science and Engineering, NEU Shenyang](https://sciprofiles.com/profile/2553752)
- [Recent Progress in Additive Manufacturing of Alloys and Composites (NCBI / NEU research output)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC11205032/)

### PRC State Key Laboratory system context

- [China's State Key Laboratory System - CSET Georgetown](https://cset.georgetown.edu/wp-content/uploads/CSET-Chinas-State-Key-Laboratory-System.pdf)
- [Understanding Ties Between China's Research Institutions and the PLA - Kharon](https://www.kharon.com/brief/understanding-ties-between-chinas-research-institutions-and-the-pla)

### Academic context (optical-side-channel attack on 3D printers)

- [One Video to Steal Them All: 3D-Printing IP Theft through Optical Side-Channels (arXiv 2506.21897, 2026)](https://arxiv.org/abs/2506.21897)
- [Turning Hearsay into Discovery: Industrial 3D Printer Side Channel Information Translated to Stealing the Object Design (arXiv 2509.18366)](https://arxiv.org/abs/2509.18366)

### Shenyang defense-industrial cluster context

- US-China Economic and Security Review Commission research on PRC military innovation
- Open-source documentation of Shenyang Aircraft Corporation, Shenyang Liming Aero-Engine, and Chinese Academy of Sciences Shenyang Institute of Automation

---

## Section integration

This section is to be inserted into the umbrella case file (`CREALITY - Coordinated Extraction Disposition Across Three Operational Layers.md`) under the section heading for PRC Infrastructure Endpoint Analysis. It supersedes any briefer treatment of the NTP endpoint that exists in the umbrella file and is referenced from the executive summary as the case-defining finding for **institutional-recipient-side analysis**.

This section is intended to be PGP-signed alongside the umbrella case file in the next signing round and to be referenced by hash from all parallel regulatory filings (FTC, CISA, state AGs, CFPB, Mozilla PNI, journalist outreach) as the supporting documentation for any claim about the NEU Shenyang endpoint.

---

*Draft 2026-06-24. Pending review and PGP signature.*
