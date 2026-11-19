# Security scope of the experimental V2 firmware

Reviewed November 19 2026 against the published V2 checkpoint. The firmware combines mesh scheduling and forwarding with data security controls. This is an implementation description rather than a security certification.

## Implemented controls

* AES-256-GCM authenticated encryption protects mesh packet payloads and checks their integrity. The implementation derives sender-specific keys from an independently provisioned mesh root key.
* The authenticated envelope carries a boot epoch and an increasing packet counter. Receivers track replay state and persist reservations. These ordering values are independent of UTC and GPS. They are not proof of physical elapsed time or distance.
* A configured node allowlist restricts admission alongside authentication and revocation handling. A node identifier alone is not a secret credential. The shipped list is a bench configuration.
* The crystal-token path requires an enrolled oscillator baseline and a known peer fingerprint to derive pairwise frame-sequence-bound token tags. Keeper verification is enabled in the published configuration.

## Experimental oscillator-derived checks

The baseline is derived from measured oscillator calibration behavior and associated enrollment measurements. The token key derivation uses the stored baseline. It does not require a fresh matching oscillator measurement before each token is minted. The separate live measurement diagnostics must not be described as an enforced live hardware gate.

Peer fingerprint announcements convey baseline-derived inputs to participating peers. Those inputs must not be treated as an independently secret cryptographic key. This checkpoint does not establish a physically unclonable function, resistance to baseline copying or a secure-element hardware root of trust.

Token sequence binding and the encrypted envelope replay checks are separate mechanisms. The baseline alone does not provide freshness. The epoch and packet counter provide replay ordering; they do not make every old but previously unseen packet automatically expire at a wall-clock deadline.

## What is not claimed

No independent security audit, cloning-resistance result or quantified security multiplier has been established. The extra checks do not justify adding bits to the AES-256 key size. Reliable operation under physical compromise, key extraction, malicious enrolled peers and active RF interference remains outside the verified scope of this publication.

## Provisioning and release contents

The source includes encryption and token code but the publication contains no enrolled mesh root keys, NVS readbacks or operational provisioning secrets. Users of the secured firmware must provision keys for their own independent network. They also need compatible node identities and build targets. The firmware includes a USB provisioning path. Public source availability does not grant access to the author's deployed mesh.

## Evidence and limits

Observed token acceptance and successful radio-driver transmissions are functional checks. They do not measure cryptographic attack resistance or end-to-end packet delivery. See [the experimental update](EXPERIMENTAL-UPDATE-2026-11-19.md) for test results and the unresolved downstream schedule and recovery behavior.
