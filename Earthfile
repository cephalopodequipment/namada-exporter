# Earthfile

# Base image with required dependencies
FROM ubuntu:20.04 AS base
WORKDIR /app

# Install system dependencies and Node.js 22 LTS
RUN apt-get update && \
    apt-get install -y wget curl build-essential protobuf-compiler clang-tools-14 && \
    curl -fsSL https://deb.nodesource.com/setup_22.x | bash - && \
    apt-get install -y nodejs

# Install Rust and Bun
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
ENV PATH="/root/.cargo/bin:${PATH}"
RUN curl -fsSL https://bun.sh/install | bash
ENV PATH="/root/.bun/bin:${PATH}"

# Clone the repository
COPY . /app

# Build shared library
RUN cd /app/shared && \
    bun install && \
    bun run prepublish

# Build exporter binary
RUN cd /app/exporter && \
    bun install --production --frozen-lockfile
RUN bun build ./exporter/src/index.ts --compile --outfile /app/namada-exporter

# Output the compiled binary to the local filesystem
SAVE ARTIFACT /app/namada-exporter AS LOCAL ./namada-exporter
