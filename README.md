# The SPM Cheatsheet

> [!NOTE]
> Changes applied here will be automatically deployed at [SwiftToolkit](https://SwiftToolkit.dev/spm-cheatsheet). [Contributions](https://github.com/swifttoolkit/spm-cheatsheet/pulls/new) are welcome!

<!-- Content start -->

## Adding Package Dependencies

To add a `Package.Dependency` to your package, there are a few options. It's important to note that you're not yet adding the dependency to your targets - this requires another step when declaring a `Target`'s dependencies.

### Remote Dependencies

```swift
dependencies: [
    .package(url: "https://github.com/Alamofire/Alamofire.git", from: "5.8.1"),
],
```
### Choosing Versions

The second parameter on the method above gives you control of what version of a dependency you're interested in.

```swift
.package(url: "example.com/repo", from: "2.3.4"),
```
When using the `from: "x.y.z"` alternative, SPM will select minor and patch versions higher than the one passed. In the example above, it means `2.3.4`, `2.3.5` or `2.4.0`, but never `3.0.0`.

To choose a **specific version**, one must use the alternative method with the `exact` parameter:
```swift
.package(url: "example.com/repo", exact: "3.0.2"),
```

To choose a branch:

```swift
.package(url: "example.com/repo", branch: "my-great-feature"),
```

Alternatively, to choose a specific commit
```swift
.package(url: "example.com/repo", revision: "40ee0820"),
```

There are also other methods that allow choosing a range of versions:

```swift
.package(url: "example.com/repo", "1.2.3"..."1.2.6"),
```

This alternative uses the `Range` operators, and they can use both `Range` or `ClosedRange` (up to, but not including, with the `..<` operator).

### Local Dependencies

SPM allows you referencing a dependency that is available locally in your machine, being a repository in a different folder. This is essential when developing multiple packages.

```swift
.package(path: "relative-or-absolute-path-to-another-package")
```

When using a local dependency, there is no option to pass a version, branch or commit: SPM picks the current state of the repository in the file system, allowing the source files of the dependency to be edited.

## Targets Vs Products

While they might sound confusing, target and products are not exactly the same. A `Target` is the  basic building block of a package: it contains source files and possibly resources as well. Targets can depend on other targets. A `Product`, in the other hand, is how you can wrap a target and offer it to external consumers of your package. It can be either a library, an executable, or a plugin - you can find more on that below.

## Targets

To declare a regular target (meaning, not an executable nor a plugin, but a module), use the following static function:

```swift
.target(name: "my-module")
```

The only required parameter in this function is the target name. By default, SPM will look for source files under `Sources/[TargetName]`. If you have the source files located in a different directory, you can use the `path` parameter:

```swift
.target(name: "my-module", path: "Sources/another-folder")
```

Note that, when using this variant, SPM requires you to use a path **inside** within the scope of the package folder, so you cannot use directories that live outside it.

A target can also be a test target:

```swift
.testTarget(name: "my-module-tests")
```

Or, alternatively, an executable target, such as a command line tool that has dependency on the Swift Argument Parser:

```swift
.executableTarget(
    name: "my-cli",
    dependencies: [
        .product(name: "ArgumentParser", package: "swift-argument-parser")
    ]
)
```

### Target Dependencies

It is very common to have a target depend on other targets, or other package's products. This can be achieve by adding a `Target.Dependency` using the `dependencies` parameter:

To add a dependency on **another target from the same package**:

```swift
.target(name: "my-module", dependencies: [
    .target("another-module")
])
```

It is very common, though, to use a product vendored by another package. To add one, you must have added the package as a `Package.Dependency` first, as explained in the [_Adding Package Dependencies_](#adding-package-dependencies) section above. After that, you can reference one of its products:

```swift
.target(name: "my-module", dependencies: [
    .product(name: "Alamofire", package: "Alamofire"),
    .product(name: "NukeUI", package: "Nuke"),
])
```

## Products (coming soon)

<!-- Content end -->