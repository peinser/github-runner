[![Build & Distribute GitHub Actions Runner Container](https://github.com/peinser/github-runner/actions/workflows/build.yaml/badge.svg)](https://github.com/peinser/github-runner/actions/workflows/build.yaml)

# GitHub Actions Runner

This repository contains the definition of Peinser's self-hosted GitHub Actions Runner, which
contains serveral utilities that we use frequently.

## Helm
The `values.yaml` is provided to ease the installation of the GitHub runner on our Kubernetes platform. In
particular, it relates to the installation processed described [here](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners-with-actions-runner-controller/quickstart-for-actions-runner-controller). For completeness,
we list the instructions that have to be executed in order to install this particular runner.

```bash
INSTALLATION_NAME="k8s-be-lanaken"
NAMESPACE="github-runners"
GITHUB_PAT="<PAT>"
helm install --update "${INSTALLATION_NAME}" \
    -f values.yaml \
    --namespace "${NAMESPACE}" \
    --set githubConfigSecret.github_token="${GITHUB_PAT}" \
    oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set
```

Note that, before configuring, a image pull secret has to be provided in order for the namespace to be able
to download from `harbor.peinser.com`.


## TODO

- [ ] Do not use GitHub PAT. Use GitHub App instead.
- [ ] Versioning of releases. Including builds to Harbor.
