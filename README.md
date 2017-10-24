# golang-alpine-dep

## What?

A Docker image with just [the official golang image for Alpine](https://hub.docker.com/_/golang/) with the [dep](https://github.com/golang/dep) dependency tool added.

## Why?

Alpine builds work best within a container due to it's use of [musl libc](https://www.musl-libc.org/). Much of the Golang community is hoping to unify dependency management on `dep`, but it's not official yet, it's not built in the official images, and, as of 24 Oct 2017, there's not an `apk add` way to install it.

You can accomplish the same thing with a two-stage Dockerfile, but you may not want to.

## Typical Use

Here are some Makefile targets to build an Alpine executable, then install it in a slimmed down Alpine container.

```Makefile
NAME = myprogram
MAIN = cmd/${NAME}.go
SOURCES = *.go ${MAIN}
BUILDDIR = build

# Alpine Build
ALPINE_BUILD = ${BUILDDIR}/alpine
ALPINE_EXE = ${ALPINE_BUILD}/${NAME}
ALPINE_TARGET_IMAGE = ${NAME}
ALPINE_BUILD_IMAGE = golang:alpine
ALPINE_WORKDIR = ${GOPATH}/src/github.com/${MYYGITHUBNAME}/${NAME}
ALPINE_GOCMD = dep ensure && go build -v -o ./services/${NAME}/${ALPINE_EXE} ./services/${NAME}/${MAIN}
ALPINE_BUILDCMD = docker run --rm -v $$PWD:${ALPINE_WORKDIR} -w ${ALPINE_WORKDIR} ${ALPINE_BUILD_IMAGE} ${ALPINE_GOCMD}


${ALPINE_BUILD}:
	mkdir -p ${ALPINE_BUILD}

${ALPINE_TARGET_IMAGE}.tmp: ${ALPINE_EXE}
	docker build -t ${ALPINE_TARGET_IMAGE} .
	touch $@

${ALPINE_EXE}: ${ALPINE_BUILD} ${SOURCES}
	# the build has to be run from the repo root due to the use of $PWD
	pushd ../.. && ${ALPINE_BUILDCMD}

```