# LSMesh: Self-healing sub-GHz wireless mesh

**LSMesh recovered from a physical keeper power cut in under four seconds.** In the bench test, the successor took over in about 3.5 seconds, with no competing keeper observed. When power returned, the original keeper rejoined automatically as a member.

LSMesh is designed to share position and status among mobile teams and robots in places where network infrastructure is limited or unreliable. Nodes coordinate radio airtime, relay traffic across hops, and recover automatically when the keeper drops offline. The mesh protocol is designed to be frequency-agnostic. The current validation platform is Heltec V2 hardware using sub-GHz LoRa, where tight airtime budgets make multi-hop scheduling and recovery demanding to verify.

## Experimental firmware update

The [November 19 experimental update](EXPERIMENTAL-UPDATE-2026-11-19.md) includes the aggregate forwarding depth correction and per-board V2 source and images. The two-relay bench test carried return traffic but exposed later schedule loss and a mesh split. End-to-end delivery remains unverified. See the update before using the firmware.

## Verified engineering checkpoints

| Checkpoint | What we observed |
| --- | --- |
| Physical power-loss recovery | The successor took over in about 3.5 seconds. The original keeper rejoined when power returned. |
| Controlled failover in both directions | Each of two boards took over as keeper in a separate bootloader-hold trial. In the later trial, the restarted keeper rejoined and all nine member seats were restored. |
| Ten-board fleet agreement | All ten attached boards reported the same keeper across eight observations over about 73 seconds. The keeper consistently showed nine members. |
| Bluetooth dashboard transport | An earlier 90-second SF6 bench capture received 169 complete reports, with zero fragment timeouts, abandoned fragments, or offset errors. |
| Reduced admission airtime | A scheduler change shortened a quiet admission frame from 960 ms to 690 ms, a reduction of about 28%. |

```mermaid
flowchart LR
    A[Keeper schedules the mesh] --> B[Keeper loses power]
    B --> C[Successor takes over]
    C --> D[Original keeper returns as a member]
```

## Position sharing and device trust

The firmware exchanges GNSS position offsets, confidence, and freshness information. Nodes cache peer updates, and the dashboard reads position and reference records over Bluetooth Low Energy (BLE). Earlier project tests observed GPS fixes and relay hops.

The secured firmware uses AES-256-GCM authenticated encryption. Hardware-rooted device identity is a separate design track.

## Next validation

The next milestone is to verify that each node gets fresh peer positions within two seconds. A keeper USB capture saw reports from every member. Median arrival gaps at that output ranged from 3.05 to 4.06 seconds, and that particular indoor run had no valid GPS fixes. The follow-up will measure valid position updates at the receiving nodes.

This repository presents the engineering case study and the experimental V2 firmware checkpoint. The source includes authenticated encryption code but no enrolled device keys or NVS backups. The experimental upload does not establish production readiness.
