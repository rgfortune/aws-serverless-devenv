FROM mcr.microsoft.com/devcontainers/python:3.13

USER root
RUN pip install --no-cache-dir aws-sam-cli

USER vscode
RUN curl -fsSL https://claude.ai/install.sh | bash
