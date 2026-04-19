FROM gcr.io/kaniko-project/executor AS kaniko

FROM ghcr.io/actions/actions-runner:latest AS runner

# Add Kaniko to the runner by default
COPY --from=kaniko /kaniko/executor /bin/kaniko
COPY --from=kaniko /kaniko/docker-credential-gcr /bin/docker-credential-gcr
COPY --from=kaniko /kaniko/docker-credential-ecr-login /bin/docker-credential-ecr-login
COPY --from=kaniko /kaniko/docker-credential-acr-env /bin/docker-credential-acr-env

RUN sudo apt-get update -y && \
    sudo apt-get install -y curl ca-certificates gnupg && \
    curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash - && \
    sudo apt-get install -y nodejs bsdextrautils && \
    sudo apt-get clean && sudo rm -rf /var/lib/apt/lists/*

RUN sudo apt update -y && \
    sudo apt install -y build-essential zip rsync jq git gettext-base wget curl python3 golang-go && \
    sudo wget -qO /usr/local/bin/yq https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 && sudo chmod a+x /usr/local/bin/yq && \
    curl -sSLf "$(curl -sSLf https://api.github.com/repos/tomwright/dasel/releases/latest | grep browser_download_url | grep linux_amd64 | grep -v .gz | cut -d\" -f 4)" -L -o dasel && chmod +x dasel && \
    sudo mv ./dasel /usr/local/bin/dasel && \
    sudo apt-get clean

#  GitOps / policy toolchain: sops, age, opa, cosign
ARG SOPS_VERSION=3.12.2
ARG AGE_VERSION=1.3.1
ARG COSIGN_VERSION=3.0.6
ARG CRANE_VERSION=0.21.5

RUN set -eux; \
    sudo curl -fsSL -o /usr/local/bin/sops \
      "https://github.com/getsops/sops/releases/download/v${SOPS_VERSION}/sops-v${SOPS_VERSION}.linux.amd64" && \
    sudo chmod +x /usr/local/bin/sops; \
    curl -fsSL "https://github.com/FiloSottile/age/releases/download/v${AGE_VERSION}/age-v${AGE_VERSION}-linux-amd64.tar.gz" \
      | sudo tar -xz -C /usr/local/bin --strip-components=1 age/age age/age-keygen; \
    sudo curl -fsSL -o /usr/local/bin/cosign \
      "https://github.com/sigstore/cosign/releases/download/v${COSIGN_VERSION}/cosign-linux-amd64" && \
    sudo chmod +x /usr/local/bin/cosign && \
    curl -sL "https://github.com/google/go-containerregistry/releases/download/v${CRANE_VERSION}/go-containerregistry_Linux_x86_64.tar.gz" \
      | sudo tar -xz -C /usr/local/bin crane && \
    sudo chmod +x /usr/local/bin/crane
