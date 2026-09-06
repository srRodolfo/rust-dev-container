ARG RUST_VERSION=1.98
FROM rust:${RUST_VERSION}-slim

RUN apt-get update && apt-get install -y \
    pkg-config \
    libssl-dev \
    build-essential \
    tini \
    && rm -rf /var/lib/apt/lists/*

RUN cargo install cargo-watch

ARG PUID=1000
ARG PGID=1000

RUN groupadd -g ${PGID} dev \
    && useradd -u ${PUID} -g dev -m -s /bin/bash dev

RUN chown -R dev:dev /usr/local/cargo

WORKDIR /home/dev/projects
RUN chown -R dev:dev /home/dev/projects

USER dev

ENTRYPOINT ["tini", "--"]
CMD ["sleep", "infinity"]
