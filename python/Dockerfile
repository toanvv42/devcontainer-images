# Python devcontainer image
FROM mcr.microsoft.com/devcontainers/python:3.12

LABEL org.opencontainers.image.source="https://github.com/toanvv42/devcontainer-images"
LABEL org.opencontainers.image.description="Python 3.12 devcontainer with poetry"

# Install poetry
RUN pip install --no-cache-dir poetry

# Configure poetry to not create virtual envs (container is already isolated)
RUN poetry config virtualenvs.create false

ENV SHELL=/bin/zsh
