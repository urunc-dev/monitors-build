FROM alpine:3.20

RUN apk add --no-cache \
	build-base \
	musl-dev \
	rust \
	cargo \
	libseccomp-static \
	libcap-ng-static \
	git \
	openssl-dev

RUN curl https://sh.rustup.rs -sSf | sh -s -- -y

WORKDIR /workspace

ENTRYPOINT ["/bin/sh", "-c"]
