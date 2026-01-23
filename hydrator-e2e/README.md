# Source Hydrator e2e repro (Helm multi-values + dirty sync branch)

This folder contains a minimal Helm chart that **requires** an additional values file to render successfully.

Use it to verify that Source Hydrator writes `manifest.yaml` to the hydrated branch, and that Argo CD sync should
consume that output (and not re-run Helm due to stray `Chart.yaml` files in the hydrated output path).

