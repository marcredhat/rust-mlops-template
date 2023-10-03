FROM docker.io/rust:bookworm AS build


WORKDIR /app

RUN apt-get update \
    && apt-get install -y \
    curl \
    pkg-config \
    libssl-dev
COPY server/Cargo.toml server/Cargo.lock ./
COPY server/src/ src/
RUN cargo clean && cargo build --release
RUN find . -type d | grep "libtorch/lib$" | xargs -I{} mv {} libtorch

FROM ubuntu:22.04

RUN apt-get update && apt install -y openssl libgomp1 && apt install -y g++-11

WORKDIR /app
COPY --from=build /app/target/release/actix-torch-server /usr/local/bin/actix-torch-server
copy --from=build /app/libtorch/* /usr/lib/
COPY saved_model/ /app/saved_model/

RUN useradd -mU -s /bin/bash actix
USER actix

EXPOSE 8080
ENTRYPOINT ["actix-torch-server", "--model-path=/app/saved_model/model.pt"]
# podman run -it -d -p 8080:8080 0c7a0672c33c
