FROM ghcr.io/actions/actions-runner:latest AS runner

RUN sudo apt update -y && \
    sudo apt install -y zip jq git gettext-base wget curl && \
    curl -sSLf "$(curl -sSLf https://api.github.com/repos/tomwright/dasel/releases/latest | grep browser_download_url | grep linux_amd64 | grep -v .gz | cut -d\" -f 4)" -L -o dasel && chmod +x dasel && \
    sudo mv ./dasel /usr/local/bin/dasel && \
    sudo apt-get clean