---
layout: post
title: "Vim Tip: Temporary Key Mappings"
categories: [vim]
tags: [vim]
---

One trick that I often use with Vim is temporarily mapping keys for tasks I need to perform in my current context.

For example, when working on a small Ruby script, I may want to run it a few times to check the output. In Vim, I can temporarily map ```<Leader>r``` to run the script.

{% highlight vim %}
:map ,r :!ruby myscript.rb<cr>
{% endhighlight %}

Here's another example. When working with Rails, I may only want to run unit tests on a specific model. In Vim, I can temporarily map ```<Leader>t``` to run only the tests I need.

{% highlight vim %}
:map ,t :!rake test TEST=test/unit/product_test<cr>
{% endhighlight %}

Using temporary key mappings has saved me many keystrokes. To find out more about this trick (and a lot of other helpful Vim-fu), I highly recommend watching [Peepcode's Play By Play with Gary Berhardt][1]. You can also refer to Vim’s documentation on the map command by entering ```:help map``` in Vim’s command line.

[1]: https://peepcode.com/products/play-by-play-bernhardt
