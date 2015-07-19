require 'time'

# Should clear this up
desc "Generate a new post"
task :post do
  raise "Need a title" unless ENV.has_key?('TITLE')

  title = ENV['TITLE']

  # split + join works better than gsub if the title ends with ! or ?
  slug = "#{Date.today}-#{title.downcase.split(/[^\w]+/).join("-")}"

  file = File.join(File.dirname(__FILE__), "_posts", slug + ".md"
  )

  template = <<END
---
layout: post
title: "#{title}"
categories: 
tags: 
---

END

  File.open(file, "w") do |f|
    f << template
  end

  system ("#{ENV['EDITOR']} #{file}")

end
