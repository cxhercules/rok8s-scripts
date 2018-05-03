FROM golang:1.10-alpine AS gobuild-base
RUN apk add --no-cache \
	bash \
	build-base \
	gcc \
	git \
	libseccomp-dev \
	linux-headers \
	make

FROM gobuild-base AS runc
ARG RUNC_VERSION
RUN git clone https://github.com/jessfraz/runc.git "$GOPATH/src/github.com/opencontainers/runc" \
	&& cd "$GOPATH/src/github.com/opencontainers/runc" \
	&& git checkout -q "all-rootless-patches" \
	&& make static BUILDTAGS="seccomp" EXTRA_FLAGS="-buildmode pie" EXTRA_LDFLAGS="-extldflags \\\"-fno-PIC -static\\\"" \
	&& mv runc /usr/bin/runc

FROM alpine:3.7
RUN apk add --no-cache \
	bash \
	fuse \
	git \
	shadow \
	shadow-uidmap \
	strace \
  jq \
  wget \
  curl \
  py-pip \
  sudo

COPY --from=runc /usr/bin/runc /usr/bin/runc

RUN curl -o /usr/local/bin/img -LO https://github.com/genuinetools/img/releases/download/v0.3.1/img-linux-amd64
RUN chmod +x /usr/local/bin/img

COPY bin /usr/local/bin

RUN install-rok8s-requirements
