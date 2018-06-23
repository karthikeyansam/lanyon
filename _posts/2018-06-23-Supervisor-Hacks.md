---
layout: post
title: Supervisor Hacks
---

# Introduction

``Supervisor`` is a client system that allows its users to monitor and control number of process on Linux systems  

# Using supervisorctl with linux permissions but without root or sudo

In the ``/etc/supervisor/supervisor.conf`` please do the below changes make supervisor control by current user

## Part 1

> [unix_http_server]
> file=/var/tmp/supervisord.sock # note folder name change
> chmod=0770 # This will provide read and write access to the socket
> chown=<user>:<group> # change the group will provide access to the socket 

## Part 2

> [supervisorctl]
> serverurl=/var/tmp/supervisord.sock

Note: still works if ``chmod`` and ``chown`` were changed

# View Supervisor in web

Need to add a below section in ``/etc/supervisor/supervisor.conf`` and need to restart the service

> [inet_http_server]
> port = 127.0.0.1:9001
> username = user
> password = 123

Then Browsing the link http://127.0.0.1:9001/ with username ``user`` and password ``123`` as mentioned above will have access to web portal

## References

1. https://coffeeonthekeyboard.com/using-supervisorctl-with-linux-permissions-but-without-root-or-sudo-977/
2. http://supervisord.org/configuration.html#inet-http-server-section-values