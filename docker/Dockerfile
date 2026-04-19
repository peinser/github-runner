FROM gcr.io/kaniko-project/executor AS kaniko

FROM ghcr.io/actions/actions-runner:latest AS runner

COPY --from=kaniko /kaniko/executor /bin/kaniko
COPY --from=kaniko /kaniko/docker-credential-gcr /bin/docker-credential-gcr
COPY --from=kaniko /kaniko/docker-credential-ecr-login /bin/docker-credential-ecr-login
COPY --from=kaniko /kaniko/docker-credential-acr-env /bin/docker-credential-acr-env

RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        gnupg \
        build-essential \
        zip \
        rsync \
        jq \
        git \
        gettext-base \
        wget \
        python3 \
        golang-go \
        bsdextrautils \
    && curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
    && apt-get install -y nodejs \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*


ARG TARGETARCH

RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) BIN_ARCH="amd64"; GO_ARCH="x86_64" ;; \
        arm64) BIN_ARCH="arm64"; GO_ARCH="arm64" ;; \
        *) echo "Unsupported arch: ${TARGETARCH}" && exit 1 ;; \
    esac; \
    \
    curl -fsSL -o /usr/local/bin/yq \
        "https://github.com/mikefarah/yq/releases/latest/download/yq_linux_${BIN_ARCH}" \
    && chmod +x /usr/local/bin/yq; \
    \
    DASIL_URL="$(curl -sSLf https://api.github.com/repos/tomwright/dasel/releases/latest \
        | grep browser_download_url \
        | grep linux_${BIN_ARCH} \
        | grep -v '.gz' \
        | cut -d\" -f4)" \
    && curl -fsSL "$DASIL_URL" -o /usr/local/bin/dasel \
    && chmod +x /usr/local/bin/dasel


ARG SOPS_VERSION=3.12.2
ARG AGE_VERSION=1.3.1
ARG COSIGN_VERSION=3.0.6
ARG CRANE_VERSION=0.21.5

RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) LINUX_ARCH="amd64"; GOCRANE_ARCH="x86_64" ;; \
        arm64) LINUX_ARCH="arm64"; GOCRANE_ARCH="arm64" ;; \
    esac; \
    \
    curl -fsSL -o /usr/local/bin/sops \
        "https://github.com/getsops/sops/releases/download/v${SOPS_VERSION}/sops-v${SOPS_VERSION}.linux.${LINUX_ARCH}" \
    && chmod +x /usr/local/bin/sops; \
    \
    curl -fsSL "https://github.com/FiloSottile/age/releases/download/v${AGE_VERSION}/age-v${AGE_VERSION}-linux-${LINUX_ARCH}.tar.gz" \
        | tar -xz -C /usr/local/bin --strip-components=1 age/age age/age-keygen; \
    \
    curl -fsSL -o /usr/local/bin/cosign \
        "https://github.com/sigstore/cosign/releases/download/v${COSIGN_VERSION}/cosign-linux-${LINUX_ARCH}" \
    && chmod +x /usr/local/bin/cosign; \
    \
    curl -fsSL \
        "https://github.com/google/go-containerregistry/releases/download/v${CRANE_VERSION}/go-containerregistry_Linux_${GOCRANE_ARCH}.tar.gz" \
        | tar -xz -C /usr/local/bin crane \
    && chmod +x /usr/local/bin/crane