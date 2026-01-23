# Manifest Hydration

To hydrate the manifests in this repository, run the following commands:

```shell
git clone https://github.com/choejwoo/argocd-example-apps
# cd into the cloned directory
git checkout 1797d22f1cde8c59174ffeb85e30e9c3f0d913ee
helm template . --name-template hydrator-helm-multivalues --namespace default --values ./hydrator-e2e/dry/helm-multivalues/values.yaml --values ./hydrator-e2e/dry/helm-multivalues/values-dev.yaml --include-crds
```
