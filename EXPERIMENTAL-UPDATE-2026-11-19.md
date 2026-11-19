# Experimental V2 aggregate forwarding update

Updated November 19 2026. Experimental firmware. Not a production or packet delivery certification.

## Why the firmware changed

The archived admission forwarding guard compared incoming aggregate depth plus one with the active tier budget. An aggregate arriving at depth two could therefore be rejected by a receiver with two tiers even when that receiver was closer to the keeper and would transmit at depth one.

The candidate compares the outgoing transmitter depth with the active tier budget. It retains the incoming hop ceiling and existing uphill routing, equal-depth one-shot, seated-node and duplicate suppression checks. It does not bypass authentication or admission identity checks.

```cpp
const uint16_t outHops = (uint16_t)g_anchorHops + 1U;
if (!(inHops + 1 < RELAY_MAX_HOPS &&
      outHops < RELAY_MAX_HOPS &&
      outHops <= g_schedTiers)) return false;
```

The capture motivated this correction but incomplete original keeper logs do not prove this guard caused every stalled join.

## Included firmware and build targets

The archive includes src, vendor dependencies with their existing notices, native-tests, platformio.ini, the partition table and eleven application images. It excludes NVS and device readbacks, enrolled keys, provisioning records, precise-location captures and personal files.

Normal targets use V7.30.153 with SF6 and the existing wire profile. Node03 uses V7.30.154 with the intentional keeper-hold fixture. Node15 is a listen-only witness and must not act as a keeper. Node09 retains its sender-specific source and headers. Source paths are not interchangeable simply because their main filenames match.

Build example from the project directory:

```text
pio run -e node04_relay41
pio run -e node03_uphill_hold
pio run -e node15_relay41
```

Images are application-only at offset 0x10000 on the existing 8MB V2 partition layout. Do not use one node's image for another node. These are not merged factory images. A blank device also needs the matching bootloader and partition table and independent authorized provisioning. Existing device identity and filesystem contents were retained during the observed rollout. No operational mesh key is shipped. Firmware loads its enrolled key from device NVS. The published node allowlist is a bench configuration, not a production trust policy.

## Verified checks

* Native extraction of the actual aggregate function: 576 topology cases. The captured depth-two case failed the baseline and passed the candidate. 56 formerly blocked uphill or equal-depth cases were admitted. No previously valid case was removed.
* All eleven connected boards received their own application image. Each write passed esptool hash verification and serial identity/version checks. Node03 retained keeper hold and node15 retained its witness role.
* A 180-second logical-topology bench experiment enforced 04 to 01 to 12 to 09 with volatile receive filters. Node12 adopted depth one and node09 adopted depth two. Keeper04 logged four accepted node09 token-bearing packets while accepting immediate radio traffic only from node01. This supports return traffic across two relay boards or three radio links.
* The bench filter also enables an existing relay probe path. This is not a physical range test. Nodes were already admitted at the start, so this does not prove fresh deep B-aggregate admission or fresh position delivery.
* A later per-node sample recorded 125 successful local radio-driver transmissions from 125 attempts across the eight member nodes. Zero driver failures were observed in that sample. Local transmit success is not an end-to-end packet delivery rate.

## Known limitations and unresolved recovery behavior

The forced chain did not remain stable. Downstream schedule progress stopped. Node09's last retained keeper04 schedule preceded its recorded warm takeover by 37.840 seconds. Node12 also lost schedule progress before appearing as keeper. Those observations require a forwarding and authority-progress investigation; they do not alone prove an election predicate defect.

All volatile receive filters were restored afterward. The intentional node03 keeper hold can prevent normal convergence after partitioning. A subsequent reset let node09 rejoin node03 while the other sphere selected node05. Report each observation with its run context rather than treating the whole fleet as continuously converged.

Received telemetry acceptance counters exclude packets lost before reception and may reject records for reasons other than RF loss. No end-to-end loss percentage or two-second fresh-position guarantee is established.

## Next validation

Trace the last advancing schedules at each relay against the warm-successor timer. Then reproduce a fresh depth-two admission with explicit aggregate forwarding and keeper acceptance witnesses. Run an outdoor walk test after the stationary chain is understood. Test forced-keeper behavior separately from normal merge behavior.

## Archive contents

LSMesh-V2-Experimental-2026-11-19.zip contains the firmware project, application images, image hashes, isolated regression source and the focused aggregate guard patch. Existing third-party license notices are retained. No new license grant for the project is implied by this upload.
