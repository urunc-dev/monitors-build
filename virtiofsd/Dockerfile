FROM alpine:3.20

RUN apk add --no-cache \
	build-base \
	musl-dev \
	libseccomp-static \
	libcap-ng-static \
	git \
	curl \
	openssl-dev

RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y && \
	/root/.cargo/bin/rustup default stable

ENV PATH="${PATH}:/root/.cargo/bin"

WORKDIR /workspace

#ENTRYPOINT ["/bin/sh", "-c"]
ENTRYPOINT ["/bin/sh", "-c", "echo \"$*\" | /bin/sh", "--"]
