---
layout: post
title: "Gmail logging with log4j 1.2.16"
date: 2010-11-26 16:50
comments: false
categories: 
---

Log4j includes an <a href="http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/net/SMTPAppender.html">SMTP</a> appender that can be used to send logging messages via email. As of log4j 1.2.16, this built-in appender has the ability to send messages via Gmail.

Here is an example log4j configuration that uses Gmail to send logged messages:
{% gist 3823221 %}

The SMTPUsername, SMTPPassword, Subject, To, and From properties should be replaced with values appropriate for your situation. One thing that tripped me up originally is that the SMTPPort should be set to 465, not 587 as I had originally done.
