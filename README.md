# Where am I?

This is repository for small landing page of ElasticSearch russian user
group, hosted by GitHub Pages.

We've started as a small user group in [Telegram](https://telegram.org)
and then something happened and we started to make meetups.

So here we store links to our resources, and, of course, everything but 
this doc is in russian.

Click [here](https://elasticsearch-ru.github.io) to get to the landing
page itself.

Please note that we are self-organized group, so Elastic company 
doesn't have any ties to us (but we're unbelievably grateful for all
the oss projects made by it).

## Building locally

Building locally is easy (at least, if you're familiar with ruby 
workflow).

```
bundle install
vendor/bundle/ruby/%ruby version%/bin/jekyll b
# or, if you've installed jekyll globally
jekyll b
```

Site will be generated in `_site` directory.

You can use docker-compose to set up web server on port 3000. 
