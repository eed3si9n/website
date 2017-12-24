scala-sbt.org
=============

This project is the source for scala-sbt.org. See [contributors](https://github.com/sbt/website/graphs/contributors) for the list of documentation contributors.

scala-sbt.org is powered by two static site engines.

[nanoc](http://nanoc.ws/) is used for the landing pages.

[Pamflet](http://www.foundweekends.org/pamflet/), a Scala-based documentation engine written by @n8han (and some contributions from @eed3si9n) generates 0.13+ documentations.

These two engines are driven by [sbt-site](https://github.com/sbt/sbt-site) and [sbt-ghpages](https://github.com/sbt/sbt-ghpages). We will shortly use pandoc to also generate pdf files.

## Attention plugin authors

The source for [Community plugins](http://www.scala-sbt.org/release/docs/Community-Plugins.html) page is at [src/reference/01-General-Info/02-Community-Plugins.md](https://github.com/sbt/website/edit/1.x/src/reference/01-General-Info/02-Community-Plugins.md).
Fork this project, add your plugin and send us a pull request if your plugin is not already on it.

## Developer notes

See [CONTRIBUTING.md](CONTRIBUTING.md).
