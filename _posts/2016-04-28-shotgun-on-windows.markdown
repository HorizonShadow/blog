---
layout: post
title:  "Shotgun on Windows"
date:   2016-04-28 13:40:00 -0300
categories: windows ruby
description: How to get shotgun running on windows using the new linux subsystem
---
If you try to run `shotgun` in windows, you'll quickly find it explodes on start up with the error

    .../shotgun:148:in `trap': unsupported signal SIGQUIT (ArgumentError)
  
This fails because windows doesn't support `fork(2)`, which Shotgun relies on.
    
With an upcoming update to windows, developers are going to have access to an ubuntu based 
subsystem through the command line. Through this, you can use Shotgun on your windows system. I'm going
to assume you already have this installed.

# Installing ruby

You're going to run into some problems if you just `apt-get` ruby. To get around this, we're going to 
use RVM, which is nice to use anyway.

What we're going to do is open up bash, install a whole bunch of development libraries, add mpapis public key,
fail to install RVM, and then install rvm manually. Sound good? Good.

Type these into command prompt sequentially. I'll explain what they do below.

    bash
    sudo apt-get update
    sudo apt-get install build-essential zlib1g zlib1g-dev zlibc libxml2 libxml2-dev libxslt-dev
    gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
    \curl -sSL https://get.rvm.io | bash -s stable --ruby
    cd ~/.rvm/archives
    tar zxvf *.tgz
    cd *
    ./install
    source ~/.rvm/scripts/rvm
    rvm install ruby-2.3.0
    rvm use 2.3
    gem install shotgun
    
First, we open bash. 

Next, we update the package list and install a bunch of development packages. 

In order to install RVM, we add mpapis public key, and then we download (and try to install) rvm. This is going to fail, but just ignore that. Hop into the newly created `archives` folder of `.rvm`, and 
unpack that `.tgz` file to expose to installation script directly. Now run that installation script. There will be some errors and warnings here, they don't affect anything. 

Once that's installed, everything's hunky dory. I will note, I had to use `puma` as a web server. `Thin` wouldn't respond to any requests. 

If you want to install nokogiri (because everything uses nokogiri), use `gem install nokogiri -- --use-system-libraries.` Installing nokogiri
normally won't compile. It uses its own versions of some libraries that aren't compatible with windows bash. We installed the alternatives
when we installed the development libraries above, so we're telling nokogiri to install using them. 