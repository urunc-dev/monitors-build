FROM alpine:3.20

RUN apk add --no-cache \
	build-base \
	musl-dev \
	rust \
	cargo \
	libseccomp-static \
	libcap-ng-static \
	git \
	curl \
	openssl-dev

RUN curl https://sh.rustup.rs -sSf | sh -s -- -y

ENV PATH="${PATH}:/root/.cargo/bin"

WORKDIR /workspace

#ENTRYPOINT ["/bin/sh", "-c"]
ENTRYPOINT ["/bin/sh", "-c", "echo \"$*\" | /bin/sh", "--"]
