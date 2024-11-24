---
sidebar_position: 1
---

# Installation

To install pretender:

```bash
dotnet add package Pretender
```

Then, allow the interceptors namespace by adding:

```xml
<InterceptorsNamespace>$(InterceptorsNamespace);Pretender.SourceGeneration</InterceptorsNamespace>
```

You should be ready to start using it. Go to the next step to start making your first `Pretend`.