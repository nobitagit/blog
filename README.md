## Blog

My personal blog.

### Local development

The blog is built using Jekyll, so you'll need Ruby.

1. Install the dependencies.

```sh
$ gem install github-pages
```

2. Build. You can either export Jekyll so it's available on your path or just run the executable from its location. If you don't know where it is just run

```sh
$ gem environment
```

Look for Executable directory (for example `EXECUTABLE DIRECTORY: /usr/local/lib/ruby/gems/2.6.0/bin`) and then run Jekyll.

```sh
$ /usr/local/lib/ruby/gems/2.6.0/bin/jekyll build
$ /usr/local/lib/ruby/gems/2.6.0/bin/jekyll serve
```

You can add `--incremental` to the last command to enable incremental builds.

3.  Open http://127.0.0.1:4000 in your browser.

Some more info can be found on the original readme of this [project](./ORIGINAL_README.md).

### Credits

This blog was started from [Jekyll-now](https://github.com/barryclark/jekyll-now).
