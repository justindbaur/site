---
slug: msbuild-sdk
title: Build a crate like nuget package
authors: jb
tags: [nuget, dotnet, rust]
---

Rust crates are very powerful, they allow to to distribute source files and include feature flags so consumers can choose what they need. You can do that with a nuget package too.

<!-- truncate -->

I will show you how to build a nuget package that allows your consumers to choose what they need and leave behind what they don't.

