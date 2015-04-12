---
layout: post
title: "A Brief Introduction to Ruby's Kernel.fork"
---

Recently, a coworker asked if I could help with manipulating some large CSV files. Each file contained approximately 1.2 million records and was over 200 megabytes in size. I’ve never worked with such large CSV files before, so I had no idea how long they would take to process.

I decided to start by writing a Ruby script that would parse and manipulate only a subset of the data. I used [Benchmark][1] to figure out how long it would take the program to run. I then did some quick calculations and the result was that it would take roughly 25 minutes to process the entire data set.

My first attempt looked like this:

{% highlight ruby %}
require 'benchmark'
require 'csv'

# Read each record in sample.csv, manipulate it,
# and write it to sample.out.csv
bm = Benchmark.realtime do
  File.open('sample.out.csv', 'w+') do |output|
    CSV.foreach('sample.csv') do |input|
      # Do some manipulations...
      output.write(input.to_csv)
    end
  end
end

puts bm
{% endhighlight %}

Coincidentally, I had been reading about working with threads and processes in Ruby in the evenings. I thought that this would be a great opportunity to use some of my new knowledge.

## About Kernel.fork

According to [Ruby’s documentation][2], ```Kernel.fork``` “creates a subprocess. If a block is specified, the block is run in the subprocess…” The documentation also points out that it should be used with ```Process.wait``` or ```Process.detach``` to avoid accumulating zombie processes.

Here is an example:

{% highlight ruby %}
fork do
  # Do stuff in a subprocess
end

# Wait for the subprocess(es) to finish
Process.wait
{% endhighlight %}

## Reworking the Code

Integrating ```Kernel.fork``` into my existing solution was very simple. All I had to do was insert a strategically placed ```fork``` block into my code. The result being that I saved myself and my coworker several extra minutes of waiting.

Here’s what I ended up with:

{% highlight ruby %}
require 'benchmark'
require 'csv'

bm = Benchmark.realtime do
  ['file1.csv', 'file2.csv', 'file3.csv'].each do |input_file|
    fork do
      output_file = input_file + '.out'
      File.open(output_file, 'w+') do |output|
        CSV.foreach(input_file) do |input|
          # Do some manipulations...
          output.write(input.to_csv)
        end
      end
    end
  end
end

puts bm
{% endhighlight %}

## Bonus Tip

You can achieve the same result in [Bash using ampersands][3] (&). In Bash, the ampersand is a control operator used to fork processes. This means that if I were to modify the previous example to accept an input file and output file as arguments, I could then run the following command in Bash and achieve similar results:

{% highlight bash %}
ruby parse.rb in1.csv out1.csv & \
ruby parse.rb in2.csv out2.csv & \
ruby parse.rb in3.csv out3.csv &
{% endhighlight %}

[1]: http://ruby-doc.org/stdlib-1.9.3/libdoc/benchmark/rdoc/Benchmark.html
[2]: http://www.ruby-doc.org/core-1.9.3/Kernel.html#method-i-fork
[3]: http://hacktux.com/bash/ampersand
