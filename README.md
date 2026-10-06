---
okf_version: "1.0"
kind: "KnowledgeAsset"
asset_type: "readme"
domain: "ruu-cloud"
severity: "guideline"
name: "Ruu Cloud"
---

# Ruu Cloud

Ruu Cloud extends the Ruu coordination domain from one host to a development
team operating across multiple independent hosts.

The governing product meaning is defined by
[RUU-CLOUD-PRODUCT-INTENT-v2.md](RUU-CLOUD-PRODUCT-INTENT-v2.md).

The non-normative
[Ruu Cloud Product Rationale](docs/product/ruu-cloud-product-rationale.md)
explains the user problem, value, pain removed, and capabilities that motivate
that Product Intent. The rationale creates no product semantics and is not an
independent input to architectural derivation.

The Core / Cloud boundary remains governed by the Product Intent: Ruu Core
provides the complete single-host capability and fundamental correctness
properties; Cloud value comes from extending the coordination domain across the
team and operating the resulting distributed system.

This repository does not treat the Product Rationale as architecture,
implementation, or verification evidence.
