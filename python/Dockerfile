FROM --platform=linux/arm64 ubuntu:latest

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        lua5.4 \
        python3 \
        python3-pip \
        python3-venv \
        git \
        curl \
        ripgrep \
        cmake \
        unzip \
        build-essential \
        software-properties-common \
        zsh \
        openssh-client && \
    rm -rf /var/lib/apt/lists/* && \
    chsh -s /usr/bin/zsh root

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

RUN git clone --depth 1 --branch to/python https://github.com/lucas-moraes/my-own-neovim-config.git /root/.config/nvim

RUN /usr/local/bin/nvim --headless -c 'Lazy sync' -c 'quitall'

RUN /usr/local/bin/nvim --headless -c 'TSInstall lua' -c 'quitall'

RUN /usr/local/bin/nvim --headless -c 'MasonInstall lua-language-server' -c 'quitall'

RUN export PATH="/root/.local/share/nvim/mason/bin:$PATH"

RUN set -eux; \
    NODE_VERSION="23"; \
    \
    curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - && \
    \
    apt-get install -y nodejs && \
    \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

CMD ["/usr/bin/zsh"]
