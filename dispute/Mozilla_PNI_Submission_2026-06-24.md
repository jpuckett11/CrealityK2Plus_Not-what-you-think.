# Mozilla "Privacy Not Included" Submission - Creality K2 Plus 3D Printer

**Submission destination**: Mozilla *Privacy Not Included* consumer
privacy ratings (`foundation.mozilla.org/en/privacynotincluded/`)
**Date drafted**: 2026-06-24
**Product**: Creality K2 Plus 3D Printer
**Submitter**: Jay Puckett (Reckoner), Lead Investigator, Obsidian Watch Group

---

## Product summary

The Creality K2 Plus is a consumer 3D printer manufactured by
Shenzhen Creality 3D Technology Co., Ltd. (PRC-domiciled,
headquartered in Shenzhen). Retail price approximately $1,000-$1,500
USD. Distributed through Amazon, MicroCenter, MatterHackers, the
direct Creality store, and other US e-commerce channels.

## Privacy assessment

**Recommendation: Do Not Buy.** The K2 Plus exhibits documented
consumer-IoT surveillance-architecture characteristics that the
*Privacy Not Included* program is designed to surface to consumers.

## Documented privacy concerns

### Continuous camera capture regardless of user activity

The K2 Plus has a built-in print-bed camera capturing 1920x1080 at
15fps. The camera capture process (`cam_app`) runs continuously
regardless of whether any user is viewing the camera or any print is
in progress. The camera sees inside the printer enclosure at all
times the device is powered on, including the user's hands, the
printed objects, and (depending on enclosure orientation) the
surrounding room.

### Persistent connection to PRC-jurisdiction cloud infrastructure

The device maintains persistent connection to Alibaba Cloud
(`mqtt.crealitycloud.com` at 47.253.214.226:1883, cleartext on
port 1883) regardless of whether the consumer is using any Creality
cloud feature. Device state (toolhead position, fan speed,
temperature, print state, filename) is transmitted in plaintext
readable by anyone in the network path.

### Hardcoded Chinese-university NTP IP bypassing DNS

The device's firmware contains a hardcoded IP (202.118.1.130,
`time.neu.edu.cn`, Northeastern University in Shenyang, China)
that the device contacts for time synchronization. The connection
bypasses DNS, meaning consumer-level DNS firewall controls cannot
block it. There is no technical justification for hardcoding this
specific IP; standard practice is to use `pool.ntp.org` with normal
DNS resolution.

### System log exfiltration default-enabled

The device's configuration file
(`/etc/appetc/alchemistp/config.json`) specifies that `/var/log/*`
(the Linux system log directory including `/var/log/messages`, the
syslog) is uploaded to Creality cloud by default. This is the entire
system log, including kernel messages, USB events, authentication
attempts, and every process that logs to syslog. The upload flag is
default-enabled.

### Consent UI theater

The device presents a privacy consent dialog at first-boot setup
asking the user to accept the privacy / Terms of Use clause. The
consent dialog does not reflect consent state on the device:
declining consent does not change the on-disk values
`agree_privacy: 1` and `data_collect: 1`, which persist regardless
of the consumer's expressed choice.

### Image-upload pipeline with per-device identifier binding

The device has a dedicated image-upload CDN (`pic2-cdn.creality.com`)
that ties uploaded images to a specific device serial and account
identifier via custom headers `__CXY_DUID_` and `__CXY_UID_`. The
configured AI model is Tencent Hunyuan, a general-purpose large
foundation model with broad recognition capability including OCR,
brand identification, character recognition, and scene
understanding. The publicly-cited rationale ("AI fault detection")
does not require a general-purpose foundation model.

### Default credentials and remote access exposure

The device ships with default root credential `creality_2024` (SHA-
256-crypt hash identical across all units, documented in
third-party guides). Dropbear SSH and ADB (port 5037) are exposed
on the LAN. Anyone on the LAN with the documented credential has
root SSH access; anyone with Creality-side authorized ADB keys has
remote root.

### No coordinated vulnerability disclosure program

Creality does not maintain a security.txt file, security contact,
bug bounty program, or CVD policy. Security researchers documenting
findings against Creality products have no vendor-side
responsible-disclosure channel.

### Account-identity-tagged adversarial blocking

Creality's direct store implements SKU-category restriction at the
customer-identity level. Customer identities flagged as
investigative are blocked from purchasing replacement mainboards
(the hardware required for adversarial investigation). This was
verified via differential testing (logged-in vs. private window
with VPN: blocked vs. accessible).

## What you can do as a consumer

If you already own a K2 Plus and want to limit exposure:

1. Block the printer's WAN-side internet access at your router. The
   printer does not need internet to print; it needs LAN access for
   Klipper/Moonraker.
2. Block external access to the printer's IP at the firewall.
3. Physically disconnect or block the camera if you do not use
   remote viewing.
4. Do not use Creality Cloud features.
5. Recognize that the device is reporting to multiple cloud
   destinations and that even with internet access, the
   user-facing controls do not stop the data collection - the
   `agree_privacy` config flag persists at 1 regardless of UI
   interaction.

## Source documentation

Full investigative case file at
`github.com/jpuckett11/CrealityK2Plus_sweep`.

This submission is based on the public case file plus the original
network-forensic findings documented in May 2026 and extended
2026-06-24 firmware extraction and live verification findings.

## Submitter contact

Jay Puckett (Reckoner)
Lead Investigator, Obsidian Watch Group
investigations@obsidianwatch.org

PGP fingerprint: 9267 A71E 3F0A 4EED 2F97 3F9B A3E2 52F2 4635 CC6C

---

*Draft 2026-06-24 in preparation for Mozilla PNI submission. Submit
via the PNI form at foundation.mozilla.org/en/privacynotincluded/
or via direct contact to the PNI editorial team.*
