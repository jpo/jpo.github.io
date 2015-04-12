---
layout: post
title: "How to Create Terminal Progress Indicators in Ruby"
---

When you’re developing a terminal application, it’s usually helpful to indicate the progress of a long running task. Over the years, I’ve done this in a number of ways. Most of them involve printing some kind of status message, such as “Processing file1.csv…” or “Processing 10 of 1,000,000…” etc.

One technique that I’ve always liked, but never took time to learn, was displaying a dynamic progress indicator. Chances are you’re seen this yourself. It’s used in popular applications such as Git and Homebrew. The indicators come in various shapes and sizes. I’ve seen progress bars, percentages, spinners, and others.

## The Technique In Action

I’ve created a simple example below so that you can see this technique in action. When you run it, you will see a percentage that dynamically updates in-place as it gradually increases from 0 to 100 over a period of 10 seconds. To try it, open your terminal, start a new irb session, and enter the code below.

{% highlight ruby %}
# Print 1 to 100 percent in place in the console using "dynamic output"
# technique
1.upto(100) do |i|
  printf("\rPercentage: %d%", i)
  sleep(0.1)
end
puts
{% endhighlight %}

## How It Works

The above example probably looks very similar to code you’ve written in the past. What’s the secret? The key is the ```\r``` character in the first argument of the printf command. This character is known as a carriage return. According to Wikipedia, a [carriage return][1] “moves the position of the cursor to the first position on the same line.” This means that each time printf is called inside the loop, the previous line is overwritten - in-place - by a new line. If the carriage return were not included, the new output would be continuously appended to the previous output and the result would look like this: ```Percentage: 0%Percentage 1%...```

## More Examples

Below, I’ve included some additional examples that I’ve seen before in other applications. I’ve also combined them into a [gist on GitHub][2]. Feel free to use them or extend them as you see fit.

### Progress Bar

{% highlight ruby %}
# Prints a text-based progress bar representing 0 to 100 percent. Each "="
# sign represents 5 percent.
0.step(100, 5) do |i|
  printf("\rProgress: [%-20s]", "=" * (i/5))
  sleep(0.5)
end
puts
{% endhighlight %}

### Spinner

{% highlight ruby %}
# Prints a text-based "spinner" element while work occurs.
spinner = Enumerator.new do |e|
  loop do
    e.yield '|'
    e.yield '/'
    e.yield '-'
    e.yield '\\'
  end
end

1.upto(100) do |i|
  printf("\rSpinner: %s", spinner.next)
  sleep(0.1)
end
{% endhighlight %}

### Combination

{% highlight ruby %}
# Prints a combination of the progress bar, spinner, and percentage examples.
spinner = Enumerator.new do |e|
  loop do
    e.yield '|'
    e.yield '/'
    e.yield '-'
    e.yield '\\'
  end
end

1.upto(100) do |i|
  progress = "=" * (i/5) unless i < 5
  printf("\rCombined: [%-20s] %d%% %s", progress, i, spinner.next)
  sleep(0.1)
end
{% endhighlight %}


[1]: http://en.wikipedia.org/wiki/Carriage_return
[2]: https://gist.github.com/jpo/3212901
