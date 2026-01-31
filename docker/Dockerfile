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
    sudo apt-get install -y nodejs && \
    sudo apt-get clean && sudo rm -rf /var/lib/apt/lists/*

RUN sudo apt update -y && \
    sudo apt install -y build-essential zip rsync jq git gettext-base wget curl python3 golang-go && \
    sudo wget -qO /usr/local/bin/yq https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 && sudo chmod a+x /usr/local/bin/yq && \
    curl -sSLf "$(curl -sSLf https://api.github.com/repos/tomwright/dasel/releases/latest | grep browser_download_url | grep linux_amd64 | grep -v .gz | cut -d\" -f 4)" -L -o dasel && chmod +x dasel && \
    sudo mv ./dasel /usr/local/bin/dasel && \
    sudo apt-get clean
