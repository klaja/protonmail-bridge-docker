### The Deb install is just a repack of the official ProtonMail Bridge deb package with less dependencies.
### I recommend you don't use this. It's here for legacy reasons.
###
### Note: the upstream Bridge .deb referenced by PACKAGE is amd64-only, so this image is pinned to amd64.

ARG DEBIAN_IMAGE=debian:bookworm-slim

FROM --platform=linux/amd64 ${DEBIAN_IMAGE} AS build

COPY install.sh PACKAGE /
RUN apt-get update \
    && apt-get install -y --no-install-recommends wget binutils ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Repack deb (removes unnecessary dependencies and produces /protonmail.deb)
RUN bash /install.sh

FROM --platform=linux/amd64 ${DEBIAN_IMAGE}
LABEL maintainer="Simon Felding <sife@adm.ku.dk>"

EXPOSE 25/tcp
EXPOSE 143/tcp

WORKDIR /protonmail

# Copy bash scripts
COPY gpgparams entrypoint.sh PACKAGE /protonmail/
COPY --from=build /protonmail.deb /tmp/protonmail.deb

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        /tmp/protonmail.deb \
        socat \
        pass \
        libsecret-1-0 \
        ca-certificates \
        procps \
    && rm -rf /var/lib/apt/lists/*

CMD ["bash", "/protonmail/entrypoint.sh"]
