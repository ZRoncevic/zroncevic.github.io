---
layout: post
title: Hello (blog) World!
categories: [general, setup, i3]
tags: [i3, arch, manjaro, java, spring]
---

Hello blog world. This is my first blog post and in it I would like to present to you my i3 window manager setup.

My story to i3 is a long one. So to make it short - I started with tmux and somebody mentioned i3 - to whch I thought they mentioned Intel's processor. Now when I tell somebody I run i3 they are asking me the same thing ;)  

This is how it looks:

![My i3wm setup]({{ site.url }}/assets/media/i3_setup.png)

For the bar I use i3blocks. My config is:

{% highlight ruby %}
command=/usr/lib/i3blocks/$BLOCK_NAME
separator_block_width=15
markup=none

#Number of pacman updates
[pacman]
label=
command=pacman -Qu | wc -l
interval=300
color=#73bbc4

# Disk usage
[disk]
label=
#instance=/mnt/data
interval=30
color=#f85818

# Memory usage
[memory]
label=
interval=30

#Screen brightnes
[screen_backlight]
label=
command=xbacklight | awk '{printf "%3.0f\n", $1}'
interval=1
color=#fff736

#Sound volume
[volume]
label=♪
instance=Master
interval=1
signal=10
color=#09a4d1
command=/usr/lib/i3blocks/volume 5 pulse

# Network interface monitoring
[iface]
color=#00FF00
interval=10
separator=false
color=#e91c95

#WiFi
[iface]
color=#00FF00
interval=10
separator=false
color=#e91c95

# CPU usage
[cpu_usage]
label=CPU
interval=10
min_width=CPU: 100.00%

#Load
[load_average]
label=
interval=5
color=#c5282f

# Battery indicator
[battery]
label=⚡
#instance=1
interval=30

# Date Time
[time]
command=date '+%Y-%m-%d %H:%M:%S'
interval=1

{% endhighlight %}


Enough for the first post, I have to leave something for the posts to come.