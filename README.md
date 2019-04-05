## Blog

My personal blog.

### Local development

The blog is built using Jekyll, so you'll need Ruby.

1. Install the dependencies.

```sh
gem install github-pages
```

2. Build. You can either export Jekyll so it's available on your path or just run the executable from its location. If you don't know where it is just run

```sh
-> gem environment
RubyGems Environment:
  - RUBYGEMS VERSION: 3.0.3
  - RUBY VERSION: 2.6.2 (2019-03-13 patchlevel 47) [x86_64-darwin18]
  - INSTALLATION DIRECTORY: /usr/local/lib/ruby/gems/2.6.0
  - USER INSTALLATION DIRECTORY: /Users/aurelioogliari/.gem/ruby/2.6.0
  - RUBY EXECUTABLE: /usr/local/opt/ruby/bin/ruby
  - GIT EXECUTABLE: /usr/bin/git
  - EXECUTABLE DIRECTORY: /usr/local/lib/ruby/gems/2.6.0/bin
  - SPEC CACHE DIRECTORY: /Users/aurelioogliari/.gem/specs
  - SYSTEM CONFIGURATION DIRECTORY: /usr/local/Cellar/ruby/2.6.2/etc
  - RUBYGEMS PLATFORMS:
    - ruby
    - x86_64-darwin-18
  - GEM PATHS:
     - /usr/local/lib/ruby/gems/2.6.0
     - /Users/aurelioogliari/.gem/ruby/2.6.0
     - /usr/local/Cellar/ruby/2.6.2/lib/ruby/gems/2.6.0
  - GEM CONFIGURATION:
     - :update_sources => true
     - :verbose => true
     - :backtrace => false
     - :bulk_threshold => 1000
  - REMOTE SOURCES:
     - https://rubygems.org/
  - SHELL PATH:
     - /usr/local/Cellar/rbenv/1.1.2/libexec
     - /usr/local/opt/openssl/bin
     - /usr/local/opt/python/libexec/bin
     - /usr/local/opt/ruby/bin
     - /usr/local/opt/ruby/bin
     - /usr/local/bin
     - /usr/bin
     - /bin
     - /usr/sbin
     - /sbin
```
 Look for Executable directory (for example `EXECUTABLE DIRECTORY: /usr/local/lib/ruby/gems/2.6.0/bin`) and then run Jekyll.

 ```sh
/usr/local/lib/ruby/gems/2.6.0/bin/jekyll serve
 ```

 3. Open http://127.0.0.1:4000 in your browser.

 Some more info can be found on the original readme of this [project](./OLD_README.md).

### Credits

This blog was started from [Jekyll-now](https://github.com/barryclark/jekyll-now).
