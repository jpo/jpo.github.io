---
layout: post
title: "Parsing Bookmarks With Nokogiri"
---

I’ve been working on a feature for one of my side projects that requires parsing bookmarks files that have been exported from a web browser. Most browsers export bookmarks in the Netscape Bookmark File Format. This format is an HTML document where the bookmarks are organized according to a standard structure. Microsoft has some helpful documentation [MSDN][1].

While considering how I would implement the feature, I spent a few minutes searching for an existing Ruby gem that already provided the functionality I needed. But, I came up empty-handed. Knowing that Nokogiri was a popular choice among Rubyists for parsing HTML files, I decided to try to implement the feature with it instead.

[Nokogiri][2], for those of you who may be new to it, is “an HTML, XML, and SAX parser with the ability to search documents via XPath and CSS selectors” 1. It’s very fast and very powerful. The poject’s site has a few examples of how it can be used to [parse][3], [search][4], and [modify][5] HTML and XML documents.

If all you’re interested in is the URLs then parsing a Netscape Bookmark File is trivial. This can easily be done with Nokogiri in a few lines of code. Here’s an example:

{% highlight ruby %}
require 'nokogiri'
document = Nokogiri::HTML(File.open('bookmarks.html'))
document.css('dt > a').each { |a| puts a['href'] }
{% endhighlight %}

This example uses CSS selectors to find all of the anchor tags (or bookmarks) in the document and retuns the text value of each one.

However, in addition to the bookmarks, I was interested in the folders as well. The reason being that I wanted the ability to use the folder names to categorize the bookmarks. This complicated things a bit. Because the bookmarks may be deeply nested within several levels of folders, I would have to traverse the document recursively much like you would if you were traversing a file system.

My first attempt at this was to use Nokogiri’s [traverse][6] feature to recursively walk through all of the nodes in the document. The problem with this approach was that traverse descends in to each node of the document as it’s encountered. Since HTML elements containing folder name and folder items were “siblings”, it was very difficult to determine what folder each bookmark belonged to.

After some trial and error, I ended up using a combination of recursion and XPath selectors. This approach allowed me to identify all the bookmarks in the current level of nesting, process them, and then descend to the next level. It works great and I’m pleased with the results. Here’s what I ended up with:

{% highlight ruby %}
# bookmark_reader.rb
require 'nokogiri'

class Bookmark
  attr_accessor :title, :url, :path

  def initialize(path, title, url)
    @path  = path
    @title = title
    @url   = url
  end
end

class BookmarkReader
  include Enumerable

  def initialize(path)
    @path = path
  end

  def each
    doc  = Nokogiri::HTML(File.open(@path))
    node = doc.at_xpath('//html/body')
    traverse(node, '/') { |b| yield b }
  end

  private

  def traverse(node, path, &block)
    anchors = node.search('./dt//a')
    folder_names = node.search('./dt/h3')
    folder_items = node.search('./dl')

    anchors.each do |anchor|
      yield Bookmark.new(path, anchor.text, anchor['href'])
    end

    folder_items.size.times do |i|
      folder_name = folder_names[i]
      folder_item = folder_items[i]
      next_path   = folder_name.nil? ? path : [path, folder_name].join('/')
      traverse(folder_item, next_path, &block)
    end
  end
end
{% endhighlight %}

{% highlight ruby %}
# USAGE
reader = BookmarkReader.new('bookmarks.html')
reader.each { |b| puts b.title }
{% endhighlight %}

Parsing the Netscape Bookmark File with Nokogiri ended up being a fun exercise and more of a challenge than I expected. If you have a better solution, I would love to hear from you.

[1]: http://msdn.microsoft.com/en-us/library/aa753582(v=vs.85).aspx
[2]: http://nokogiri.org/
[3]: http://nokogiri.org/tutorials/parsing_an_html_xml_document.html
[4]: http://nokogiri.org/tutorials/searching_a_xml_html_document.html
[5]: http://nokogiri.org/tutorials/modifying_an_html_xml_document.html
[6]: http://nokogiri.rubyforge.org/nokogiri/Nokogiri/XML/Node.html#M000308
