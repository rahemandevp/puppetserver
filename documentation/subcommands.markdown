---
layout: default
title: "Puppet Server: Subcommands"
canonical: "/puppetserver/latest/subcommands.html"
---


We've provided several CLI commands to help with debugging and
exploring Puppet Server. Most of the commands are the same ones you would use
in a Ruby environment --- such as `gem`, `ruby`, and `irb` --- except they run
against Puppet Server's JRuby installation and gems instead of your system Ruby.

The following subcommands are provided:

* [gem](#gem)
* [ruby](#ruby)
* [irb](#irb)
* [foreground](#foreground)

The format for each subcommand is:

~~~sh
puppetserver <subcommand> [<args>]
~~~

When running from source, the format is:

~~~sh
lein <subcommand> -c /path/to/puppetserver.conf [--] [<args>]
~~~

Note that if you are running from source, you need to separate flag arguments (such as `--version` or `-e`) with `--`, as shown above. Otherwise, those arguments will be applied to Leiningen instead of to Puppet Server. This isn't necessary when running from
packages (i.e., `puppetserver <subcommand>`).

## gem

Installs and manages gems that are isolated from system Ruby and are accessible only to Puppet Server. This is a simple wrapper around the standard Ruby `gem`, so all of the
usual arguments and flags should work as expected.

Examples:

~~~sh
$ puppetserver gem install pry --no-ri --no-rdoc
~~~

~~~sh
$ lein gem -c /path/to/puppetserver.conf -- install pry --no-ri --no-rdoc
~~~

If needed, you also can use the `JAVA_ARGS_CLI` environment variable to pass
along custom arguments to the Java process that the `gem` command is run within.
 
Example:

~~~sh
$ JAVA_ARGS_CLI=-Xmx8g puppetserver gem install pry --no-ri --no-rdoc
~~~

If you prefer to have the `JAVA_ARGS_CLI` option persist for multiple command
executions, you could set the value in the `/etc/sysconfig/puppetserver` or
`/etc/default/puppetserver` file, depending upon your OS distribution:

~~~ini
JAVA_ARGS_CLI=-Xmx8g
~~~

With the value specified in the sysconfig or defaults file, subsequent commands
would use the `JAVA_ARGS_CLI` variable automatically:

~~~sh
$ puppetserver gem install pry --no-ri --no-rdoc
// Would run 'gem' with a maximum Java heap of 8g
~~~

For more information, see [Puppet Server and Gems](./gems.markdown).

## ruby

Runs code in Puppet Server's JRuby interpreter. This is a simple wrapper
around the standard Ruby `ruby`, so all of the usual arguments and flags should
work as expected.

Useful when experimenting with gems installed via `puppetserver gem` and the
Puppet and Puppet Server Ruby source code.

Examples:

~~~sh
$ puppetserver ruby -e "require 'puppet'; puts Puppet[:certname]"
~~~

~~~sh
$ lein ruby -c /path/to/puppetserver.conf -- -e "require 'puppet'; puts Puppet[:certname]"
~~~

If needed, you also can use the `JAVA_ARGS_CLI` environment variable to pass
along custom arguments to the Java process that the `ruby` command is run within.
 
Example:

~~~sh
$ JAVA_ARGS_CLI=-Xmx8g puppetserver ruby -e "require 'puppet'; puts Puppet[:certname]"
~~~

If you prefer to have the `JAVA_ARGS_CLI` option persist for multiple command
executions, you could set the value in the `/etc/sysconfig/puppetserver` or
`/etc/default/puppetserver` file, depending upon your OS distribution:

~~~ini
JAVA_ARGS_CLI=-Xmx8g
~~~

With the value specified in the sysconfig or defaults file, subsequent commands
would use the `JAVA_ARGS_CLI` variable automatically:

~~~sh
$ puppetserver ruby -e "require 'puppet'; puts Puppet[:certname]"
// Would run 'ruby' with a maximum Java heap of 8g
~~~

## irb

Starts an interactive REPL for the JRuby that Puppet Server uses. This is a simple wrapper
around the standard Ruby `irb`, so all of the usual arguments and flags should
work as expected.

Like the `ruby` subcommand, this is useful for experimenting in an interactive
environment with any installed gems (via `puppetserver gem`) and the
Puppet and Puppet Server Ruby source code.

Examples:

~~~ruby
$ puppetserver irb
irb(main):001:0> require 'puppet'
=> true
irb(main):002:0> puts Puppet[:certname]
centos6-64.localdomain
=> nil
~~~

~~~sh
$ lein irb -c /path/to/puppetserver.conf -- --version
irb 0.9.6(09/06/30)
~~~

If needed, you also can use the `JAVA_ARGS_CLI` environment variable to pass
along custom arguments to the Java process that the `irb` command is run within.
 
Example:

~~~sh
$ JAVA_ARGS_CLI=-Xmx8g puppetserver irb
~~~

If you prefer to have the `JAVA_ARGS_CLI` option persist for multiple command
executions, you could set the value in the `/etc/sysconfig/puppetserver` or
`/etc/default/puppetserver` file, depending upon your OS distribution:

~~~ini
JAVA_ARGS_CLI=-Xmx8g
~~~

With the value specified in the sysconfig or defaults file, subsequent commands
would use the `JAVA_ARGS_CLI` variable automatically:

~~~sh
$ puppetserver irb
// Would run 'irb' with a maximum Java heap of 8g
~~~

## foreground

Starts the Puppet Server, but doesn't background it; similar to starting the service
and then tailing the log.

Accepts an optional `--debug` argument to raise the logging level to DEBUG.

Examples:

~~~java
$ puppetserver foreground --debug
2014-10-25 18:04:22,158 DEBUG [main] [p.t.logging] Debug logging enabled
2014-10-25 18:04:22,160 DEBUG [main] [p.t.bootstrap] Loading bootstrap config from specified path: '/etc/puppetserver/bootstrap.cfg'
2014-10-25 18:04:26,097 INFO  [main] [p.s.j.jruby-puppet-service] Initializing the JRuby service
2014-10-25 18:04:26,101 INFO  [main] [p.t.s.w.jetty9-service] Initializing web server(s).
2014-10-25 18:04:26,149 DEBUG [clojure-agent-send-pool-0] [p.s.j.jruby-puppet-agents] Initializing JRubyPuppet instances with the following settings:
~~~
