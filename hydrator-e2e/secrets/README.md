# Secrets (templates)

Source Hydrator needs:
- a **pull** repo credential secret (`argocd.argoproj.io/secret-type: repository`)
- a **push** repo credential secret (`argocd.argoproj.io/secret-type: repository-write`)

Create these in the `argocd` namespace, adjusted for your Git auth method (PAT, GitHub App, SSH, ...).

