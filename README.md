Build State poller for Jenkins
==============================

This is quite crappy code, but works.

Usage:
------

    cd ~
    build_state_notifier jenkins.example.org [--no-ssl]

Requirements:
-------------

* This assumes that /rssLatest is readable by anonymous. If not, you have to
  add your credentials in the file.
* growlnotify must exist, Growl must be installed.

