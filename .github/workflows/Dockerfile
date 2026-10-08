FROM python:3.14-trixie AS base

COPY resources/zscaler-root.pem /usr/local/share/ca-certificates/zscaler.crt
RUN update-ca-certificates

RUN apt-get update -y &&  \
    apt-get install -y \
    poppler-utils \
    pipx

RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN pipx ensurepath

FROM base AS builder

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    UV_INSTALL_DIR=/usr/local/bin \
    UV_SYSTEM_CERTS=true \
    UV_NO_DEV=1 \
    VIRTUAL_ENV=/usr/local \
    UV_HTTP_TIMEOUT=600 \
    UV_HTTP_RETRIES=10 \
    UV_CONCURRENT_DOWNLOADS=100 \
    UV_PROJECT_ENVIRONMENT=/usr/local \
    UV_CACHE_DIR=/root/.cache/uv

# Download the latest installer
RUN pipx install uv --global

FROM builder AS uv-install

RUN --mount=type=cache,id=doc-api-uv,target=/root/.cache/uv \
    --mount=type=bind,source=uv.lock,target=uv.lock \
    --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
    uv sync
