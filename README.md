# Bazel build rules for GNU Bison

## Overview

```python
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

http_archive(
    name = "rules_m4",
    urls = ["https://github.com/jmillikin/rules_m4/releases/download/v0.2/rules_m4-v0.2.tar.xz"],
    sha256 = "c67fa9891bb19e9e6c1050003ba648d35383b8cb3c9572f397ad24040fb7f0eb",
)
load("@rules_m4//m4:m4.bzl", "m4_register_toolchains")
m4_register_toolchains()

http_archive(
    name = "rules_bison",
    urls = ["https://github.com/jmillikin/rules_bison/releases/download/v0.2/rules_bison-v0.2.tar.xz"],
    sha256 = "6ee9b396f450ca9753c3283944f9a6015b61227f8386893fb59d593455141481",
)
load("@rules_bison//bison:bison.bzl", "bison_register_toolchains")
bison_register_toolchains()
```

```python
load("@rules_bison//bison:bison.bzl", "bison_cc_library")
bison_cc_library(
    name = "hello",
    src = "hello.y",
)
cc_binary(
    name = "hello_bin",
    deps = [":hello"],
)
```

```python
load("@rules_bison//bison:bison.bzl", "bison_java_library")
bison_java_library(
    name = "HelloJavaParser",
    src = "hello_java.y",
)
java_binary(
    name = "HelloJava",
    srcs = ["HelloJava.java"],
    main_class = "HelloJava",
    deps = [":HelloJavaParser"],
)
```

## Other Rules

```python
load("@rules_bison//bison:bison.bzl", "bison")
bison(
    name = "hello_bin_srcs",
    src = "hello.y",
)
cc_binary(
    name = "hello_bin",
    srcs = [":hello_bin_srcs"],
)
```

```python
genrule(
    name = "hello_gen",
    srcs = ["hello.y"],
    outs = ["hello_gen.c"],
    cmd = "M4=$(M4) $(BISON) --output=$@ $<",
    toolchains = [
        "@rules_bison//bison:current_bison_toolchain",
        "@rules_m4//m4:current_m4_toolchain",
    ],
)
```

## Toolchains

```python
load("@rules_bison//bison:bison.bzl", "BISON_TOOLCHAIN_TYPE", "bison_toolchain")
load("@rules_m4//m4:m4.bzl", "M4_TOOLCHAIN_TYPE")

def _my_rule(ctx):
    bison = bison_toolchain(ctx)
    ctx.actions.run(
        executable = bison.bison_tool,
        env = bison.bison_env,
        # ...
    )

my_rule = rule(
    _my_rule,
    toolchains = [
        BISON_TOOLCHAIN_TYPE,
        M4_TOOLCHAIN_TYPE,
    ],
)
```
