---
layout: post
title: "Testing website with capybara-webkit and minitest"
description: ""
category: Development
tags: [Headless browser, Testing]
---
{% include JB/setup %}
---

# Setup for headless browser tests with minitest and capybara-webkit.

You need to install selenium-webdriver and capybara-webkit as gem. Capybara-webkit requires two system libraries **qt** and **xvfb**. To install execute:

  `apt-get install libqtwebkit-dev`<br>
  `apt-get install xvfb`

as sudo. This works for Debian Wheezy.

# Install the gems:

  `gem install selenium-webdriver`<br>
  `gem install capybara-webkit`

To run the headless browser you have to execute:

  `DISPLAY=localhost:1.0 xvfb-run ruby TEST.rb`<br>

or you integrate it to your test.

For the following example `export 'DISPLAY=localhost:1.0'` is already in ~/.bashrc.

# Example test:

`

    require 'rubygems'
    require 'minitest/autorun'
    require 'capybara/dsl'
    require 'capybara-webkit'
 
    Capybara.default_driver = :selenium
    Capybara.default_wait_time = 20
    Capybara.javascript_driver = :webkit
    Capybara.run_server = false
    Capybara.app_host = 'https://services.in-silico.ch'

    class LazarWebTest < MiniTest::Test
      include Capybara::DSL

      def test_00_start
        `Xvfb :1 -screen 0 1024x768x16 2>/dev/null &`
        sleep 2
      end
      
      def test_01_visit
        visit('/')
        puts page.source
      end

      def test_99_kill
        `pidof Xvfb|xargs kill`
      end

    end
`
