---
title: "Ironbuild"
date: 2019-12-17T23:04:01Z
draft: false
tags: []
---

Ironbuild is my scattered list of suggestions for the 'perfect' build tool, designed for small-to-medium 
companies. Most people suffer from the complexity added by 'google scale' software, and something simpler
would be beneficial.

There are many motivations. First, companies attempting to introduce build caching into their pipeline
are expected to have a team of engineers around to maintain the infrastructure. Managing a microservices-based 
build server and a fleet of workers is time-consuming and there is an entire market
that is being ignored. Second, the complexity in the Bazel ecosystem (both for maintainers and users) is
unapproachable. Either you have a Bazel monorepo or you don't; there is no in-between. Third, protocol 
buffers are [less than desirable](https://reasonablypolymorphic.com/blog/protos-are-wrong/index.html),
making any code using them a bit crap.

We start with a build client, and work outwards as needed. To start with, we'll be compatible with
remote exec, and remote cache. Ship with a minimal docker container that you can run to build
things locally.

We do not care about:
- configuration validation (cue over skylark)
- sandboxing (use docker)

**Cross compiling as a first class citizen:** All rules should be able to cross compile meaning that the
architecture of the worker is irrelevant. 

**Ergonomics:** To enable adoption, we need clear errors, great tooling, simple adoption, and a common
core of well maintained plugins. Cross compilation should be a property of the configuration, not up
to the rules.

**Clear SoC:** Bazel muddles the declarative build instruction definition and the imperative
build process definitions. Why is it that, when so many tools are capable of splitting these (K8S for
example), that Bazel was unable to do this? For writing rules, starlark is a great way to enforce
encapsulation. But why do the definitions have to use it? Why not use yaml files to define the targets,
and have a plugin system to define rules? This would help a lot with making the builds more approachable.

K8S files define a state. Applying that state causes K8S to calculate how to make it reality. In this analogy,
the 'rules' would be the k8s kind, a ReplicaSet for example, and the config has specific parameters that describe how it
should behave. Why is Bazel not the same? Why is a target not some declarative format that describes some
desired state (that a given binary is built, or that a given docker container is pushed) that the build tool 
must make a reality?

```yaml
kind: rust_binary
platform: windows
srcs: 
- Main.rs
deps:
- warp
---
kind: rust_crate
name: warp
version: 0.1.2
```

Rules (plugins) should be sandboxed (WebAssembly?) with a common interface, 
with the additional bonus that plugins can be written in any (compiled) language of choice.

- vastly simplified client code
- vastly simplified end-user code (no mixing declarative + imperative)
- familiar semantics to other declarative tools
- no DSL

**Remote-Only:** To simplify the architecture the system is remote-only. If you want to build locally,
you'll need either docker or minikube on your machine. The complexity that is saved handing both local _and_
remote builds will let us properly nail the toolchain.


