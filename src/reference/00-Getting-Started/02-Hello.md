---
out: Hello.html
---

  [Basic-Def]: Basic-Def.html
  [Setup]: Setup.html

Hello, World
------------

This page assumes you've [installed sbt][Setup].

### Create a project directory with source code

A valid sbt project can be a directory containing a single source file.
Try creating a directory `hello` with a file `hw.scala`, containing the
following:

```scala
object Hi {
  def main(args: Array[String]) = println("Hi!")
}
```

Now from inside the `hello` directory, start sbt and type `run` at the sbt
interactive console. On Linux or OS X the commands might look like this:

```
\$ mkdir hello
\$ cd hello
\$ echo 'object Hi { def main(args: Array[String]) = println("Hi!") }' > hw.scala
\$ sbt
...
> run
...
Hi!
```

In this case, sbt works purely by convention. sbt will find the
following automatically:

-   Sources in the base directory
-   Sources in `src/main/scala` or `src/main/java`
-   Tests in `src/test/scala` or `src/test/java`
-   Data files in `src/main/resources` or `src/test/resources`
-   jars in `lib`

By default, sbt will build projects with the same version of Scala used
to run sbt itself.

You can run the project with `sbt run` or enter the [Scala
REPL](http://www.scala-lang.org/node/2097) with `sbt console`. Invoking `sbt console`
sets up your project's classpath so you can try out live Scala examples
based on your project's code.

### Build definition

Most projects will need some manual setup. Basic build settings go in a
file called `build.sbt`, located in the project's base directory.

For example, if your project is in the directory `hello`, in
`hello/build.sbt` you might write:

```scala
lazy val root = (project in file(".")).
  settings(
    name := "hello",
    version := "1.0",
    scalaVersion := "$example_scala_version$"
  )
```

In [.sbt build definition][Basic-Def] you'll learn more about how to write
a `build.sbt` file.

If you plan to package your project in a jar, you will want to set at
least the name and version in a `build.sbt`.

### Setting the sbt version

You can force a particular version of sbt by creating a file
`hello/project/build.properties`. In this file, write:

```
sbt.version=$app_version$
```

to force the use of sbt $app_version$. sbt is 99% source compatible from
release to release. Still, setting the sbt version in
`project/build.properties` avoids any potential confusion.
