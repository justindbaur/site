---
slug: nuget-crate
title: NuGet Crate
authors: jb
tags: [nuget, dotnet, rust]
---

Rust crates are very powerful, they allow to to distribute source files and include feature flags so consumers can choose what they need. You can do that with a nuget package too.

<!-- truncate -->

NuGet packages can pretty easily include source files that will be added to the consuming project. You do this by packing files into the `contentFiles` area, like below.

```xml
<ItemGroup>
  <Content 
    Include="**/*.cs"
    Pack="true"
    PackagePath="contentFiles/cs/any/MyPackage"
    Visible="false"
    BuildAction="compile" />
</ItemGroup>
```

Lets break apart what is going on there, you are telling nuget to include all the `*.cs` files relative to the current file and pack them into the `contentFiles/cs/any/MyPackage` directory inside the NuGet package. It's helpful to know that a `nupkg` file is really just a zipped up directory. In fact, you can rename it from `MyPackage1.0.0.nupkg` to `MyPackage1.0.0.zip` and then peak inside it to see the layout. The `cs` portion of the file path says that you are providing the C# code language, other possible values are `vb` for Visual Basic, `fs` for F#, and `any`. The next portion is the target framework, by saying `any` the files in that directory will be used no matter what framework the consuming project is targeting. If your source files should only be used in certain target frameworks, you can replace this something like `net9.0` instead. `MyPackage` just refers to the name of your package, so replace that with your own name. The `BuildAction="compile"` just tells the compiler to include those files as part of compilation. Read more about `contentFiles` [here](https://learn.microsoft.com/en-us/nuget/reference/nuspec#package-folder-structure).

You are now including raw source files in your nuget package and they are compiled and to make feature flags, you can just use `<DefineConstants>$(DefineConstants);MY_FEATURE</DefineConstants>` to turn a feature on. Then, in the code you are including in the nuget package just add `#if MY_FEATURE [code] endif` to gate whether that feature is enabled. This is awesome, and can allow you to do a lot of cool things, but where it lacks is what if your feature requires another nuget package to work? Well, you could just write good documentation that when they define certain features they also need to add a `<PackageReference Include="MyFeaturePackage" Version="1.0.0" />`. But that feels pretty fragile, can we do something a bit more automatic? The answer is yes.

The answer to this is MSBuild SDKs. With normal nuget packages, you only get to insert yourself into compilation but with MSBuild SDKs, you get to insert yourself pretty much everywhere, most importantly, package resolution. All an MSBuild SDK is is two files, `Sdk/Sdk.props` & `Sdk/Sdk.targets`. Then you can reference the SDK in your project like such:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <Sdk Name="MySdk" Version="1.0.0" />
</Project>
```