# golang-alpine-dep

**What?**: just [the official golang image for Alpine](https://hub.docker.com/_/golang/) with the [dep](https://github.com/golang/dep) dependency tool added.

**Why?**: Alpine builds work best within a container due to it's use of [musl libc](https://www.musl-libc.org/). Much of the Golang community is hoping to unify dependency management on `dep`, but it's not official yet, it's not built in the official images, and, as of 24 Oct 2017, there's not an `apk add` way to install it.
