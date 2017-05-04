---
out: Scopes.html
---

  [Basic-Def]: Basic-Def.html
  [Task-Graph]: Task-Graph.html
  [Library-Dependencies]: Library-Dependencies.html
  [Multi-Project]: Multi-Project.html
  [Inspecting-Settings]: ../docs/Inspecting-Settings.html

Scopes
------

This page describes scopes. It assumes you've read and understood the
previous pages, [build definition][Basic-Def] and [task graph][Task-Graph].

### The whole story about keys

[Previously][Basic-Def] we pretended that a key like `name` corresponded
to one entry in sbt's map of key-value pairs. This was a simplification.

In truth, each key can have an associated value in more than one
context, called a "scope."

Some concrete examples:

- if you have multiple projects (also called subprojects) in your build definition, a key can
  have a different value in each project.
- the `compile` key may have a different value for your main sources and
  your test sources, if you want to compile them differently.
- the `packageOptions` key (which contains options for creating jar
  packages) may have different values when packaging class files
  (`packageBin`) or packaging source code (`packageSrc`).

*There is no single value for a given key `name`*, because the value may
differ according to scope.

However, there is a single value for a given *scoped* key.

If you think about sbt processing a list of settings to generate a
key-value map describing the project, as
[discussed earlier][Basic-Def], the keys in that key-value map are
*scoped* keys. Each setting defined in the build definition (for example
in `build.sbt`) applies to a scoped key as well.

Often the scope is implied or has a default, but if the defaults are
wrong, you'll need to mention the desired scope in `build.sbt`.

### Scope axes

A *scope axis* is a type, where each instance of the type can define its
own scope (that is, each instance can have its own unique values for
keys).

There are three scope axes:

- Subprojects
- Dependency configurations
- Tasks

#### Scoping by subproject axis

If you [put multiple projects in a single build][Multi-Project], each
project needs its own settings. That is, keys can be scoped according to
the project.

The project axis can also be set to "entire build", so a setting applies
to the entire build rather than a single project. Build-level settings
are often used as a fallback when a project doesn't define a
project-specific setting.

#### Scoping by dependency configuration axis

A *dependency configuration* defines a graph of library dependencies, potentially with its own
classpath, sources, generated packages, etc. The dependency configuration concept
comes from Ivy, which sbt uses for
managed dependencies [Library Dependencies][Library-Dependencies], and from
[MavenScopes](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#Dependency_Scope).

Some configurations you'll see in sbt:

- `Compile` which defines the main build (`src/main/scala`).
- `Test` which defines how to build tests (`src/test/scala`).
- `Runtime` which defines the classpath for the `run` task.

By default, all the keys associated with compiling, packaging, and
running are scoped to a dependency configuration and therefore may work differently
in each dependency configuration. The most obvious examples are the task keys
`compile`, `package`, and `run`; but all the keys which *affect* those keys
(such as `sourceDirectories` or `scalacOptions` or `fullClasspath`) are also
scoped to the configuration.

#### Scoping by task axis

Settings can affect how a task works. For example, the `packageSrc` task
is affected by the `packageOptions` setting.

To support this, a task key (such as `packageSrc`) can be a scope for
another key (such as `packageOptions`).

The various tasks that build a package (`packageSrc`, `packageBin`,
`packageDoc`) can share keys related to packaging, such as `artifactName`
and `packageOptions`. Those keys can have distinct values for each
packaging task.

### Global scope

Each scope axis can be filled in with an instance of the axis type (for
example the task axis can be filled in with a task), or the axis can be
filled in with the special value `Global`.

`Global` means what you would expect: the setting's value applies to all
instances of that axis. For example if the task axis is Global, then the
setting would apply to all tasks.

### Delegation

A scoped key may be undefined, if it has no value associated with it in
its scope.

For each scope, sbt has a fallback search path made up of other scopes.
Typically, if a key has no associated value in a more-specific scope,
sbt will try to get a value from a more general scope, such as the
`Global` scope or the entire-build scope.

This feature allows you to set a value once in a more general scope,
allowing multiple more-specific scopes to inherit the value.

You can see the fallback search path or "delegates" for a key using the
`inspect` command, as described below. Read on.

### Referring to scoped keys when running sbt

On the command line and in interactive mode, sbt displays (and parses)
scoped keys like this:

```
{<build-uri>}<project-id>/config:intask::key
```

- `{<build-uri>}<project-id>` identifies the project axis. The
  `<project-id>` part will be missing if the project axis has "entire build" scope.
- `config` identifies the configuration axis.
- `intask` identifies the task axis.
- `key` identifies the key being scoped.

`*` can appear for each axis, referring to the `Global` scope.

If you omit part of the scoped key, it will be inferred as follows:

- the current project will be used if you omit the project.
- a key-dependent configuration will be auto-detected if you omit the
  configuration or task.

For more details, see [Interacting with the Configuration System][Inspecting-Settings].

### Examples of scoped key notation

- `fullClasspath` specifies just a key, so the default scopes are used:
  current project, a key-dependent configuration, and global task
  scope.
- `test:fullClasspath` specifies the configuration, so this is
  `fullClasspath` in the `test` configuration, with defaults for the other
  two scope axes.
- `*:fullClasspath` specifies `Global` for the configuration, rather than
  the default configuration.
- `doc::fullClasspath` specifies the `fullClasspath` key scoped to the `doc`
  task, with the defaults for the project and configuration axes.
- `{file:/home/hp/checkout/hello/}default-aea33a/test:fullClasspath`
  specifies a project, `{file:/home/hp/checkout/hello/}default-aea33a`,
  where the project is identified with the build
  `{file:/home/hp/checkout/hello/}` and then a project id inside that
  build `default-aea33a`. Also specifies configuration `test`, but leaves
  the default task axis.
- `{file:/home/hp/checkout/hello/}/test:fullClasspath` sets the project
  axis to "entire build" where the build is
  `{file:/home/hp/checkout/hello/}`.
- `{.}/test:fullClasspath` sets the project axis to "entire build" where
  the build is `{.}`. `{.}` can be written `ThisBuild` in Scala code.
- `{file:/home/hp/checkout/hello/}/compile:doc::fullClasspath` sets all
  three scope axes.

### Inspecting scopes

In sbt shell, you can use the `inspect` command to understand
keys and their scopes. Try `inspect test:fullClasspath`:

```
\$ sbt
> inspect test:fullClasspath
[info] Task: scala.collection.Seq[sbt.Attributed[java.io.File]]
[info] Description:
[info]  The exported classpath, consisting of build products and unmanaged and managed, internal and external dependencies.
[info] Provided by:
[info]  {file:/home/hp/checkout/hello/}default-aea33a/test:fullClasspath
[info] Dependencies:
[info]  test:exportedProducts
[info]  test:dependencyClasspath
[info] Reverse dependencies:
[info]  test:runMain
[info]  test:run
[info]  test:testLoader
[info]  test:console
[info] Delegates:
[info]  test:fullClasspath
[info]  runtime:fullClasspath
[info]  compile:fullClasspath
[info]  *:fullClasspath
[info]  {.}/test:fullClasspath
[info]  {.}/runtime:fullClasspath
[info]  {.}/compile:fullClasspath
[info]  {.}/*:fullClasspath
[info]  */test:fullClasspath
[info]  */runtime:fullClasspath
[info]  */compile:fullClasspath
[info]  */*:fullClasspath
[info] Related:
[info]  compile:fullClasspath
[info]  compile:fullClasspath(for doc)
[info]  test:fullClasspath(for doc)
[info]  runtime:fullClasspath
```

On the first line, you can see this is a task (as opposed to a setting,
as explained in [.sbt build definition][Basic-Def]). The value
resulting from the task will have type
`scala.collection.Seq[sbt.Attributed[java.io.File]]`.

"Provided by" points you to the scoped key that defines the value, in
this case
`{file:/home/hp/checkout/hello/}default-aea33a/test:fullClasspath` (which
is the `fullClasspath` key scoped to the `test` configuration and the
`{file:/home/hp/checkout/hello/}default-aea33a` project).

"Dependencies" was discussed in detail in the [previous page][Task-Graph].

We'll discuss "Delegates" later in this page.

Try `inspect fullClasspath` (as opposed to the above example,
inspect `test:fullClasspath`) to get a sense of the difference. Because
the configuration is omitted, it is autodetected as `compile`.
`inspect compile:fullClasspath` should therefore look the same as
`inspect fullClasspath`.

Try `inspect *:fullClasspath` for another contrast. `fullClasspath` is not
defined in the `Global` configuration by default.

Again, for more details, see [Interacting with the Configuration System][Inspecting-Settings].

### Referring to scopes in a build definition

If you create a setting in `build.sbt` with a bare key, it will be scoped
to the current project, configuration `Global` and task `Global`:

```scala
lazy val root = (project in file("."))
  .settings(
    name := "hello"
  )
```

Run sbt and `inspect name` to see that it's provided by
`{file:/home/hp/checkout/hello/}default-aea33a/*:name`, that is, the
project is `{file:/home/hp/checkout/hello/}default-aea33a`, the
configuration is `*` (meaning global), and the task is not shown (which
also means global).

Keys have an overloaded method called `.in` that is used to set the scope.
The argument to `.in(...)` can be an instance of any of the scope axes. So for
example, though there's no real reason to do this, you could set the
`name` scoped to the `Compile` configuration:

```scala
name in Compile := "hello"
```

or you could set the name scoped to the `packageBin` task (pointless! just
an example):

```scala
name in packageBin := "hello"
```

or you could set the `name` with multiple scope axes, for example in the
`packageBin` task in the `Compile` configuration:

```scala
name in (Compile, packageBin) := "hello"
```

or you could use `Global` for all axes:

```scala
name in Global := "hello"
```

(`name in Global` implicitly converts the scope axis `Global` to a scope
with all axes set to `Global`; the task and configuration are already
`Global` by default, so here the effect is to make the project `Global`,
that is, define `*/*:name` rather than
`{file:/home/hp/checkout/hello/}default-aea33a/*:name`)

If you aren't used to Scala, a reminder: it's important to understand
that in and `:=` are just methods, not magic. Scala lets you write them in
a nicer way, but you could also use the Java style:

```scala
name.in(Compile).:=("hello")
```

There's no reason to use this ugly syntax, but it illustrates that these
are in fact methods.

### When to specify a scope

You need to specify the scope if the key in question is normally scoped.
For example, the `compile` task, by default, is scoped to `Compile` and `Test`
configurations, and does not exist outside of those scopes.

To change the value associated with the `compile` key, you need to write
`compile in Compile` or `compile in Test`. Using plain `compile` would define
a new compile task scoped to the current project, rather than overriding
the standard compile tasks which are scoped to a configuration.

If you get an error like *"Reference to undefined setting"*, often
you've failed to specify a scope, or you've specified the wrong scope.
The key you're using may be defined in some other scope. sbt will try to
suggest what you meant as part of the error message; look for "Did you
mean compile:compile?"

One way to think of it is that a name is only *part* of a key. In
reality, all keys consist of both a name, and a scope (where the scope
has three axes). The entire expression
`packageOptions in (Compile, packageBin)` is a key name, in other words.
Simply `packageOptions` is also a key name, but a different one (for keys
with no in, a scope is implicitly assumed: current project, global
config, global task).

#### Build-wide settings

An advanced technique for factoring out common settings
across subprojects is to define the settings scoped to `ThisBuild`.

If a key that is scoped to a particular subproject is not found,
sbt will look for it in `ThisBuild` as a fallback.
Using the mechanism, we can define a build-wide default setting for
frequently used keys such as `version`, `scalaVersion`, and `organization`.

For convenience, there is `inThisBuild(...)` function that will
scope both the key and the body of the setting expression to `ThisBuild`.
Putting setting expressions in there would be equivalent to appending `in ThisBuild` where possible.

```scala
lazy val root = (project in file("."))
  .settings(
    inThisBuild(List(
      // Same as:
      // organization in ThisBuild := "com.example"
      organization := "com.example",
      scalaVersion := "$example_scala_version$",
      version      := "0.1.0-SNAPSHOT"
    )),
    name := "Hello",
    publish := (),
    publishLocal := ()
  )

lazy val core = (project in file("core"))
  .settings(
    // other settings
  )

lazy val util = (project in file("util"))
  .settings(
    // other settings
  )
```

#### Delegation in detail (.value lookup)

Now that we've covered all the details of scoping, we can explain the `.value`
lookup in detail. It's ok to skip this section at in the beginning.
To summarize what we've learned so far:

- A particular scope consists of three scope axes: Subprojects, Dependency configuration, and Tasks.
  You can think of axes like as digits in a number, or (r, g, b) components in a color.
- There's a special value `Global` scope (written as `*` in shell) that can be applied to either of the axes.
- There's a special value `ThisBuild` scope (written as `{.}` in shell) only for Subprojects axis.
- build.sbt can reference a scoped key using `.in(...)` method.

Now let's suppose we have a build definition as follows:

```scala
lazy val foo = settingKey[Int]("")
lazy val bar = settingKey[Int]("")

lazy val projA = (project in file("a"))
  .settings(
    foo := {
      (bar in Test).value + 1
    },
    bar in Compile := 1
  )
```

Inside of `foo`'s setting body the dependency a scoped key `(bar in Test)` is declared.
However, as you see, `bar in Test` is undefined in `projA`.
sbt is able to still resolve `(bar in Test)` to another scoped key,
and initialize `foo` as `2`.
This fallback search path is called *scope delegation*.

First rule is that the _precedence_ among the axes is:
Subprojects, Dependency configuration, and then Tasks.

- Rule 1A: If a scope has more specific Subproject axis, then it will always have higher precedence over other scopes without.
- Rule 1B: If a scope has more specific Dependency configuration, then it will have higher precedence over specific task scoping.

What is the expected `name in projB` value in the following build?

```scala
scalaVersion in (ThisBuild, packageBin) := "2.12.2"

lazy val projB = (project in file("b"))
  .settings(
    name := {
      "foo" + (scalaVersion in packageBin).value
    },
    scalaVersion := "2.11.11"
  )
```

The answer is `foo2.11.11`. Because of Rule 1A `scalaVersion` scoped to `projB` has
higher precendence over `(ThisBuild, packageBin)`.

Next, what would you see if you ran `projC/test`?

```scala
scalacOptions in ThisBuild += "-Ywarn-unused-import"

lazy val projC = (project in file("c"))
  .settings(
    test := {
      println((scalacOptions in (Compile, console)).value)
    },
    scalacOptions in console -= "-Ywarn-unused-import",
    scalacOptions in Compile := scalacOptions.value // added by sbt
  )
```

The answer is `List(-Ywarn-unused-import)`. Because of Rule 1B `scalacOptions in Compile`
has higher precedence than `scalacOptions in console`.

- Rule 2: For Dependency configuration axis, sbt searches in the order of the given value,
  its parents, their parents recursively, and then falls back to `Global`.

The example for that is `projA` that we saw earlier:

```scala
lazy val foo = settingKey[Int]("")
lazy val bar = settingKey[Int]("")

lazy val projA = (project in file("a"))
  .settings(
    foo := {
      (bar in Test).value + 1
    },
    bar in Compile := 1
  )
```

`(bar in Test)` is undefined, but due to Rule 2 it will look for `(bar in Runtime)` first,
which is `Test` configuration's parent, then `(bar in Compile)`, which is its parent.

- Rule 3: For Task axis, sbt searches for the given value, and then falls back to `Global`,
  which is non-task scoped version of the scope.

We can apply Rule 3 to think about the `(scalacOptions in (Compile, console))` example.
Since `(scalacOptions in (Compile, console))` was not there, Rule 3 says that one of
the delegates is `(scalacOptions in Compile)`.

These are well defined rules, but you might want to look up quickly what is going on.
This is where `inspect` can be used.

```
Hello> inspect projC/compile:console::scalacOptions
[info] Task: scala.collection.Seq[java.lang.String]
[info] Description:
[info]  Options for the Scala compiler.
[info] Provided by:
[info]  {file:/Users/xxxx/}projC/compile:scalacOptions
[info] Defined at:
[info]  /Users/xxxx/build.sbt:47
[info] Reverse dependencies:
[info]  projC/compile:console
[info]  projC/*:test
[info] Delegates:
[info]  projC/compile:console::scalacOptions
[info]  projC/compile:scalacOptions
[info]  projC/*:console::scalacOptions
[info]  projC/*:scalacOptions
[info]  {.}/compile:console::scalacOptions
[info]  {.}/compile:scalacOptions
[info]  {.}/*:console::scalacOptions
[info]  {.}/*:scalacOptions
[info]  */compile:console::scalacOptions
[info]  */compile:scalacOptions
[info]  */*:console::scalacOptions
[info]  */*:scalacOptions
....
```

See "Provided by". This shows that `projC/compile:console::scalacOptions` is
actually delegated to `projC/compile:scalacOptions`.
Next see the "Delegates". This is *all* of the possible delegate candidates
listed in the order of precedence.

- All the scopes with `projC` scoping on Subproject axis are listed first,
  then `ThisBuild` (`{.}`), and `Global` (`*`).
- Within a subproject, scopes with `Compile` scoping on Dependency configuration axis
  are listed first, then falls back to `Global` (`*`) configuration.
- Finally, Task axis scoping lists the given task value `console::` and the one without.

Note that scope delegation feels similar to class inheritance in an object-oriented language,
but there's a difference. In an OO language like Scala if there's a method named
`drawShape` on a trait `Shape`, its subclasses can override that even when `drawShape` is used
within the `Shape` trait, which is called dynamic dispatch.

In `build.sbt`, however, if you define a setting in terms of another setting at the build level,
a key scoped to project level might delegate to the build level, but it will *not* come back.
Here is an example.

```scala
lazy val root = (project in file("."))
  .settings(
    inThisBuild(List(
      organization := "com.example",
      scalaVersion := "2.12.2",
      version      := scalaVersion.value + "_0.1.0"
    )),
    name := "Hello"
  )

lazy val projD = (project in file("d"))
  .settings(
    scalaVersion := "2.11.11"
  )
```

What will `projD/version` return? The answer is `2.12.2_0.1.0`.
`projD/version` delegates to `version in ThisBuild`,
and it depends on `scalaVersion in ThisBuild`.
Because of this reason, build level setting should be limited mostly to simple value assignments.
