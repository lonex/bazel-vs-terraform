
# The uncanny resemblance between Bazel and Terraform

[Bazel][bazel] and [Terraform][terraform] are disigned to solve completely different problem. One is used for infra-structure provisioning and configuration; the other is for building and packaging software. Yet, the interface, API and user experience of both demonstrate similarity. This document intends to introduce Bazel to people who are familiar with Terraform or vice versa.

## Concept

Terraform has `workspace` and `module`, while Bazel has `workspace`, `repository` and `package`. The idea of the abstraction behind Terraform [module][tf_module] and Bazel [repository][bazel_repository] and package are similar. They both serve an conceptual unit of the work, and have similar requirement on code organization.

Terraform's goal is to have a uniform and consistent interface to manage different cloud infra-structure, while Bazel's goal is to have a uniform and consistent interface to build software for different languages.

## Cache

Terraform has local and remote state. Terraform state actually stores cache of the attributes [for performance][tf_state].

> In addition to basic mapping, Terraform stores a cache of the attribute values for all resources in the state. This is the most optional feature of Terraform state and is done only as a performance improvement.

Some of the Terraform providers heavily depend on the cached data to function, e.g. Terraform Helm Provider.
The remote state is shared by multiple client to interact with the remote infra-structure.

Bazel has local and [remote cache][bazel_remote].

> A remote cache is used by a team of developers and/or a continuous integration (CI) system to share build outputs.

## Language and syntax

[HCL][hcl] is the interface language to write Terraform code. Bazel uses the [Starlark language][starlark]. They are both DSL-like high-level languages.

HCL supports the following types: `string`, `number`, `bool`, `list`, `map` and `null`. Starlark supports `string`, `int`, `bool`, `list`, `dict`, `None` and `function`. This is how similar the `for` expression between the two,

Terraform code
```
[for s in foo_list : upper(s) if s != ""]
```
vs Bazel code
```starlark
[s.upper() for s in foo_list if s != ""]
```

Each language has its own set of expressions, functions, and conntrol statements.

<em>If you are new to each of them, be prepared to a) spend long hours to read through the API reference; b) good luck to find relevant code examples online; c) repeat a) and b) even you think you are good at programming with the language.</em>

## Main files

For Terraform, each module has its own dedicated directory. There is `main.tf` that exposes the main interface, while the supporting code are in `variables.tf`, `locals.tf`, and `data.tf`.

For Bazel, each package has its own dedicated directory. There is `BUILD` file that exposes the main interface, while the supporting data and functions are defined in other `.bzl` files.

Terraform module can refer to other module from a different directory. Bazel package can refer to other package from different directory.

## Extensibility

Terraform depends on the [Terraform providers][tf_provider] to interact with remote infra-structure. Bazel depends on external repositories to support build for different Programming language and framework. If you want to package your work as sharable code, you distribute it either in the form a Terraform provider or Bazel repository.

## Dependency

For Terraform, when your configuration code depends on many modules or providers, you can use `terraform graph` and `Graphviz` to visualize it, e.g. `terraform graph -type=plan | dot -Tpng -o graph.png`. For Bazel, you can use Bazel query and `Graphviz` to visualize the dependency, e.g. `bazel query 'deps(//foo)' --output graph | dot -Tpng > graph.png`. This kind of dependency debugging approach is quite similar.


[bazel]: https://docs.bazel.build/versions/4.2.2/bazel-overview.html "Bazel version 4"
[terraform]: https://www.terraform.io/intro "Terraform 0.14"
[tf_module]: https://www.terraform.io/language/modules "Terraform module"
[bazel_repository]: https://docs.bazel.build/versions/main/build-ref.html#workspace "Bazel workspace"
[tf_state]: https://www.terraform.io/language/state/purpose#performance "Terraform state"
[bazel_remote]: https://docs.bazel.build/versions/main/remote-caching.html#remote-caching "Bazel remote cache"
[hcl]: https://www.terraform.io/language/syntax/configuration "Terraform language"
[starlark]: https://github.com/bazelbuild/starlark "Starlark language"
[tf_provider]: https://www.terraform.io/language/providers#providers "Terraform provider"
