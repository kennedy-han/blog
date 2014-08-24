---
layout: post
title: "hidden file in picture(linux)"
description: "hidden file in picture"
category: linux
tags: [hidden file linux]
---
{% include JB/setup %}

###hidden some file in picture(JPEG...etc.)
use `cat` and `pipe`

example:

there is a folder includes: a.jpg b.txt c.txt

then use `zip` command:

<code>
zip hidden.zip b.txt c.txt
</code>

<code>
cat a.jpg hidden.zip > newPic.jpg
</code>

then you get a new JPEG file: `newPic.jpg` and it includes hidden.zip

###how to get hidden files?
first rename the file extension name: `mv newPic.jpg newPic.zip`

use `unzip` command: `unzip newPic.zip`
