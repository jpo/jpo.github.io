---
layout: post
title: "Vim Tip: How to Display Whitespace Characters"
---

One of the things that took me awhile to figure out when I started using Vim was how to display whitespace characters. Whitespace characters are characters that take up space in a document but are not visible to the user. Examples include spaces, tabs, and new-lines characters.

In this post, I’m going to explain how to configure Vim to display whitespace characters. I’ll also touch on problems you may run into and how to avoid them.

## Turning On Whitespace Characters

Vim refers to the mode that is used to display whitespace characters as list mode. By default, it’s turned off. To turn it on you can enter set list on the Vim command line or add it to your vimrc file. To turn it off again, you can use the ```set nolist``` command.

## Setting Which Whitespace Characters Are Displayed

List mode is configured to only show the end-of-line characters ($) by default. If you want to show more characters or configure how the characters are displayed you can set the ```listchars``` variable.

```listchars``` is a global variable whose value can be set to a comma-separated list of key/value pairs. The key represents the whitespace character and the value represents how it will be displayed. For example, the default value for ```listchars``` is ```eol:$```. This means that when list mode is on end-of-line characters will show up as $.

Options that you can use with ```listchars``` are below. They were taken from the Vim help documentation.

{% highlight text %}
eol - Character to show at the end of each line.
tab - Two characters to be used to show a tab.
trail - Character to show for trailing spaces.
extends - Character to show in the last column when the line continues beyond the right of the screen.
precedes - Character to show in the first column when the line continues beyond the left of the screen.
conceal - Character to show in place of concealed text, when conceallevel is set to 1.
nbsp - Character to show for a space.
{% endhighlight %}

Here is how I’ve set ```listchars``` in my configuration:

{% highlight vim %}
set listchars=eol:$,nbsp:_,tab:>-,trail:~,extends:>,precedes:<
{% endhighlight %}

## It’s Not Working!

If you’ve turned on list mode and can’t see whitespace characters, the issue may be with your colorscheme. List mode uses the ```NonText``` and ```SpecialKey``` highlighting groups to determine what color the whitespace characters will be when displayed. In some cases, these highlighting groups may be set to the same color as your background. In short, if the background is black and whitespace characters are black, you won’t see them! Check your colorscheme and make sure these groups are set to a distinct color.

## Further Reading

The Vim help documentation is an excellent resource if you know how to find what you’re looking for. You can find more information about ```list``` and a ```listchars``` by entering ```help list``` or ```help listchars``` on the Vim command line.
