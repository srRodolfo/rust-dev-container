ARG RUST_VERSION=1.98
FROM rust:${RUST_VERSION}-slim

RUN apt-get update && apt-get install -y \
    pkg-config \
    libssl-dev \
    build-essential \
    tini \
    && rm -rf /var/lib/apt/lists/*

ARG PUID=1000
ARG PGID=1000

RUN groupadd -g ${PGID} dev \
    && useradd -u ${PUID} -g dev -m -s /bin/bash dev

RUN chown -R dev:dev /usr/local/cargo /usr/local/rustup

USER dev

RUN rustup component add rust-src rustfmt
RUN cargo install cargo-watch

WORKDIR /app

ENTRYPOINT ["tini", "--"]
CMD ["sleep", "infinity"]
