---
layout: post
title: "Testing website with capybara-webkit and test/unit"
description: ""
category: testing 
tags: [headless brwoser, testing]
---
{% include JB/setup %}
---

# Setup for headless browser tests with test/unit and capybara-webkit.

You need to install capybara and capybara-webkit as gem. Capybara-webkit requires two system libraries **qt4** and **xvfb**. To install execute:

  `apt-get install gt4-dev-tools xvfb`

as sudo. This works for Debian.

# Install the gems:

  `gem install capybara`<br>
  `gem install capybara-webkit`

To run the headless browser you have to execute:

  `Xvfb :1 -screen 0 1024x768x16 -nolisten inet6 &`<br>
  `export 'DISPLAY=localhost:1.0'`

you can ignore warnings like:

  *SELinux: Disabled on system, not enabling in X server<br>
  [dix] Could not init font path element /usr/share/fonts/X11/cyrillic, removing from list!<br>
  [dix] Could not init font path element /usr/share/fonts/X11/100dpi/:unscaled, removing from list!<br>
  [dix] Could not init font path element /usr/share/fonts/X11/75dpi/:unscaled, removing from list!<br>
  [dix] Could not init font path element /usr/share/fonts/X11/Type1, removing from list!<br>
  [dix] Could not init font path element /usr/share/fonts/X11/100dpi, removing from list!<br>
  [dix] Could not init font path element /usr/share/fonts/X11/75dpi, removing from list!<br>
  [dix] Could not init font path element /var/lib/defoma/x-ttcidfont-conf.d/dirs/TrueType, removing from list!<br>
  libudev: udev_monitor_enable_receiving: bind failed: Operation not permitted<br>
  config/udev: failed to bind the udev monitor<br>
  [config] failed to initialise udev<br>*


You can integrate this command to your test. For the following example `export 'DISPLAY=localhost:1.0'` is already in ~/.bashrc.

# Example test:

`

    require 'rubygems'
    require 'test/unit'
    require 'capybara/dsl'
    require 'capybara-webkit'
 
    Capybara.default_driver = :webkit
    Capybara.default_wait_time = 5
    Capybara.javascript_driver = :webkit
    Capybara.run_server = false

    class LazarWebTest < Test::Unit::TestCase
      include Capybara::DSL

      @@uri = "http://lazar.in-silico.ch"

      def test_00_start
        `Xvfb :1 -screen 0 1024x768x16 -nolisten inet6 &`
        sleep 2
      end
      
      def test_01_visit
        visit(@@uri)
        puts page.source
      end

      def test_99_kill
        Capybara.reset!
        `pidof Xvfb|xargs kill`
      end

    end
`
