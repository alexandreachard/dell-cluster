FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

COPY --from=docker.io/jdxcode/mise:latest /usr/local/bin/mise /usr/local/bin/

# Activate mise in both shells + direnv for .envrc support
RUN echo 'eval "$(mise activate bash)"' >> /home/vscode/.bashrc && \
  echo 'eval "$(mise activate zsh)"' >> /home/vscode/.zshrc
