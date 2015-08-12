require 'time'

# Should clear this up
desc "Generate a new post"
task :post do
  raise "Needs a title" unless ENV.has_key?('TITLE')
  raise "Needs a slug"  unless ENV.has_key?('SLUG')

  title = ENV['TITLE']
  slug  = "#{Date.today}-#{ENV['SLUG'].downcase.gsub(/[^\w]+/, '-')}"

  # split + join works better than gsub if the title ends with ! or ?
  # slug = "#{Date.today}-#{title.downcase.split(/[^\w]+/).join("-")}"

  file = File.join(File.dirname(__FILE__), "_posts", slug + ".md")

  template = <<END
---
layout: post
title: #{title}
categories:
  - 
tags:
  - 
---

Teaser

<!--more-->

END

  File.open(file, "w") do |f|
    f << template
  end

  system ("#{ENV['EDITOR']} #{file}")

end
