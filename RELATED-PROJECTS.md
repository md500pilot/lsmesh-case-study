# Related projects and design comparison

Reviewed November 19 2026. These are related public implementations. The comparison describes documented architecture and the LSMesh checkpoint published here. It is not a comparative performance benchmark or a claim that these mechanisms are unique to LSMesh.

| Project | Documented architecture | Relation to LSMesh |
| --- | --- | --- |
| [LoRaMesher](https://github.com/LoRaMesher/LoRaMesher) | Distance-vector routing over TDMA superframes with a network manager; multi-hop synchronization; sponsor-based joining and manager election. | The closest architectural comparison in this review. LSMesh also uses a keeper-published schedule and relay admission. Both need to handle lost synchronization and replacement authority. Their detailed scheduling and admission mechanisms require a code-level comparison before judging performance. |
| [MeshCore](https://github.com/meshcore-dev/MeshCore) | Lightweight multi-hop routing for LoRa and other packet radios with companion and repeater roles. | Related embedded mesh work. Its documented role and routing model differs from LSMesh's keeper-published airtime schedule. No comparative throughput or recovery result is claimed. |
| [Meshtastic](https://github.com/meshtastic/firmware) | LoRa firmware for messaging and position sharing; documented managed broadcast flooding and a separate direct-message routing approach. | Overlaps with LSMesh's position and status use cases. Compare application delivery and position age under matched radio settings rather than assuming their MAC and scheduling models are interchangeable. |

## What this LSMesh checkpoint emphasizes

* Keeper-published dynamic superframes with member slots and relay windows.
* Admission forwarding and aggregate depth checks toward the keeper.
* Authority epochs and advancing schedule evidence for recovery decisions.
* GNSS position offsets with confidence and freshness metadata plus BLE export.
* Authenticated encryption and replay controls alongside experimental oscillator-derived token checks. See [Security scope](SECURITY-SCOPE.md).

These are features of this implementation. They are not claims that another project lacks equivalent capabilities. Hardware-rooted trust remains a separate design and validation track.

## Validation status

The experimental bench chain carried return traffic across two relay boards but later lost downstream schedule progress and split into multiple keeper spheres. The intentional forced-keeper fixture affects convergence. Local radio-driver success is not proof of end-to-end delivery or fresh position availability. No claim of better reliability, faster recovery or greater cryptographic strength than the related projects is established.

## Primary references

* [LoRaMesher protocol specification](https://github.com/LoRaMesher/LoRaMesher/blob/main/PROTOCOL_SPEC.md): TDMA coordination; sponsor-based admission; manager election. Its documented recovery widens beacon listening before election after missed synchronization.
* [MeshCore project documentation](https://github.com/meshcore-dev/MeshCore#about-meshcore): routing and node roles.
* [Meshtastic mesh algorithm](https://meshtastic.org/docs/overview/mesh-algo/): broadcast and direct-message routing.

Upstream projects evolve. This description records the documentation reviewed on the date above.
