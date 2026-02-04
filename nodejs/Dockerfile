FROM --platform=linux/arm64 ubuntu:latest

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    git \
    curl \
    ripgrep \
    unzip \
    build-essential \
    zsh \
    fzf \
    fd-find \
    bat \
    eza \
    openssh-client \
    python3 \
    python3-pip \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/* && \
    chsh -s /usr/bin/zsh root && \
    ln -sf /usr/bin/fdfind /usr/local/bin/fd

ENV NVM_DIR=/root/.nvm
ENV NODE_VERSION=20

RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash && \
    export NVM_DIR="$NVM_DIR" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" && \
    nvm install $NODE_VERSION && \
    nvm use $NODE_VERSION && \
    nvm alias default $NODE_VERSION && \
    npm install -g \
        typescript \
        ts-node \
        nodemon \
        eslint \
        prettier \
        http-server \
        pm2

ENV PATH="$NVM_DIR/versions/node/v$(ls $NVM_DIR/versions/node | head -1)/bin:$PATH"

RUN set -eux; \
    NVIM_URL="https://github.com/neovim/neovim/releases/download/stable/nvim-linux-arm64.tar.gz"; \
    \
    curl -sSLO "${NVIM_URL}" && \
    tar xzf nvim-linux-arm64.tar.gz && \
    mv nvim-linux-arm64 /opt/nvim-bin && \
    ln -s /opt/nvim-bin/bin/nvim /usr/local/bin/nvim && \
    rm nvim-linux-arm64.tar.gz

RUN set -eux; \
    LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep -Po '"tag_name": "v\K[^"]*') && \
    LAZYGIT_FILE="lazygit_${LAZYGIT_VERSION}_Linux_arm64.tar.gz"; \
    LAZYGIT_URL="https://github.com/jesseduffield/lazygit/releases/download/v${LAZYGIT_VERSION}/${LAZYGIT_FILE}"; \
    \
    curl -sSLO "${LAZYGIT_URL}" && \
    tar xzf "${LAZYGIT_FILE}" lazygit && \
    install lazygit /usr/local/bin && \
    rm "${LAZYGIT_FILE}" lazygit

RUN git clone --depth 1 --branch to/ts-react-node https://github.com/lucas-moraes/neovim-config.git /root/.config/nvim

RUN export NVM_DIR="$NVM_DIR" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" && \
    /usr/local/bin/nvim --headless -c 'Lazy sync' -c 'quitall' 2>/dev/null || true

RUN export NVM_DIR="$NVM_DIR" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" && \
    /usr/local/bin/nvim --headless -c 'TSInstall javascript typescript' -c 'quitall' 2>/dev/null || true

RUN export NVM_DIR="$NVM_DIR" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" && \
    /usr/local/bin/nvim --headless -c 'MasonInstall typescript-language-server eslint-lsp prettier' -c 'quitall' 2>/dev/null || true

RUN export PATH="/root/.local/share/nvim/mason/bin:$PATH"

RUN set -eux; \
    git clone --depth 1 https://github.com/lucas-moraes/term-explorer.git /opt/term-explorer && \
    install -d /root/.config/term-explorer && \
    install -m 0644 /opt/term-explorer/term-explorer.zsh /root/.config/term-explorer/term-explorer.zsh && \
    install -m 0644 /opt/term-explorer/README.md /root/.config/term-explorer/README.md && \
    install -m 0644 /opt/term-explorer/LICENSE /root/.config/term-explorer/LICENSE && \
    printf '\n# term-explorer\nsource /opt/term-explorer/term-explorer.zsh\n' >> /etc/zsh/zshrc && \
    cat >/usr/local/bin/term-explorer <<'EOF'
#!/usr/bin/env zsh
source /opt/term-explorer/term-explorer.zsh
term-explorer "$@"
EOF

RUN chmod +x /usr/local/bin/term-explorer && \
    ln -sf /usr/local/bin/term-explorer /usr/local/bin/te

WORKDIR /workspace

CMD ["/usr/bin/zsh"]
