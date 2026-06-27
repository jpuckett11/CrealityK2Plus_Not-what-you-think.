# Creality K2 Plus, Final Findings Report

**Investigator:** Jay Puckett (Obsidian Watch Group), investigations@obsidianwatch.org
**GPG:** `9267 A71E 3F0A 4EED 2F97 3F9B A3E2 52F2 4635 CC6C`
**Subject:** Creality K2 Plus 3D printer, firmware V1.1.5.5 (released 2026-05-08), OEM model code `CR0CN240110C10`, SoC Allwinner T113-S3.
**Methodology:** [METHODOLOGY.md](METHODOLOGY.md). Full reports under [reports/](reports/). Artifact hashes in [ARTIFACTS.md](ARTIFACTS.md). Chain of custody in [CHAIN_OF_CUSTODY.md](CHAIN_OF_CUSTODY.md).
**Status:** First-cut final.

## Framing

This report describes what was observed on a single Creality K2 Plus running firmware V1.1.5.5 during the investigation window. It documents network destinations, file contents, daemon activity, and firmware artifacts as observable facts. Where an observation has a benign explanation, that explanation is noted alongside any other plausible explanations. Where the investigator cannot determine intent or purpose, the report says so. The reader is left to draw conclusions appropriate to their own interest (consumer, regulator, security researcher).

The reports below should be read together. No single observation in isolation is dispositive. Patterns across multiple observations may be informative but require independent corroboration.

**On jurisdiction.** Several of the destinations documented in this report are operated by entities registered in or operating infrastructure in the People's Republic of China (Alibaba Cloud, the Chinese mirror domains of Creality, the academic-network NTP server, the Sensors Analytics SDK ingest endpoints). This is documented because data flowing to those destinations is subject to PRC data-access law (Cybersecurity Law 2017, Data Security Law 2021, Personal Information Protection Law 2021), under which operators can be compelled to provide stored data to authorities without notice to the data subject. The concern is jurisdictional: the customer is generally unaware that data routed to these destinations sits in a legal regime different from the one their consumer-protection rights derive from.

This is not a statement about the engineers, employees, or customers of any Chinese company, nor a claim of intent. Most of the people who designed and operate these systems are doing standard product engineering. The compelled-access regime is a feature of the law, not of the individuals subject to it. The same observation would be made about a US-based service routing data to a destination subject to similar US compelled-access law without disclosure to the customer.

Where this report uses terms like "Chinese-registered" or "Chinese-region", it refers to the jurisdiction the operator is subject to, not to a characterization of any individual.

## Consumer recommendation

Do not buy this device.

The recommendation is based on the findings documented in the sections below and on conduct documented in companion files (`dispute/`, `case-files/`). The findings include:

- Credential-collection clause in the Terms of Use enumerating password, passphrase, user ID, account ID, and network ID, with the consent flag persisted on disk regardless of the consumer's answer at first-boot setup (§1.1, §1.2)
- Hardcoded foreign NTP IP (Northeastern University, Shenyang) reached without DNS lookup, bypassing standard gateway-level mitigations (§1.3)
- Persistent MQTT and HTTPS telemetry to PRC-jurisdiction infrastructure throughout normal operation (§1.3, §1.6)
- Account-identity-tagged restriction on the Mainboard SKU category at the Creality direct-store website specifically targeting the investigator's customer identity (see umbrella case file)
- Chargeback-defeat conduct through redirection to Seel, Inc., an active class-action defendant in *Mickey v. Seel, Inc.*, N.D. Cal. case 4:26-cv-01336 (`dispute/`)
- Refund payment-rail bypass requests defeating consumer chargeback protection and prejudicing BNPL lender recovery (`dispute/`)
- Parts unavailability for documented failure modes - the F008_PC_FPC_V14 flex cable is a manufacturer-acknowledged failure mode (V14 revision count) that Creality does not sell as a service part, replicated across three components (compute board, adapter board, the V14 cable itself), corroborated by independent owner reports (`case-files/Anti-Repair Engineering`)
- Post-resolution one-directional retrieval-demand contact at roughly two-orders-of-magnitude responsiveness asymmetry (high-frequency outbound demand pings versus next-business-day or longer silence on inbound consumer logistics questions), documented as ongoing during 2026-06-24 through 2026-06-25 (`case-files/Affirm Full-Loan Refund`)
- Remote-brick capability demonstrated against the investigator's own device on or about 2026-05-30, with cam_app surveillance daemon persistence through the bricked state (`case-files/Camera Daemon Persistent Through Bricked State`)

Existing owners can pursue a local-only operation path documented under `recovery/` once the necessary hardware replacement parts are sourced from third parties. The path requires root access (officially provided by Creality), removal of the cloud daemons via init.d, and network lockdown at the gateway (printers VLAN, no WAN route, DNS sinkhole, local NTP, source-MAC egress block). Most consumers will not maintain this discipline indefinitely, and stock-firmware OTA updates revert mitigations.

The recommendation is unconditional: do not buy this device. Open-firmware alternatives suitable for the same use cases exist (Voron community ecosystem, Prusa with community Klipper, Bambu hardware with cloud disabled at the gateway).

## 1. Observed facts

### 1.1 Default root credentials

The shipped `/etc/shadow` (extracted from the publicly downloadable OTA image) contains a SHA-256-crypt hash whose plaintext value is `creality_2024`. The hash bytes are identical to those produced by `crypt.crypt("creality_2024", "$5$d5o85tQWWFj2/jfn$")`. The investigator verified by SSH login to the live device.

No init script in the extracted rootfs regenerates this password on first boot. The plaintext value is also documented in third-party setup guides (SimplyPrint, others).

**Practical effect (what readers may reasonably infer):** any party with the firmware and network reachability to a K2 Plus on V1.1.5.5 can SSH as root using the documented default. Whether this is acceptable depends on the deployment environment.

### 1.2 Persistent state of the consent flag

`/mnt/UDISK/creality/userdata/config/system_config.json` on the live device contains:
```json
"agree_privacy": 1,
"data_collect": 1
```

The investigator declined the privacy / Terms of Use dialog presented at first-boot setup. The on-disk values shown above are the same as the firmware's shipped default. The investigator could not determine, from external observation, whether the dialog wrote to this field at all, or whether the field has any runtime effect.

`log_main` (Go binary, package `dev_agent`) contains an exported symbol `dev_agent/utils.AgreePrivacyStatus`. The investigator did not reverse-engineer the function body. The symbol's name suggests it reads a privacy-consent value.

### 1.3 Outbound network traffic observed during print operation

Captured via ARP-MITM with TLS interception (mitmproxy + installed CA on the device's system trust store; no cert pinning encountered). Active during a ~30-minute window with no user actions on the cloud features and no remote app open. The printer was actively printing throughout the window (a ~60-minute Klipper gcode job ran to completion during the investigation):

| Destination | Port | Protocol | Frequency | Content |
|---|---|---|---|---|
| `api.crealitycloud.com` | 443 | TLS | ~1-2 sec polling | firmware update checks, slice/material profile sync, device-type lookups |
| `mqtt.crealitycloud.com` (resolves to `47.253.214.226`, Alibaba Cloud LLC) | 1883 | MQTT, no TLS | persistent connection | device state telemetry (PUBLISH) + ThingsBoard RPC subscriptions |
| `202.118.1.130` (`time.neu.edu.cn`, Northeastern University, Shenyang, China) | 123 | NTP | periodic | time sync. Reached without a DNS lookup. IP appears hardcoded in firmware. |
| `162.159.200.123`, `208.113.130.146`, `203.107.6.88`, `23.155.72.147` | 123 | NTP | periodic | additional NTP sources, reached via DNS |

The investigator did not observe traffic to user-facing endpoints (`submitH264JPG`, `submitVideoJwt`, `preSubmitTimelapse`, `reportAiNotice`) during this window. Such traffic was observed when the user opened the Creality phone app and viewed the camera (see §1.7).

### 1.4 Headers and identifiers transmitted

Every HTTPS request to `api.crealitycloud.com` includes custom headers prefixed `__CXY_*`:

```
__CXY_BRAND_:     creality
__CXY_OS_VER_:    5.4.61            (printer's Linux kernel version)
__CXY_OS_LANG_:   0
__CXY_PLATFORM_:  10
__CXY_DUID_:      <device serial, 14 hex chars derived from WiFi MAC>
__CXY_APP_VER_:   1.1.5.5           (firmware version)
__CXY_APP_CH_:    creality          (distribution channel)
__CXY_APP_ID_:    creality_model
__CXY_REQUESTID_: <per-request unique ID, timestamp-prefixed>
__CXY_TIMEZONE_:  <seconds offset from UTC>
__CXY_JWTOKEN_:   <JWT, present on authenticated calls>
__CXY_UID_:       <CREALITY-UID>    (Creality user ID, see §1.5)
```

The JWT payload includes `sub` (device serial), `aud` (the same `__CXY_UID_` value), `iss` (`https://api.crealitycloud.com`), and `exp` (~1 month from issuance). Signing algorithm HS256.

### 1.5 User ID present without account creation

The `__CXY_UID_` value `<CREALITY-UID>` (10 digits) is present in every authenticated request. The investigator did not create a Creality Cloud account at any point in the investigation. The investigator declined the account-creation flow during initial setup.

The investigator cannot determine from external observation whether:
- The device is pre-provisioned with a UID at the factory, bound to the hardware serial;
- The device generated and registered an anonymous UID on first boot;
- The UID was assigned by Creality's backend during the device's first contact.

In all three cases, the UID is bound to the device serial in the JWT.

### 1.6 MQTT cleartext traffic content

The MQTT connection to `47.253.214.226:1883` was captured cleartext via a workstation-side socat proxy (DNS spoof routed the printer's connection through the workstation, which forwarded transparently to the real broker). Observed in PUBLISH payloads:

- Printer hostname, e.g. `K2Plus-XXXX`
- Local LAN IP address of the printer
- Production serial number
- Firmware version, screen hardware version
- Current toolhead X/Y/Z coordinates
- Fan speeds, temperatures
- Print-job state (filename, duration, progress)
- LED state
- Filament-system state
- Various status flags (`video1`, `videoElapse`, `webrtcV2`, `upgradeStatus`, etc.)

The printer SUBSCRIBED to:
- `v1/devices/me/attributes/response/+`
- `v1/devices/me/rpc/request/+`

These are ThingsBoard IoT-platform topic conventions. The `rpc/request/+` subscription is the standard channel by which a server can invoke remote procedures on the device. Whether such RPCs are issued for purposes beyond those visible in the documented user-facing app is not determinable from external observation alone.

### 1.7 Behavior observed when the user opens the camera in the phone app

When the investigator opened the Creality phone app and selected the printer's camera:

1. A new HTTPS call: `GET https://api.crealitycloud.com/api/cxy/ws/webrtc/signal/push/<device_sn>` (WebSocket-based WebRTC signaling)
2. An MQTT RPC arrived on `v1/devices/me/rpc/request/<id>` with payload `{"method":"get","params":{"cfsList":1}}` (a query for Color Filament System state)
3. The device responded via `v1/devices/me/rpc/response/<id>` with the requested state
4. A TCP connection from the device to `47.254.52.250:3478` (Alibaba Cloud LLC, port 3478 = STUN/TURN for WebRTC NAT traversal)
5. WebRTC media stream flowed between phone and printer (encrypted, not captured in clear; the device's MQTT telemetry includes `"features": "[\"videoInfo.videoEncryption\"]"`)
6. The printer wrote video data to `/mnt/UDISK/timelapse/main_output.h264`; file grew during view and stopped growing when the view was closed.

This sequence matches standard IoT remote-camera design (signaling via cloud, media via WebRTC TURN-relayed peer-to-peer). The investigator did not observe behavior in this path that is unexpected for the documented remote-camera feature.

### 1.8 Endpoint inventory from firmware analysis (static)

`strings usr/lib/libcrealitycloud.so` and `etc/appetc/alchemistp/config.json` yield the following cloud-endpoint references:

| Type | Production (.com) | Chinese mirror (.cn) | Dev/Internal |
|---|---|---|---|
| API | `api.crealitycloud.com` | `api.crealitycloud.cn` | `api-dev.crealitycloud.cn` (HTTP) |
| Admin | `admin-pre.crealitycloud.com` | `admin-pre.crealitycloud.cn` | |
| MQTT | `mqtt.crealitycloud.com` | `mqtt.crealitycloud.cn` | `pre-tb-iot.crealitycloud.com:1883` (cleartext) |
| CDN | `pic2-cdn.creality.com`, `pic-cdn.creality.com` | | `pic-cdn-dev.creality.com` |
| Smart | | `c-smart.cxswyjy.com` | |
| Telemetry | | `devdata.cxswyjy.com` | |
| Object Storage | `oss-us-east-1.aliyuncs.com` (HTTP) | `oss-cn-hangzhou.aliyuncs.com` (HTTP) | |
| Internal LAN | | | `172.23.88.185:8080`, `172.29.99.188:4020` |

API endpoint paths discovered in `libcrealitycloud.so`:
```
/api/cxy/v2/firmware/checkUpgrade
/api/cxy/v2/slice/profile/official/material/printerParameter
/api/cxy/v2/device/printerTypeListNew
/api/cxy/v2/device/user/onwerInfo                          [vendor typo "onwer"]
/api/cxy/v2/device/user/preCommitLog
/api/cxy/v2/device/user/localGcodeSubmit
/api/cxy/v2/device/user/localPrint
/api/cxy/v2/device/user/getAliyunSts
/api/cxy/v2/device/user/reportAiNotice
/api/cxy/v2/device/user/submitH264JPG
/api/cxy/v2/device/submitVideoJwt
/api/cxy/v2/device/preSubmitTimelapse
/api/cxy/v2/device/registerDevice
/api/cxy/v2/slice/profile/user/listJwt
/api/cxy/v3/stem/firmware/stemModelDetail
/api/cxy/v3/stem/firmware/stemModelList
/api/cxy/ws/webrtc/signal/push/<device_sn>
```

The investigator observed live traffic to a subset of these (firmware checks, profile sync, type lookups, webrtc signal) during the capture window. The remaining endpoints were not exercised because the investigator did not perform actions (slicing, printing, AI use, account features) that would trigger them.

### 1.9 `cxswyjy.com` domain ownership

WHOIS:
```
Registrar:    Alibaba Cloud Computing (Beijing) Co., Ltd.
Name Server:  DNS7.HICHINA.COM, DNS8.HICHINA.COM
Expiry:       2026-12-31
```

`cxsw` is the pinyin abbreviation for the Chinese-language name of Creality (创想三维, ChuangXiang SanWei). `yjy` could be one of several pinyin abbreviations (the investigator cannot determine from the registration alone). The domain hosts `devdata.cxswyjy.com` (referenced in the firmware as the telemetry-upload endpoint per §1.10) and `c-smart.cxswyjy.com` (referenced as `SERVERURL` in `alchemistp/config.json`).

The investigator did not locate any reference to `cxswyjy.com` in Creality's consumer-facing English or Chinese marketing material reviewed.

### 1.10 Log upload manifest

`etc/appetc/alchemistp/config.json` (read by the daemon stack at startup) contains:
```
"UploadFilePath": "/mnt/UDISK/creality/userdata/log/*;/mnt/UDISK/printer_data/logs/*;/var/log/*"
"UploadFileName": "klippy.log;master-server.log;display-server.log;messages"
"IDC_SERVER_URL": "https://devdata.cxswyjy.com/api/v1/"
```

`/var/log/messages` on this OpenWrt system contains kernel events, daemon state changes, and `dropbear` authentication events. The configured destination for the upload is `devdata.cxswyjy.com`. The investigator did not capture a successful upload of this file in clear during the investigation window; the destination and content are taken from the firmware's own configuration.

### 1.11 MAC address handling

- WiFi interface MAC: the OUI prefix observed on the test unit is not present in the IEEE OUI registry checked by the investigator (`macvendors.com`, local `oui.txt` lookups, Wireshark `manuf` database). The U/L bit is 0 (the value is presented as a globally-unique factory MAC).
- Wired interface MAC: the U/L bit is 1 (the value is locally-administered / random). The vendor portion does not correspond to any IEEE assignment.
- When the user disabled WiFi via the device's settings menu, Moonraker's `/machine/system_info` endpoint no longer reported a `wlan0` interface. The MAC associated with the WiFi mode was no longer enumerated by the device.

The investigator cannot determine, from external observation, the policy by which these MACs are generated or persisted on the device.

### 1.12 Cryptographic material in the firmware

`usr/share/cert/` contains:
- `server.crt`: CN=`My Server`, O=`Internal Service`, C=`CN`. Valid 2025-08 to 2026-08.
- `ca.crt`: CN=`My Private CA`, O=`Internal Network`, C=`CN`. Self-signed. Valid 2025-08 to 2035-08.
- `server.key`: PEM-encoded plaintext private key for `server.crt`.

The naming pattern (`My Server`, `My Private CA`) is the default placeholder for the OpenSSL certificate-generation prompts when fields are not filled in. The certificates are present in the publicly downloadable firmware and therefore on every K2 Plus running this firmware.

The investigator did not determine whether any service running on the device actually uses this cert pair, or whether it is residual from a build process.

### 1.13 CrealityPrint slicer (companion software)

CrealityPrint v7.1.1.4472 (released 2026-04-29) was also analyzed (installer download, static analysis after install). Observations:

- The slicer's UI is rendered in an embedded Microsoft Edge WebView2 (Chromium). Some UI sections load HTML/JS from Creality cloud servers, meaning UI content can change between sessions without a software update.
- `src/slic3r/GUI/AnalyticsDataUploadManager.cpp` (in the publicly available source at `CrealityOfficial/CrealityPrint`) contains URL strings:
  - `https://www.crealitycloud.cn/api/rest/bicollector/front/sa/data` (selected when country code is CN)
  - `https://www.crealitycloud.com/api/rest/bicollector/front/sa/data` (selected otherwise)
  - `https://api-dev.crealitycloud.cn/api/rest/bicollector/front/sa/data` (selected for Alpha/Beta builds)
- The path component `sa/data` is the standard Sensors Analytics (神策数据) ingest endpoint pattern. Sensors Analytics is a Chinese behavioral-analytics SDK with documented HTTP interface.
- The slicer's source includes enum cases for tracking ~50 model-action events (`ANALYTICS_MODEL_ACTION_ADD`, `MOVE`, `ROTATE`, `SCALE`, `HOLLOW`, `CUT`, `BOOLEAN`, `MEASURE`, etc.) and ~50+ string event names (`click_event`, `ai_service_call`, `click_home_page_crealitymall`, etc.).
- `resources/data/update_config.json`:
  ```
  "provider": "squirrel.windows",
  "update_feed": "http://172.20.180.14/shared/crealityprint/windows"
  ```
  The IP is an RFC1918 address (within Creality's corporate network). It is not reachable from a consumer's network. The investigator did not determine the fallback update mechanism.

The investigator did not run the slicer long enough to capture live analytics traffic in clear.

### 1.14 Items observed in shipped firmware that are difficult to reconcile with standard consumer-IoT practice

Synthesized from §§1.1, 1.6, 1.9, 1.10, 1.11, 1.12, 1.13 above. These items have been called out separately because each is, in the investigator's professional assessment, a choice that a typical consumer-IoT engineering team would not make if asked to justify it in front of a security review:

1. **Hardcoded foreign-academic NTP IP that bypasses DNS** (§1.3). The address `202.118.1.130` (`time.neu.edu.cn`, China Education and Research Network) is the only NTP source contacted without a DNS lookup. The device's other NTP sources resolve normally. A consumer device sold internationally would conventionally default to `pool.ntp.org` or a regional equivalent and would resolve all NTP destinations through DNS, allowing owner-side filtering. The hardcoded IP cannot be blocked at any DNS-level control.

2. **Identical default root password baked into every device on this firmware** (§1.1). The shipped `/etc/shadow` hash decodes to the publicly documented value `creality_2024` and is the same on every K2 Plus running V1.1.5.5. No first-boot script regenerates a per-device password. Standard practice for a network-connected consumer device is to either prompt the user to set a password at setup or to generate a per-device random secret displayed only on the local screen.

3. **Plaintext TLS private key shipped in firmware** (§1.12). `usr/share/cert/server.key` is a plaintext PEM file paired with `server.crt` (CN = `My Server`, O = `Internal Service`, C = `CN`) and `ca.crt` (CN = `My Private CA`, O = `Internal Network`). The naming pattern is the OpenSSL placeholder template for unfilled fields. The cert pair appears to be development-environment material that was not removed before the production build. Every device has the same private key.

4. **Cleartext MQTT in 2026** (§1.6). The persistent MQTT connection to `47.253.214.226:1883` (Alibaba Cloud LLC) is unencrypted. The observed PUBLISH payloads include the device's local LAN IP, production serial number, current toolhead coordinates, and other state. Contemporary IoT practice is MQTT-over-TLS on port 8883.

5. **Creality corporate-internal LAN IP addresses referenced in production firmware** (§1.8 internal-LAN row, §1.13 slicer `update_feed`). The firmware contains references to `172.23.88.185:8080` and `172.29.99.188:4020`. The slicer ships an updater configuration pointing at `http://172.20.180.14/shared/crealityprint/windows` (plain HTTP, RFC1918 address inside Creality's corporate network). These are not reachable from a consumer's network and appear to be configuration-management artifacts from the development environment that were not removed before release.

6. **Plain-HTTP OAuth callback URLs in the slicer source** (§1.13). The slicer's embedded strings include `http://dev.crealitycloud.cn/oauth?back_url=http://localhost:%1%/login` and similar pre-production OAuth flows. OAuth authorization codes traveling over `http://` are observable by any process on the local host, including processes that may not be entitled to the credential.

7. **MAC address handling that misrepresents factory provenance** (§1.11). The WiFi-mode MAC's OUI prefix (the first three bytes) is not registered in IEEE's OUI database checked by the investigator. The U/L bit is set such that the MAC presents as a globally-unique factory MAC. Standard practice for a device that generates its MAC in software is to set the U/L bit to 1 (locally-administered) so observers can correctly identify the MAC as non-factory. The combination here misrepresents the MAC's origin.

Items 1, 2, 3, and 4 are the most consequential. They have direct security-property implications regardless of the operator's intent. Items 5, 6, and 7 are signals of release-engineering hygiene that, taken together with the rest of the report, inform a judgment about how seriously the vendor treats the security boundary between their internal-network configuration and their shipping product.

### 1.15 Vendor AI integration (from Creality's own public statements)

Creality's AI features (visible in the slicer source as `ai_service_call`, `ai_infill`, and the cloud endpoints `ai-cn.crealitycloud.cn` / `ai-usa.crealitycloud.com`, and in the firmware as `/api/cxy/v2/device/user/reportAiNotice` and `/api/cxy/v2/device/user/submitH264JPG`) are documented by Creality as integrating with **Tencent's Hunyuan large foundation model** via Creality's `MakeNow` platform. The AI use cases publicly described by Creality are model creation/visualization, fault detection during printing (image-based), and optimization of print parameters (supports, toolpaths).

The investigator did not capture the contents of AI-bearing traffic during this investigation (those endpoints did not fire in the steady-state capture window). Inputs that would flow through this path, per the public partnership material, include print-bed camera frames (for fault detection), uploaded 3D model data (for model analysis), and print-configuration parameters.

Combined with §1.3 and §1.6, the complete vendor cloud picture is:

| Function | Operator |
|---|---|
| Public-facing API + CDN | Cloudflare (US, fronting) |
| Firmware OTA / object storage | Alibaba Cloud Object Storage (China + US regions) |
| IoT MQTT broker | Alibaba Cloud LLC |
| WebRTC TURN relay (camera) | Alibaba Cloud LLC |
| AI inference (image-based fault detection, model analysis) | Tencent Hunyuan, via Creality MakeNow |
| Behavioral analytics (slicer side) | Sensors Analytics (神策数据), China |
| NTP (one of several sources) | China Education and Research Network |

Three distinct PRC-jurisdiction cloud operators participate in the data path (Alibaba, Tencent, Sensors Analytics). Each is independently subject to the PRC compelled-disclosure regime referenced in the Framing section above.

Source: Creality's own partnership announcement, https://3dprintingindustry.com/news/creality-and-tencent-announce-new-ai-partnership-243692/ and Creality's IPO-positioning press materials covering the MakeNow + Hunyuan integration.

### 1.16 Server-side IP retention demonstrated via consumer app diagnostic feature

On 2026-06-26, with the investigator's K2 Plus device offline (powered off, network unreachable), the investigator opened the Creality Print companion application's "Diagnosis" feature for the bound device. The diagnostic dialog displayed the following text verbatim:

> Please check
> 1. Is the device turned on
> 2. Is the device connected to the network
> 3. Whether the device ip has changed (current ip:192.168.1.220)

`192.168.1.220` is the investigator's local LAN address (RFC 1918 private space) for the K2 Plus on the home network. Screenshot evidence withheld pending sanitization. The original screenshot is retained under chain of custody in the investigator's private case file; a sanitized version will be added when ready for public release.

The May 30 MITM proxy capture corroborating server-side retention is included verbatim with sensitive identifiers redacted: `artifacts/network/api_crealitycloud_mitm_2026-05-30_redacted.log`. The capture shows the printer initiating authenticated `__CXY_JWTOKEN_` sessions against `api.crealitycloud.com` continuously, with `__CXY_DUID_` and `__CXY_UID_` headers identifying the cloud device serial and Creality account, against endpoints including `POST /api/cxy/v2/firmware/checkUpgrade` and `POST /api/cxy/v2/slice/profile/official/material/printerParameter`. These calls occurred while the operator had refused the Cloud Terms of Service and during a window where the printer was not actively printing. JWT bodies decoded during analysis carried `sub` = cloud device serial, `aud` = Creality account UID, `iss` = `https://api.crealitycloud.com`, and a multi-week `exp` validity, i.e. fully authenticated cloud sessions held open against ToU refusal.

What this observation demonstrates:

- **Server-side persistence of the device's last-known IP.** The device has been offline for weeks; the diagnostic dialog still returns an IP value labeled "current." The value is retrieved from Creality's backend, not from a live device query. The retention persists past device-offline state.
- **Application-level collection of the LAN-side IP, not just the WAN-side IP visible passively.** The address shown is a private RFC 1918 address inside the consumer's home network. Creality could not learn this from passive TCP observation - the public WAN IP is what their servers see at the network layer. The LAN-side IP is only available if the device specifically reports its own internal IP to the cloud, which requires application-level enrichment by the device's cloud client.
- **Consumer-facing surface for the retained IP value.** The retained IP is not a hidden backend field; it is displayed in plain text in the consumer-facing diagnostic dialog. The retention is acknowledged and operationalized.
- **A separate row in the same UI shows the device's binding identifier (`123715280F1752`) in a column labeled "IP Address."** The conflation between device serial and network address in the UI labeling indicates the field is overloaded for general device-identifier purposes, not strictly for network addressing.

Pairing this with §1.3 (outbound traffic to `api.crealitycloud.com` and `mqtt.crealitycloud.com`) and §1.4 (headers and identifiers transmitted) establishes the full data lifecycle: the device transmits its identifiers and IP to Creality at the wire level (§1.3, §1.4), Creality retains those values server-side (§1.16, this section), and surfaces the retained values to the consumer via diagnostic tooling. The May 30 MITM evidence and the 2026-06-26 app-side demonstration together document collection, transmission, and retention as observable behaviors of the production system.

Implications for owners and regulators:

- IP retention indefinitely-persistent past device offline state is itself a privacy decision. IP addresses are personal data under GDPR Article 4 when linkable to other identifiers, and similar protection applies under CCPA/CPRA for California residents when the IP is linkable to an account.
- Retention of the LAN-side private IP (not just the public WAN IP) is enriched, application-level data collection - it is not an unavoidable side effect of network connectivity. It is a choice the device's cloud client makes to enrich what Creality's backend would otherwise see.
- The combination of device-identity tagging (§1.4, §1.5) plus persistent IP retention (this section) plus the account-identity-tagged restriction behavior observed against the investigator's customer identity (umbrella case file) constructs a per-customer location-over-time map maintained by the vendor.

## 2. What the investigation did NOT establish

- **No certificate pinning was observed** on the device's HTTPS clients. The mitmproxy CA, once installed in the system trust store, was accepted without complaint. This may or may not be true of other firmware versions or other Creality models.
- **No cleartext credentials were observed in transit** during the capture window. The JWT bound to the device is a credential, but it is HS256-signed and the signing key is held by Creality (not visible from the client side).
- **No exploitation was performed.** All findings derive from (a) the publicly downloadable OTA image, (b) the publicly documented default password, or (c) ARP MITM on the investigator's own LAN.

## 3. Recommended mitigations for owners

These are suggestions a reader may consider; the appropriateness depends on the owner's preferences.

### 3.1 If the cloud features (remote print, remote camera) are used

- Block outbound traffic to `202.118.1.130` (the hardcoded Chinese NTP IP). The device has other configured NTP sources; time sync is unaffected.
- Block outbound traffic to the IPs `devdata.cxswyjy.com` resolves to (these change). This severs the log-upload channel.

### 3.2 If the cloud features are not used

Block the printer from internet egress entirely. Place it on a VLAN or guest network with no WAN. Locally-accessible services (Klipper, Moonraker, web UI, WebRTC stream on the LAN) continue to function.

### 3.3 Custom firmware

Vanilla Klipper + MainsailOS or FluiddPi can be installed in place of the Creality daemon stack after gaining root via the documented default password. This eliminates the cloud-comms components entirely. Voids warranty.

### 3.4 Cloud-comm daemons can be disabled without affecting printing

The Creality cloud-comm daemons (`master-server`, `app-server`, `log_main`, `webrtc`) operate independently of the Klipper / MCU print path. Verified observationally during this investigation: a ~60-minute print ran to completion while the named daemons were repeatedly killed and restarted as part of the MITM setup, without interrupting the print or producing observable defects. Owners with root SSH access who wish to retain local printing functionality while disabling phone-home behavior can stop these daemons (and prevent procd from restarting them) without losing the ability to print via Klipper / Moonraker on the LAN.

## 4. Recommended disclosure routes

| Recipient | What |
|---|---|
| FTC Bureau of Consumer Protection | The credential-collection clause in the ToU. The on-disk consent default. The mirror infrastructure on `.cn` domains. |
| Mozilla *Privacy Not Included* | Full finding set for inclusion in their consumer-IoT privacy ratings. |
| CISA | The hardcoded foreign-academic NTP IP, the cleartext MQTT, the plaintext cert/key shipped in firmware. |
| State AGs (California first) | CCPA/CPRA sensitive-PI implications of the credential-collection ToU clauses. |
| Creality (coordinated disclosure) | Full finding set with 90-day embargo. |

## 5. Reproducibility

- Firmware: `https://file2-cdn.creality.com/file/061e5074c360584d65ff4ae3aba9c028/CR0CN240110C10_R_202605081127_ota_img_V1.1.5.5.img`. SHA-256 in [ARTIFACTS.md](ARTIFACTS.md). Extract with `cpio -id` then `unsquashfs`.
- Static endpoint inventory: `strings usr/lib/libcrealitycloud.so | grep -E 'https?://|mqtt'` and read `etc/appetc/alchemistp/config.json`.
- Default password verification: `python3 -c "import crypt; print(crypt.crypt('creality_2024', '\$5\$d5o85tQWWFj2/jfn\$'))"`. Compare to the `etc/shadow` hash in the firmware.
- Live API decryption: install mitmproxy CA in `/etc/ssl/certs/ca-certificates.crt` on the device, spoof `api.crealitycloud.com` to the workstation via local DNS, run `mitmdump --mode reverse:https://api.crealitycloud.com:443 --listen-port 443`.
- MQTT cleartext: `socat TCP-LISTEN:1883,fork TCP:47.253.214.226:1883` on the workstation, spoof `mqtt.crealitycloud.com` via local DNS, restart the printer's `app-server` daemon to force MQTT reconnect.

## 6. Limitations

- Single device, single firmware version. Firmware-level observations generalize to all V1.1.5.5 units. Runtime observations (specific UID, specific timestamps) reflect this unit during this window.
- TLS payload capture is partial. `api.crealitycloud.com` flows were fully decrypted. `devdata.cxswyjy.com`, `pic2-cdn.creality.com`, and `c-smart.cxswyjy.com` flows were not (additional mitmproxy reverse listeners would be needed for SNI-based routing).
- `/api/cxy/v3/stem/firmware/stemModel*` endpoints were referenced in firmware but not exercised during the capture window.
- The CFS (Color Filament System) accessory firmware (`CR0CN200400C10`, separate OTA image) was not analyzed.
- The WebRTC media stream was not captured in cleartext (encrypted via SRTP per the device's reported feature flag).
- The investigator did not perform physical disassembly. Hardware-component identification was not done.

## 7. Investigator and contact

Jay Puckett, Obsidian Watch Group
investigations@obsidianwatch.org
GPG `9267 A71E 3F0A 4EED 2F97 3F9B A3E2 52F2 4635 CC6C` (ed25519)

Repository: `https://github.com/jpuckett11/CrealityK2Plus_sweep`

For coordinated disclosure or supplementary material privately, contact the investigator via the GPG-encrypted email above. The repository's commits are GPG-signed by the investigator. Any unsigned commit on `main` should be considered suspect.

## 8. Professional retainer availability

The investigator is available for retainer engagement in litigation and regulatory matters where the work product, methodology, or findings in this case file are relevant to the questions at issue. Specific engagement scopes:

- **Expert witness testimony.** Connected-device extraction practices, consumer-IoT data flows, firmware analysis, MITM-derived traffic evidence, OTA delivery mechanisms, supply-chain identification, and the documentation chain from raw capture to report. Available for declaration, deposition, and trial testimony in state and federal court.
- **Consumer-protection litigation.** Class actions or AG-led actions involving connected-device manufacturers, consent-flow defects, undisclosed data retention, account-identity-tagged restriction, chargeback-defeat mechanisms, or related conduct documented in this case file or the umbrella file.
- **IP claims (defensive and plaintiff-side).** Hardware reverse engineering, firmware-level analysis, supply-chain provenance evaluation, and the methodology framework that distinguishes observed behavior from intent claims. Particularly applicable where a defendant's product is alleged to incorporate technology subject to disputed origin, where prior-art questions turn on what a device actually does versus what the documentation says, or where infringement analysis requires runtime-behavior observation distinct from source review.
- **Right-to-repair and parts-availability matters.** The anti-repair pattern documented across the F008_PC_FPC_V14 cable, the compute board, and the adapter board, with the manufacturer-acknowledged V14 revision count, is a worked example of the conduct pattern relevant to right-to-repair litigation and state-AG enforcement under repair-disclosure statutes.
- **Regulatory and oversight testimony.** Available to support FTC, CFPB, CISA, NHTSA, and state-AG investigations of the conduct documented across this case file portfolio. Also available for legislative oversight committee testimony where the conduct pattern is relevant to pending policy questions on consumer-IoT, foreign-supply-chain, or extraction-disposition frameworks.
- **Coordinated vulnerability disclosure and threat-modeling consultation.** Engagement under standard CVD frameworks for vendors who wish to address findings prior to public disclosure, or for organizations operating consumer-IoT products who wish to evaluate their own extraction-disposition posture before a third-party investigator does it for them.

The investigator's existing institutional standing includes Google VRP partnership status, Anthropic VRP researcher relationship, active engagement with US law enforcement cyber units, and the public investigation portfolio at github.com/jpuckett11/ covering the K2 Plus case and the Obsidian Watch Group broader investigative work.

Rate, scope, and conflict screening available on inquiry. Direct contact:

Jay Puckett (Reckoner)
Lead Investigator, Obsidian Watch Group
investigations@obsidianwatch.org
GPG `9267 A71E 3F0A 4EED 2F97 3F9B A3E2 52F2 4635 CC6C`

For litigation-context inquiries, please include the case caption, the role you are considering for the investigator (testimony, consulting expert, supplementary fact witness), and the jurisdiction. For regulatory engagements, include the relevant agency and statutory framework. For CVD engagements, include the affected product and proposed embargo timeline.
