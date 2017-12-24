---
title: Download
---

Download
========

<div class="arc_mac">
<h2>Mac</h2>

<h3>Homebrew</h3>
<pre>
$ brew install sbt@1
</pre>

<h3>Macports (Third-party package)</h3>
<pre>
$ port install sbt
</pre>
</div>

<div class="arc_windows">
<h2>Windows</h2>

<a href="https://github.com/sbt/sbt/releases/download/v<%= @config[:windowsBuild] %>/sbt-<%= @config[:windowsBuild] %>.msi" class="btn" role="button">sbt-<%= @config[:windowsBuild] %>.msi</a>
</div>

<div class="arc_linux">
  <div class="distro_debian">
  	<h2>Linux (deb)</h2>
<pre>
echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2EE0EA64E40A89B84B2DF73499E82A75642AC823
sudo apt-get update
sudo apt-get install sbt
</pre>
  </div>

  <div class="distro_redhat">
  	<h2>Linux (rpm)</h2>
<pre>
curl https://bintray.com/sbt/rpm/rpm &gt; bintray-sbt-rpm.repo
sudo mv bintray-sbt-rpm.repo /etc/yum.repos.d/
sudo yum install sbt
</pre>
  </div>

  <div class="distro_gentoo">
  	<h2>Gentoo</h2>
  	See <a href="<%= @config[:sbtBinaryVersion] %>/docs/Installing-sbt-on-Linux.html#Gentoo">instruction for ebuilds</a> (third-party package).
  </div>
</div>

<div class="arc_all">
<h2>All platforms</h2>

<a href="https://github.com/sbt/sbt/releases/download/v<%= @config[:sbtVersion] %>/sbt-<%= @config[:sbtVersion] %>.zip" class="btn" role="button">sbt-<%= @config[:sbtVersion] %>.zip</a>

<a href="https://github.com/sbt/sbt/releases/download/v<%= @config[:sbtVersion] %>/sbt-<%= @config[:sbtVersion] %>.tgz" class="btn" role="button">sbt-<%= @config[:sbtVersion] %>.tgz</a>
</div>

<h3>Previous releases</h3>

<h4>1.x</h4>
<ul>

<li>
  sbt 1.0.3
  (<a href="https://github.com/sbt/sbt/releases/download/v1.0.3/sbt-1.0.3.zip">.zip</a>)
  (<a href="https://github.com/sbt/sbt/releases/download/v1.0.3/sbt-1.0.3.tgz">.tgz</a>)
  (<a href="https://github.com/sbt/sbt/releases/download/v1.0.3/sbt-1.0.3.msi">.msi</a>)
</li>

<li>
  sbt 1.0.2
  (<a href="https://github.com/sbt/sbt/releases/download/v1.0.2/sbt-1.0.2.zip">.zip</a>)
  (<a href="https://github.com/sbt/sbt/releases/download/v1.0.2/sbt-1.0.2.tgz">.tgz</a>)
  (<a href="https://github.com/sbt/sbt/releases/download/v1.0.2/sbt-1.0.2.msi">.msi</a>)
</li>

<li>
  sbt 1.0.1
  (<a href="https://github.com/sbt/sbt/releases/download/v1.0.1/sbt-1.0.1.zip">.zip</a>)
  (<a href="https://github.com/sbt/sbt/releases/download/v1.0.1/sbt-1.0.1.tgz">.tgz</a>)
  (<a href="https://github.com/sbt/sbt/releases/download/v1.0.1/sbt-1.0.1.msi">.msi</a>)
</li>

<li>
  sbt 1.0.0
  (<a href="https://github.com/sbt/sbt/releases/download/v1.0.0/sbt-1.0.0.zip">.zip</a>)
  (<a href="https://github.com/sbt/sbt/releases/download/v1.0.0/sbt-1.0.0.tgz">.tgz</a>)
  (<a href="https://github.com/sbt/sbt/releases/download/v1.0.0/sbt-1.0.0.msi">.msi</a>)
</li>

</ul>

<h4>0.13</h4>
<ul>
<li>
  sbt 0.13.16 
  (<a href="https://cocl.us/sbt-0.13.16.zip">.zip</a>)
  (<a href="https://cocl.us/sbt-0.13.16.tgz">.tgz</a>)
  (<a href="https://cocl.us/sbt-0.13.16.msi">.msi</a>)
</li>

<li>
  sbt 0.13.15
  (<a href="https://github.com/sbt/sbt/releases/download/v0.13.15/sbt-0.13.15.zip">.zip</a>)
  (<a href="https://github.com/sbt/sbt/releases/download/v0.13.15/sbt-0.13.15.tgz">.tgz</a>)
  (<a href="https://github.com/sbt/sbt/releases/download/v0.13.15/sbt-0.13.15.msi">.msi</a>)
</li>

</ul>



