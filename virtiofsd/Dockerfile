# Copyright (c) 2022 Intel
#
# SPDX-License-Identifier: Apache-2.0

FROM alpine:3.16.2

ENV RUSTUP_HOME="/opt/rustup"
ENV CARGO_HOME="/opt/cargo"
ENV PATH="/opt/cargo/bin/:${PATH}"

SHELL ["/bin/ash", "-o", "pipefail", "-c"]

RUN mkdir ${RUSTUP_HOME} ${CARGO_HOME} && chmod -R a+rwX ${RUSTUP_HOME} ${CARGO_HOME}

RUN apk --no-cache add \
        curl \
        gcc \
        git \
        libcap-ng-static \
        libseccomp-static \
        musl-dev && \
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y --default-toolchain

RUN ARCH=$(uname -m); \
    	rust_arch=""; \
    	libc=""; \
    	arch_libc=""; \
    	extra_rust_flags=" -C link-self-contained=yes"; \
        	case "${ARCH}" in \
    	        "aarch64") rust_arch="${ARCH}"; libc="musl"; arch_libc="" ;; \
    	        "x86_64") rust_arch="${ARCH}"; libc="musl"; arch_libc="" ;; \
    	        *) echo "Unsupported architecture: ${ARCH}" && exit 1 ;; \
        	esac; \
        	echo "RUST_ARCH=${rust_arch}" > /etc/profile.d/rust.sh; \
    	echo "LIBC=${libc}" >> /etc/profile.d/rust.sh; \
    	echo "ARCH_LIBC=${arch_libc}" >> /etc/profile.d/rust.sh; \
    	echo "EXTRA_RUST_FLAGS=\"${extra_rust_flags}\"" >> /etc/profile.d/rust.sh;

ENTRYPOINT ["/bin/sh", "-c", "echo \"$*\" | /bin/sh", "--"]
