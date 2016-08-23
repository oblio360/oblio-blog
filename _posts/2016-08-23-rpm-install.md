--- 
layout: post 
title: "RPM install"
---

Someone recently asked me: "What is the lifecycle of the rpm -i packagename.rpm command?", which is a fancy way of saying: "what happens when you install a package using rpm?".

The question reminded me that despite the fact that I've used the command often, I actually forgot many of the details behind the scenes. We take rpm for granted, just as we do for other tools such as sed, awk & co.

So I decided it might be fun to take a look around and see what actually happens when you run "rpm -i packagename.rpm". I downloaded CentOS 7, installed it in a VM and started digging.

As a forewarning to readers, this post won't go into the type of detail [some other people went to](https://github.com/alex/what-happens-when). I won't be chasing voltage level changes through the computer wires and I'll be focusing instead on the actual high level steps executed by rpm as is installs the package.

So, let's get to it.

In order to best understand what happens during a package installation I decided that I'll make a couple of RPM packages myself, so that I'd be able to control the package installation fully. I started slowly, by looking around for documentation and I as usual, [stackoverflow.com for the win](http://stackoverflow.com/questions/880227/what-is-the-minimum-i-have-to-do-to-create-an-rpm-file)!

Based on the basic example from there and from the excellent documentation on [rpm.org](http://www.rpm.org/max-rpm/index.html) I created two small RPM specs.

The source code for them can be found here: [RPM samples](https://github.com/costincaraivan/rpmsample).

I'll disect one of the rpmspec files:

    # Don't try fancy stuff like debuginfo, which is useless on binary-only
    # packages. Don't strip binary too
    # Be sure buildpolicy set to do nothing
    %define        __spec_install_post %{nil}
    %define          debug_package %{nil}
    %define        __os_install_post %{_dbpath}/brp-compress

A bit of boilerplate. As a note, everything starting with "%" defined a rpmspec section, so it's important.

    Summary: A test dependency rpm package
    Name: testdependency
    Version: 1.0
    Release: 1
    License: MIT
    Group: Development/Tools
    SOURCE0 : %{name}-%{version}.tar.gz
    URL: http://blog.oblio360.com/

Package metadata. Out of these, name, version and release are important as they are actually used as variables throughout the spec.

    BuildRoot: %{_tmppath}/%{name}-%{version}-%{release}-root

Kind of self explanatory: RPM build root.

    %description
     %{summary}

Again, kind of self explanatory: package description/summary.

    %prep
     # Empty section

The [rpm build preparation phase/script](http://www.rpm.org/max-rpm/s1-rpm-inside-scripts.html).

    %build
     # Empty section.

The actual rpm build phase/script. In this case it doesn't do anything since the package isn't fancy.

    %clean
     rm -rf %{buildroot}

Build clean up.

   %pre
    echo 'Hello from the pre-install script.'

Pre-install script. This script is run just before the actual installation.

   %install
    rm -rf %{buildroot}
    mkdir -p  %{buildroot}

    # in builddir
    cp -a * %{buildroot}

Actual installation: copies all the files to their destination.

    %post
     echo 'Hello from the post-install script.'

Post install script. Run immediately after the installation.

    %preun
     echo 'Hello from the preun-install script'

Pre uninstall script.

    %postun
      echo 'Hello from the postun-install script'

Post uninstall script.


    %files
    %defattr(-,root,root,-)
    %config(noreplace) %{_sysconfdir}/%{name}/%{name}.conf
    %{_bindir}/*

Files included in the installation. This section uses [two RPM files directives](http://www.rpm.org/max-rpm/s1-rpm-inside-files-list-directives.html):

* deffattr defines file attributes: permissions and ownership; we keep the original permissions and set the ownership to root:root
* config marks the conf files as config files so that they get special treatment from RPM when installed

    %changelog
     * Tue Aug 23 2016 Oblio Point <oblio@oblio360.com> 1.0-1
     - First Build

Human readable changelog.

The second spec looks much the same, except for the fact that it specificies this rpm as a dependency.

Now that we've gone through all this hard work to create two sample RPMs, let's see how the actuall installation looks like:

    sudo ./install.sh 
    Preparing packages...
    Hello from the pre-install script.
    testdependency-1.0-1.x86_64
    Hello from the post-install script.
    Hello from the pre-install script.
    testprogram-1.0-1.x86_64
    Hello from the post-install script.

So RPM:

* figured out the dependency order
* triggered the installation of these dependencies
* for each dependency:
  * ran the pre-install script
  * ran the installation
  * ran the post-installation script

To be honest, this was much more straightforward that I remembered it to be from the last time I dabbled in RPM building. Things do become more complex along the way, as you can see in this [wonderful article](http://www.ibm.com/developerworks/library/l-rpm2/):

1.    Run N's %pre script.
2.    Copy N's new files to the file system.
3.    Run N's %post script.
4.    Run all installation triggers (those marked %triggerin in other packages) set off by the installation of N.
5.    Execute all of N's installation triggers.
6.    Run all of n's uninstallation (%triggerun) triggers.
7.    Run all the uninstallation triggers (those found in other packages) set off by the uninstallation of n.
8.    Execute the %preun hook of n.
9.    Remove any files not overwritten by the install of N.
10.    Execute all the uninstallation triggers (%triggerpostun) found in n.

So there you have it, the whole shebang of running an RPM install!
