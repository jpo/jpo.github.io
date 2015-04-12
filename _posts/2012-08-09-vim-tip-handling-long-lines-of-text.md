---
layout: post
title: "Vim Tip: Handling Long Lines of Text"
---

When you’re using Vim to read log files or write blog posts, one problem that you may come up against is wrapping long lines of text. Vim, being the powerful editor that it is, can handle this in multiple ways. Today, I’d like to talk about two of those options. One changes the way that text is displayed in the buffer. The other changes the way the text is formatted in the file.

## Option 1: Wrap

If you are concerned with the way long lines are displayed in Vim, then the ```wrap``` setting is for you. It’s turned on by default. When a long line of text reaches the edge of the window, Vim will continue displaying the text on the next line in the same way that Microsoft Word or any other rich text editor would. I find this setting useful when reading log files. Below is an example of how to change it.

{% highlight vim %}
" Default. Turn word-wrapping on.  
set wrap

" Turn word-wrapping off.  
set nowrap
{% endhighlight %}

## Option 2: TextWidth

If you are concerned with the way long lines are formatted in the buffer, you can use the ```textwidth``` setting. It’s set to 0 by default. When set to a specific character limit, such as 80, Vim will force any text exceeding that limit onto a new line in the buffer. I find this setting useful when writing a long article, such as a blog post. Below is an example of how to change it.

{% highlight vim %}
" Start a new line at 80 characters.  
set textwidth=80

" Do not use textwidth
set textwidth=0
{% endhighlight %}

## Bonus: Reformatting All Long Lines In A File

What if you’re working with a file that has a lot of long lines and you’d like to reformat them all to a specific number of characters? This simplest way to do this is with the ```gq``` command. When used in conjunction with the ```textwidth``` setting, ```gq``` will reformat the selected lines to the desired length. Below is an example of how you can reformat all of the lines in a file.

{% highlight vim %}
" Set the textwidth
set textwidth=80

" Move the cursor to the top of the file
gg

" Select all text in the file
VG

" Reformat all of the lines to the length specified by textwidth
gq
{% endhighlight %}

## Find Out More

Vim provides many other settings that can be used in conjunction with ```wrap``` and ```textwidth```. To find out more, you can reference Vim’s help documentation by entering ```:help wrap``` or ```:help textwidth``` on Vim’s command line.
