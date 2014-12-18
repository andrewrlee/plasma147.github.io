---
layout: post
title: How to find out which application is listening on a port.
category: Software
tags: [Ports, Windows, Linux]
date: 2012-05-16
---

<p>I keep having to look up how to work out what Application is listening on a certain port.</p>
<p>This is useful as it allows you to see if an applications is running on the expect port, and so configured correctly.</p>
<p>Posting it here so I don't have to keep looking it up!</p>

<p>
Linux:<br/>

 sudo netstat -tulpn | grep :<portnumber>

<br/>
Windows:<br/>

netstat -aon | findstr :<portnumber
<br/>
</p>