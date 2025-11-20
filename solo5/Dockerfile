FROM alpine:3.16.2

RUN apk --no-cache add \
        curl \
        gcc \
        git \
        libseccomp-static \
        pkgconf \
        libseccomp-dev \
        make \
        musl-dev

ENTRYPOINT ["/bin/sh", "-c", "echo \"$*\" | /bin/sh", "--"]
