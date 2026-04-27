FROM mcr.microsoft.com/devcontainers/dotnet:10.0

ARG NODE_VERSION=22
RUN curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm@latest \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# GitHub CLI
RUN (type -p wget >/dev/null || apt-get install -y wget) \
    && mkdir -p -m 755 /etc/apt/keyrings \
    && out=$(mktemp) && wget -nv -O$out https://cli.github.com/packages/githubcli-archive-keyring.gpg \
    && cat $out | tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
    && chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
       | tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
    && apt-get update \
    && apt-get install -y gh \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Squad CLI
RUN npm install -g @bradygaster/squad-cli

# devtunnel CLI
RUN curl -sL https://aka.ms/DevTunnelCliInstall | bash
