---
title: List VSS writers in a object.
date: 2017-05-31 00:00:00 +0000
layout: post
author: Alex Innes
categories: []
---
This is a simple script that uses the CMD command that outputs the state of the VSS Writers. The script uses regex to split the output into logical sections and then outputs it as a object so it can be used in another script.
<!--more--> 
{% gist 5a0263f57bef8f00431dca923faa63d3 %}