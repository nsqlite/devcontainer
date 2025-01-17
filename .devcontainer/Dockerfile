FROM golang:1.23.4-bookworm AS golang
FROM debian:bookworm

LABEL org.opencontainers.image.source=https://github.com/nsqlite/nsqlitedc
LABEL org.opencontainers.image.description="NSQLite Dev Container"
LABEL org.opencontainers.image.licenses=MIT

ENV DEBIAN_FRONTEND="noninteractive"

ENV CGO_ENABLED=1
ENV GOBIN="/usr/local/gobin"
RUN mkdir -p /usr/local/gobin

ENV PATH="$PATH:/usr/local/go/bin"
ENV PATH="$PATH:/usr/local/gobin"
ENV PATH="$PATH:/usr/local/zig"

# Copy the golang binaries
COPY --from=golang /usr/local/go /usr/local/go

# Install system dependencies
RUN apt-get update && \
    apt-get install -y gcc ccache wget curl xz-utils git zip unzip p7zip-full net-tools sqlite3 && \
    rm -rf /var/lib/apt/lists/*

# Install dependencies with go install
RUN go install github.com/go-task/task/v3/cmd/task@v3.40.1 && \
    go install github.com/golangci/golangci-lint/cmd/golangci-lint@v1.63.4

# Install zig lang
# TODO: Add support for ARM and other architectures
RUN wget --no-verbose https://ziglang.org/download/0.13.0/zig-linux-x86_64-0.13.0.tar.xz && \
    tar -xf ./zig-linux-x86_64-0.13.0.tar.xz && \
    mv ./zig-linux-x86_64-0.13.0 /usr/local/zig && \
    rm -rf ./zig-linux-x86_64-* && \
    chmod +x /usr/local/zig/zig

# Add the startup script on every bash session
COPY ./startup.sh /usr/local/bin/startup.sh
RUN echo "\n\n" >> /root/.bashrc && \
    cat /usr/local/bin/startup.sh >> /root/.bashrc

# Sleep infinity to keep the container running
CMD ["sleep", "infinity"]
