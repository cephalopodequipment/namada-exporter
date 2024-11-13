VERSION 0.8

# Define the build target
build:
    FROM +setup

    # Save the compiled binary as a local artifact
    SAVE ARTIFACT /app/namada-exporter AS LOCAL ./namada-exporter

# Setup stage with dependencies and build steps
setup:
    FROM ubuntu:22.04
    WORKDIR /app
    ENV DEBIAN_FRONTEND=noninteractive

    # Install system dependencies and Node.js 22 LTS
    RUN apt-get update && \
        apt-get install -y wget curl build-essential protobuf-compiler unzip clang-tools-14 && \
        curl -fsSL https://deb.nodesource.com/setup_22.x | bash - && \
        apt-get install -y nodejs

    # Install Rust and Bun
    RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
    ENV PATH="/root/.cargo/bin:${PATH}"
    RUN curl -fsSL https://bun.sh/install | bash
    ENV PATH="/root/.bun/bin:${PATH}"

    # Copy project files
    COPY . /app

    # Build shared library
    RUN cd /app/shared && \
        bun install && \
        bun run prepublish

    # Build exporter binary
    RUN cd /app/exporter && \
        bun install --production --frozen-lockfile
    RUN bun build ./exporter/src/index.ts --compile --outfile /app/namada-exporter