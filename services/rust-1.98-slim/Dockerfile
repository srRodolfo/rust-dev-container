FROM rust:1.98-slim

RUN apt-get update && apt-get install -y \
    pkg-config \
    libssl-dev \
    build-essential \
    tini \
    && rm -rf /var/lib/apt/lists/*

RUN cargo install cargo-watch

RUN groupadd -g 1000 dev \
    && useradd -u 1000 -g dev -m -s /bin/bash dev

RUN chown -R dev:dev /usr/local/cargo

WORKDIR /projects
RUN chown -R dev:dev /projects

USER dev

ENTRYPOINT ["tini", "--"]
CMD ["sleep", "infinity"]
