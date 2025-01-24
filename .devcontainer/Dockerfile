FROM golang:1.23.5-bookworm AS golang
FROM denoland/deno:debian-2.1.6 AS deno
FROM debian:bookworm

LABEL org.opencontainers.image.source=https://github.com/nsqlite/nsqlitedc
LABEL org.opencontainers.image.description="NSQLite Dev Container"
LABEL org.opencontainers.image.licenses=MIT

# Base environment variables
ENV DEBIAN_FRONTEND="noninteractive"

# Install golang from docker image
COPY --from=golang /usr/local/go /usr/local/go
RUN mkdir -p /usr/local/gobin
ENV CGO_ENABLED=1
ENV GOBIN="/usr/local/gobin"
ENV PATH="$PATH:/usr/local/go/bin"
ENV PATH="$PATH:/usr/local/gobin"

# Install deno from docker image
COPY --from=deno /usr/bin/deno /usr/local/bin/deno

# Install system dependencies
RUN apt-get update && \
    apt-get install -y \
        gcc wget curl iputils-ping xz-utils git \
        zip unzip p7zip-full net-tools sqlite3 && \
    rm -rf /var/lib/apt/lists/*

# Install dependencies with go install
RUN go install github.com/go-task/task/v3/cmd/task@v3.40.1 && \
    go install github.com/golangci/golangci-lint/cmd/golangci-lint@v1.63.4

# Install zig lang
# TODO: Add support for ARM and other architectures
ENV PATH="$PATH:/usr/local/zig"
RUN wget --no-verbose https://ziglang.org/download/0.13.0/zig-linux-x86_64-0.13.0.tar.xz && \
    tar -xf ./zig-linux-x86_64-0.13.0.tar.xz && \
    mv ./zig-linux-x86_64-0.13.0 /usr/local/zig && \
    rm -rf ./zig-linux-x86_64-* && \
    chmod +x /usr/local/zig/zig

# Install rust
ENV PATH="$PATH:/usr/local/cargo/bin"
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -q -y

# Install dependencies with cargo install
RUN cargo install --locked cargo-zigbuild

# Default git config
# https://github.com/golangci/golangci-lint/issues/4033
RUN git config --global --add safe.directory '*'

# Add the startup script on every bash session
COPY ./startup.sh /usr/local/bin/startup.sh
RUN echo "\n\n" >> /root/.bashrc && \
    cat /usr/local/bin/startup.sh >> /root/.bashrc

# Sleep infinity to keep the container running
CMD ["sleep", "infinity"]
