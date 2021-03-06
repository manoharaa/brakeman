![Brakeman Logo](http://brakemanscanner.org/images/logo_medium.png)

[![Travis CI
Status](https://secure.travis-ci.org/presidentbeef/brakeman.png)](https://travis-ci.org/presidentbeef/brakeman)
[![Code
Climate](https://codeclimate.com/github/presidentbeef/brakeman.png)](https://codeclimate.com/github/presidentbeef/brakeman)

# Brakeman

Brakeman is a static analysis tool which checks Ruby on Rails applications for security vulnerabilities.

It works with Rails 2.x, 3.x, and 4.x.

There is also a [plugin available](http://brakemanscanner.org/docs/jenkins/) for Jenkins/Hudson.

For even more continuous testing, try the [Guard plugin](https://github.com/guard/guard-brakeman).

# Homepage/News

Website: http://brakemanscanner.org/

Twitter: http://twitter.com/brakeman

Mailing list: brakeman@librelist.com

# Installation

Using RubyGems:

    gem install brakeman

Using Bundler, add to development group in Gemfile:

    group :development do
      gem 'brakeman', :require => false
    end

From source:

    gem build brakeman.gemspec
    gem install brakeman*.gem

# Usage

    brakeman [app_path]

It is simplest to run Brakeman from the root directory of the Rails application. A path may also be supplied.

# Options

To specify an output file for the results:

    brakeman -o output_file

The output format is determined by the file extension or by using the `-f` option. Current options are: `text`, `html`, `tabs`, `json` and `csv`.

Multiple output files can be specified:

    brakeman -o output.html -o output.json

To suppress informational warnings and just output the report:

    brakeman -q

To see all kinds of debugging information:

    brakeman -d

Specific checks can be skipped, if desired. The name needs to be the correct case. For example, to skip looking for default routes (`DefaultRoutes`):

    brakeman -x DefaultRoutes

Multiple checks should be separated by a comma:

    brakeman -x DefaultRoutes,Redirect

To do the opposite and only run a certain set of tests:

    brakeman -t SQL,ValidationRegex

To indicate certain methods are "safe":

    brakeman -s benign_method,totally_safe

By default, brakeman will assume that unknown methods involving untrusted data are dangerous. For example, this would cause a warning (Rails 2):

    <%= some_method(:option => params[:input]) %>

To only raise warnings only when untrusted data is being directly used:

    brakeman -r

By default, each check will be run in a separate thread. To disable this behavior:

    brakeman -n

Normally Brakeman will parse `routes.rb` and attempt to infer which controller methods are used as actions. However, this is not perfect (especially for Rails 3). To ignore the automatically inferred routes and assume all methods are actions:

    brakeman -a

Note that this will be enabled automatically if Brakeman runs into an error while parsing the routes.

If Brakeman is running a bit slow, try

    brakeman --faster

This will disable some features, but will probably be much faster (currently it is the same as `--skip-libs --no-branching`). *WARNING*: This may cause Brakeman to miss some vulnerabilities.

By default, Brakeman will return 0 as an exit code unless something went very wrong. To return an error code when warnings were found:

    brakeman -z

To skip certain files that Brakeman may have trouble parsing, use:

    brakeman --skip-files file1,file2,etc

Brakeman will raise warnings on models that use `attr_protected`. To suppress these warnings:

    brakeman --ignore-protected

To compare results of a scan with a previous scan, use the JSON output option and then:

    brakeman --compare old_report.json

This will output JSON with two lists: one of fixed warnings and one of new warnings.

Brakeman will ignore warnings if configured to do so. By default, it looks for a configuration file in `config/brakeman.ignore`.
To create and manage this file, use:

    brakeman -I

# Warning information

See WARNING\_TYPES for more information on the warnings reported by this tool.

# Warning context

The HTML output format provides an excerpt from the original application source where a warning was triggered. Due to the processing done while looking for vulnerabilities, the source may not resemble the reported warning and reported line numbers may be slightly off. However, the context still provides a quick look into the code which raised the warning.

# Confidence levels

Brakeman assigns a confidence level to each warning. This provides a rough estimate of how certain the tool is that a given warning is actually a problem. Naturally, these ratings should not be taken as absolute truth.

There are three levels of confidence:

 + High - Either this is a simple warning (boolean value) or user input is very likely being used in unsafe ways.
 + Medium - This generally indicates an unsafe use of a variable, but the variable may or may not be user input.
 + Weak - Typically means user input was indirectly used in a potentially unsafe manner.

To only get warnings above a given confidence level:

    brakeman -w3

The `-w` switch takes a number from 1 to 3, with 1 being low (all warnings) and 3 being high (only highest confidence warnings).

# Configuration files

Brakeman options can stored and read from YAML files. To simplify the process of writing a configuration file, the `-C` option will output the currently set options.

Options passed in on the commandline have priority over configuration files.

The default config locations are `./config/brakeman.yml`, `~/.brakeman/config.yml`, and `/etc/brakeman/config.yml`

The `-c` option can be used to specify a configuration file to use.

# License see MIT-LICENSE
