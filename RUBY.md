# Ruby Style Guide

These are standards and conventions we use for Ruby code at [ElectNext](https://electnext.com).
We've based it off of the awesome [AirBnB Styleguide][airbnb-ruby],
which was based on the excellent [Github Ruby Styleguide][github-ruby],
which in turn was inspired by [Bozhidar Batsov's guide][bbatsov-ruby].

Be like Bruce Lee and take what is useful.


## <a name='TOC'>Table of Contents</a>
  1.  [Whitespace](#whitespace)
      1. [Inline](#inline)
      2. [Indentation](#indentation)
      3. [Newlines](#newlines)
  2.  [Line Length](#line-length)
  3.  [Commenting](#commenting)
  4.  [Method Definitions](#method-definitions)
  5.  [Conditional Expressions](#conditional-expressions)
      1. [Ternary Operator Usage](#ternary-operator-usage)
  6.  [Syntax](#syntax)
  7.  [Naming](#naming)
  8.  [Classes](#classes)
  9.  [Exceptions](#exceptions)
  10. [Collections](#collections)
  11. [Strings](#strings)
  12. [Hashes](#hashes)
  13. [Regular Expressions](#regular-expressions)
  14. [Percent Literals](#percent-literals)
  15. [Be Consistent](#be-consistent)
  16. [Object Oriented Design](#rails-conventions-and-object-oriented-design)
      1. [Instance methods](#instance-methods)
      2. [HTTP Requests](#http-requests)

## Whitespace

### Inline

* Never leave trailing whitespace.

* Use spaces around operators; after commas, colons, and semicolons; and around
  `{` and before `}`.

    ```
    sum = 1 + 2
    a, b = 1, 2
    1 > 2 ? true : false; puts 'Hi'
    [1, 2, 3].each { |e| puts e }
    ```

* No spaces after `(`, `[` or before `]`, `)`.

    ```
    some(arg).other
    [1, 2, 3].length
    ```

### Indentation

* Use soft-tabs with a two space-indent.

* Indent `when` as deep as `case`.

    ```
    case
    when song.name == 'Misty'
      puts 'Not again!'
    when song.duration > 120
      puts 'Too long!'
    when Time.now.hour > 21
      puts "It's too late"
    else
      song.play
    end

    kind = case year
           when 1850..1889 then 'Blues'
           when 1890..1909 then 'Ragtime'
           when 1910..1929 then 'New Orleans Jazz'
           when 1930..1939 then 'Swing'
           when 1940..1950 then 'Bebop'
           else 'Jazz'
           end
    ```

### Newlines

* Add a new line after `if` conditions span multiple lines to help
  differentiate between the conditions and the body.

    ```
    if @reservation_alteration.checkin == @reservation.start_date &&
       @reservation_alteration.checkout == (@reservation.start_date + @reservation.nights)

      redirect_to_alteration @reservation_alteration
    end
    ```

## Line Length

* Keep lines fewer than 100 characters. Long complex lines of code are hard to
  quickly understand.

    Here are examples from our codebase showing several techniques for breaking
    complex statements into multiple lines that are all < 100 characters.
    Notice techniques like:

    * liberal use of linebreaks inside unclosed ( { [
    * chaining methods, ending unfinished chains with a "."
    * composing long strings by putting strings next to each other, separated
      by a backslash-then-newline.
    * breaking long logical statements with linebreaks after operators like
      "&&" and "||"


    ```
    scope = Translation::Phrase.includes(:phrase_translations).
      joins(:phrase_screenshots).
      where(:phrase_screenshots => {
        :controller => controller_name,
        :action => JAROMIR_JAGR_SALUTE,
      })
    ```

    ```
    translation = FactoryGirl.create(
      :phrase_translation,
      :locale => :is,
      :phrase => phrase,
      :key => 'phone_number_not_revealed_time_zone',
      :value => 'Símanúmerið þitt verður ekki birt. Það er aðeins hægt að hringja á '\
                'milli 9:00 og 21:00 %{time_zone}.'
    )
    ```

    ```
    if @reservation_alteration.checkin == @reservation.start_date &&
       @reservation_alteration.checkout == (@reservation.start_date + @reservation.nights)

      redirect_to_alteration @reservation_alteration
    end
    ```

    ```
    <% if @presenter.guest_visa_russia? %>
      <%= icon_tile_for(I18n.t("email.reservation_confirmed_guest.visa.details_header",
                               :default => "Visa for foreign Travelers"),
                        :beveled_big_icon => "stamp" do %>
        <%= I18n.t("email.reservation_confirmed_guest.visa.russia.details_copy",
                   :default => "Foreign guests travelling to Russia may need to obtain a visa...") %>
      <% end %>
    <% end %>
    ```

    These code snippets are very much more readable than the alternative:

    ```
    scope = Translation::Phrase.includes(:phrase_translations).joins(:phrase_screenshots).where(:phrase_screenshots => { :controller => controller_name, :action => JAROMIR_JAGR_SALUTE })

    translation = FactoryGirl.create(:phrase_translation, :locale => :is, :phrase => phrase, :key => 'phone_number_not_revealed_time_zone', :value => 'Símanúmerið þitt verður ekki birt. Það er aðeins hægt að hringja á milli 9:00 og 21:00 %{time_zone}.')

    if @reservation_alteration.checkin == @reservation.start_date && @reservation_alteration.checkout == (@reservation.start_date + @reservation.nights)
      redirect_to_alteration @reservation_alteration
    end

    <% if @presenter.guest_visa_russia? %>
      <%= icon_tile_for(I18n.t("email.reservation_confirmed_guest.visa.details_header", :default => "Visa for foreign Travelers"), :beveled_big_icon => "stamp" do %>
        <%= I18n.t("email.reservation_confirmed_guest.visa.russia.details_copy", :default => "Foreign guests travelling to Russia may need to obtain a visa prior to...") %>
      <% end %>
    <% end %>
    ```

## Commenting

> Though a pain to write, comments are absolutely vital to keeping our code
> readable. The following rules describe what you should comment and where. But
> remember: while comments are very important, the best code is
> self-documenting. Giving sensible names to types and variables is much better
> than using obscure names that you must then explain through comments.

> When writing your comments, write for your audience: the next contributor who
> will need to understand your code. Be generous — the next one may be you!

&mdash;[Google C++ Style Guide][google-c++]

Portions of this section borrow heavily from the Google
[C++][google-c++-comments] and [Python][google-python-comments] style guides.


### Function comments

Every function declaration should have comments immediately preceding it that
describe what the function does and how to use it. These comments should be
descriptive ("Opens the file") rather than imperative ("Open the file"); the
comment describes the function, it does not tell the function what to do. In
general, these comments do not describe how the function performs its task.
Instead, that should be left to comments interspersed in the function's code.

Every function should mention what the inputs and outputs are, unless it meets
all of the following criteria:

* not externally visible
* very short
* obvious

You may use whatever format you wish. In Ruby, two popular function
documentation schemes are [TomDoc](http://tomdoc.org/) and
[YARD](http://rubydoc.info/docs/yard/file/docs/GettingStarted.md). You can also
just write things out concisely:

```
# Return the fallback locales for the_locale.
# If opts[:exclude_default] is set, the default locale, which is otherwise
# always the last one in the returned list, will be excluded.
#
# For example:
#   fallbacks_for(:"pt-BR")
#     => [:"pt-BR", :pt, :en]
#   fallbacks_for(:"pt-BR", :exclude_default => true)
#     => [:"pt-BR", :pt]
def fallbacks_for(the_locale, opts = {})
  ...
end
```

### Block and inline comments

The final place to have comments is in tricky parts of the code. If you're
going to have to explain it at the next code review, you should comment it now.
Complicated operations get a few lines of comments before the operations
commence. Non-obvious ones get comments at the end of the line.

```
def fallbacks_for(the_locale, opts = {})
  # dup() to produce an array that we can mutate.
  ret = @fallbacks[the_locale].dup

  # We make two assumptions here:
  # 1) There is only one default locale (that is, it has no less-specific
  #    children).
  # 1) The default locale is just a language. (Like :en, and not :"en-US".)
  if opts[:exclude_default] &&
      ret.last == self.default_locale &&
      ret.last != language_from_locale(the_locale)
    ret.pop
  end

  ret
end
```

On the other hand, never describe the code. Assume the person reading the code
knows the language (though not what you're trying to do) better than you do.

### Punctuation, Spelling and Grammar

Pay attention to punctuation, spelling, and grammar; it is easier to read
well-written comments than badly written ones.

Comments should be as readable as narrative text, with proper capitalization
and punctuation. In many cases, complete sentences are more readable than
sentence fragments. Shorter comments, such as comments at the end of a line of
code, can sometimes be less formal, but you should be consistent with your
style.

Although it can be frustrating to have a code reviewer point out that you are
using a comma when you should be using a semicolon, it is very important that
source code maintain a high level of clarity and readability. Proper
punctuation, spelling, and grammar help with that goal.

### TODO comments

Use TODO comments for code that is temporary, a short-term solution, or
good-enough but not perfect.

TODOs should include the string TODO in all caps, followed by the name, e-mail
address, or other identifier of the person who can best provide context about
the problem referenced by the TODO, in parentheses. A colon is optional. A
comment explaining what there is to do is required. The main purpose is to have
a consistent TODO format that can be searched to find the person who can
provide more details upon request. A TODO is not a commitment that the person
referenced will fix the problem. Thus when you create a TODO, it is almost
always your name that is given.

```
# TODO(JKB) Make this play nicely with country-specific locales.
# TODO(max): Fix this by mocking Trebuchet.
```

### Commented-out code

Never leave commented-out code in our codebase.


## Method Definitions

* Use `def` with parentheses when there are arguments. Omit the
  parentheses when the method doesn't accept any arguments.

     ```
     def some_method
       # body omitted
     end

     def some_method_with_arguments(arg1, arg2)
       # body omitted
     end
     ```

* Do not use default arguments. Use an options hash instead.

    ```
    # bad
    def obliterate(things, gently = true, except = [], at = Time.now)
      # implementation omitted
    end

    # good
    def obliterate(things, options = {})
      default_options = {
        :gently => true, # obliterate with soft-delete
        :except => [], # skip obliterating these things
        :at => Time.now, # don't obliterate them until later
      }
      options.reverse_merge!(default_options)

      # implementation omitted
    end
    ```

* Use spaces around the `=` operator when assigning default values:

    ```
    # bad
    def some_method(options={})
      # do something...
    end

    # good
    def some_method(options = {})
      # do something...
    end
    ```

    While several Ruby books suggest the first style, the second is much more
    prominent in practice (and arguably a bit more readable).


## Conditional Expressions

* Never use `then` for multi-line `if/unless`.

    ```
    # bad
    if some_condition then
      # body omitted
    end

    # good
    if some_condition
      # body omitted
    end
    ```

* The `and` and `or` keywords are banned. It's just not worth it. Always use `&&` and `||` instead.

* Modifier `if/unless` usage is okay when the body is simple, the
  condition is simple, and the whole thing fits on one line. Otherwise,
  avoid modifier `if/unless`.

    ```
    # Bad -- this doesn't fit on one line.
    add_trebuchet_experiments_on_page(request_opts[:trebuchet_experiments_on_page]) if request_opts[:trebuchet_experiments_on_page] && !request_opts[:trebuchet_experiments_on_page].empty?

    # okay
    if request_opts[:trebuchet_experiments_on_page] &&
         !request_opts[:trebuchet_experiments_on_page].empty?

      add_trebuchet_experiments_on_page(request_opts[:trebuchet_experiments_on_page])
    end

    # Bad -- this is complex and deserves multiple lines and a comment.
    parts[i] = part.to_i(INTEGER_BASE) if !part.nil? && [0, 2, 3].include?(i)

    # okay
    return if self.reconciled?
    ```

* Never use `unless` with `else`. Rewrite these with the positive case first.

    ```
    # bad
    unless success?
      puts 'failure'
    else
      puts 'success'
    end

    # good
    if success?
      puts 'success'
    else
      puts 'failure'
    end
    ```

* Avoid `unless` with multiple conditions.

    ```
      # bad
      unless foo? && bar?
        puts "baz"
      end

      # okay
      if !(foo? && bar?)
        puts "baz"
      end
    ```

* Don't use parentheses around the condition of an `if/unless/while`,
  unless the condition contains an assignment (see "Using the return
  value of `=`" below).

    ```
    # bad
    if (x > 10)
      # body omitted
    end

    # good
    if x > 10
      # body omitted
    end

    # ok
    if (x = self.next_value)
      # body omitted
    end
    ```

### Ternary Operator Usage

* Avoid the ternary operator (`?:`) except in cases where all expressions are
  extremely trivial. However, do use the ternary operator(`?:`) over
  `if/then/else/end` constructs for single line conditionals.

    ```
    # bad
    result = if some_condition then something else something_else end

    # good
    result = some_condition ? something : something_else
    ```

* Use one expression per branch in a ternary operator. This
  also means that ternary operators must not be nested. Prefer
  `if/else` constructs in these cases.

    ```
    # bad
    some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

    # good
    if some_condition
      nested_condition ? nested_something : nested_something_else
    else
      something_else
    end
    ```

* Avoid multi-line `?:` (the ternary operator), use `if/unless` instead.


## Syntax

* Never use `for`, unless you know exactly why. Most of the time iterators
  should be used instead. `for` is implemented in terms of `each` (so
  you're adding a level of indirection), but with a twist - `for`
  doesn't introduce a new scope (unlike `each`) and variables defined
  in its block will be visible outside it.

    ```
    arr = [1, 2, 3]

    # bad
    for elem in arr do
      puts elem
    end

    # good
    arr.each { |elem| puts elem }
    ```


* Prefer `{...}` over `do...end` for single-line blocks.  Avoid using
  `{...}` for multi-line blocks (multiline chaining is always
  ugly). Always use `do...end` for "control flow" and "method
  definitions" (e.g. in Rakefiles and certain DSLs).  Avoid `do...end`
  when chaining.

    ```
    names = ["Bozhidar", "Steve", "Sarah"]

    # good
    names.each { |name| puts name }

    # bad
    names.each do |name|
      puts name
    end

    # good
    names.select { |name| name.start_with?("S") }.map { |name| name.upcase }

    # bad
    names.select do |name|
      name.start_with?("S")
    end.map { |name| name.upcase }
    ```

    Some will argue that multiline chaining would look OK with the use of
    {...}, but they should ask themselves - it this code really readable and
    can't the blocks contents be extracted into nifty methods.

* Avoid `return` where not required.

    ```
    # bad
    def some_method(some_arr)
      return some_arr.size
    end

    # good
    def some_method(some_arr)
      some_arr.size
    end
    ```

* Using the return value of `=` (an assignment) is ok, but surround the
  assignment with parenthesis.

    ```
    # good - shows intented use of assignment
    if (v = array.grep(/foo/)) ...

    # bad
    if v = array.grep(/foo/) ...

    # also good - shows intended use of assignment and has correct precedence.
    if (v = self.next_value) == "hello" ...
    ```

* Use `||=` freely to initialize variables.

    ```
    # set name to Bozhidar, only if it's nil or false
    name ||= 'Bozhidar'
    ```

* Don't use `||=` to initialize boolean variables. (Consider what
  would happen if the current value happened to be `false`.)

    ```
    # bad - would set enabled to true even if it was false
    enabled ||= true

    # good
    enabled = true if enabled.nil?
    ```

* Avoid using Perl-style special variables (like `$0-9`, `$`,
  etc. ). They are quite cryptic and their use in anything but
  one-liner scripts is discouraged. Prefer long form versions such as
  `$PROGRAM_NAME`.

* Never put a space between a method name and the opening parenthesis.

    ```
    # bad
    f (3 + 2) + 1

    # good
    f(3 + 2) + 1
    ```

* If the first argument to a method begins with an open parenthesis,
  always use parentheses in the method invocation. For example, write
`f((3 + 2) + 1)`.

* Use `_` for unused block parameters.

    ```
    # bad
    result = hash.map { |k, v| v + 1 }

    # good
    result = hash.map { |_, v| v + 1 }
    ```

* When a method block takes only one argument, and the body consists solely of
  reading an attribute or calling one method with no arguments, use the `&:`
  shorthand.

    ```
    # bad
    bluths.map { |bluth| bluth.occupation }
    bluths.select { |bluth| bluth.blue_self? }

    # good
    bluths.map(&:occupation)
    bluths.select(&:blue_self?)
    ```

## Naming

* Use `snake_case` for methods and variables.

* Use `CamelCase` for classes and modules.  (Keep acronyms like HTTP,
  RFC, XML uppercase.)

* Use `SCREAMING_SNAKE_CASE` for other constants.

* The names of predicate methods (methods that return a boolean value)
  should end in a question mark. (i.e. `Array#empty?`).

* The names of potentially "dangerous" methods (i.e. methods that modify `self` or the
  arguments, `exit!`, etc.) should end with an exclamation mark. Bang methods
  should only exist if a non-bang method exists. ([More on this][ruby-naming-bang]).

* Name throwaway variables "_".
    ```
    payment, _ = Payment.complete_paypal_payment!(params[:token], native_currency, created_at)
    ```

## Classes

* Avoid the usage of class (`@@`) variables due to their "nasty" behavior
in inheritance.

    ```
    class Parent
      @@class_var = 'parent'

      def self.print_class_var
        puts @@class_var
      end
    end

    class Child < Parent
      @@class_var = 'child'
    end

    Parent.print_class_var # => will print "child"
    ```

    As you can see all the classes in a class hierarchy actually share one
    class variable. Class instance variables should usually be preferred
    over class variables.

* Use `def self.method` to define singleton methods. This makes the methods
  more resistant to refactoring changes.

    ```
    class TestClass
      # bad
      def TestClass.some_method
        # body omitted
      end

      # good
      def self.some_other_method
        # body omitted
      end
    ```
* Avoid `class << self` except when necessary, e.g. single accessors and aliased
  attributes.

    ```
    class TestClass
      # bad
      class << self
        def first_method
          # body omitted
        end

        def second_method_etc
          # body omitted
        end
      end

      # good
      class << self
        attr_accessor :per_page
        alias_method :nwo, :find_by_name_with_owner
      end

      def self.first_method
        # body omitted
      end

      def self.second_method_etc
        # body omitted
      end
    end
    ```

* Indent the `public`, `protected`, and `private` methods as much the
  method definitions they apply to. Leave one blank line above them.

    ```
    class SomeClass
      def public_method
        # ...
      end

      private
      def private_method
        # ...
      end
    end
    ```

## Exceptions

* Don't use exceptions for flow of control.

    ```
    # bad
    begin
      n / d
    rescue ZeroDivisionError
      puts "Cannot divide by 0!"
    end

    # good
    if d.zero?
      puts "Cannot divide by 0!"
    else
      n / d
    end
    ```

* Avoid rescuing the `Exception` class.

    ```
    # bad
    begin
      # an exception occurs here
    rescue Exception
      # exception handling
    end

    # good
    begin
      # an exception occurs here
    rescue StandardError
      # exception handling
    end

    # acceptable
    begin
      # an exception occurs here
    rescue
      # exception handling
    end
    ```

## Collections

* Use `Set` instead of `Array` when dealing with unique elements. `Set`
  implements a collection of unordered values with no duplicates. This
  is a hybrid of `Array`'s intuitive inter-operation facilities and
  `Hash`'s fast lookup.

* Use symbols instead of strings as hash keys.

    ```
    # bad
    hash = { 'one' => 1, 'two' => 2, 'three' => 3 }

    # good
    hash = { :one => 1, :two => 2, :three => 3 }
    ```

* Use multi-line hashes when it makes the code more readable, and use
  trailing commas to ensure that parameter changes don't cause
  extraneous diff lines when the logic has not otherwise changed.

    ```
    hash = {
      :protocol => 'https',
      :only_path => false,
      :controller => :users,
      :action => :set_password,
      :redirect => @redirect_url,
      :secret => @secret,
    }
    ```

## Strings

* Prefer string interpolation instead of string concatenation:

    ```
    # bad
    email_with_name = user.name + ' <' + user.email + '>'

    # good
    email_with_name = "#{user.name} <#{user.email}>"
    ```

    Furthermore, keep in mind Ruby 1.9-style interpolation. Let's say you have
    are composing cache keys like this:

    ```
    CACHE_KEY = '_store'

    cache.write(@user.id + CACHE_KEY)
    ```

    Prefer instead string interpolation instead of string concatentation:

    ```
    CACHE_KEY = '%d_store'

    cache.write(CACHE_KEY % @user.id)
    ```

* Avoid using `String#+` when you need to construct large data chunks.
  Instead, use `String#<<`. Concatenation mutates the string instance in-place
  and is always faster than `String#+`, which creates a bunch of new string objects.

    ```
    # good and also fast
    html = ''
    html << '<h1>Page title</h1>'

    paragraphs.each do |paragraph|
      html << "<p>#{paragraph}</p>"
    end
    ```

* Prefer single-quoted strings when you don't need string interpolation or special symbols such as `\t`, `\n`, `'`, etc.

    ```Ruby
    # bad
    name = "Bozhidar"

    # good
    name = 'Bozhidar'
    ```

## Hashes

Use hashrocket syntax for Hash literals instead of the JSON style introduced in 1.9.

There is a good [discussion of this on StackOverflow](http://stackoverflow.com/questions/10004158/is-hash-rocket-deprecated).

    ```Ruby
    #bad
    user = {
      login: "Defunkt",
      name: "Chris Wanstrath"
    }

    #bad
    user = {
      login: "Defunkt",
      name: "Chris Wanstrath",
      "followers-count" => 52390235
    }

    #good
    user = {
      :login => "Defunkt"
      :name => "Chris Wanstrath",
      "followers-count" => 52390235
    }
    ```


## Regular Expressions

* Avoid using $1-9 as it can be hard to track what they contain. Named groups
  can be used instead.

    ```
    # bad
    /(regexp)/ =~ string
    ...
    process $1

    # good
    /(?<meaningful_var>regexp)/ =~ string
    ...
    process meaningful_var
    ```

* Be careful with `^` and `$` as they match start/end of line, not string
  endings.  If you want to match the whole string use: `\A` and `\Z`.

    ```
    string = "some injection\nusername"
    string[/^username$/]   # matches
    string[/\Ausername\Z/] # don't match
    ```

* Use `x` modifier for complex regexps. This makes them more readable and you
  can add some useful comments. Just be careful as spaces are ignored.

    ```
    regexp = %r{
      start         # some text
      \s            # white space char
      (group)       # first group
      (?:alt1|alt2) # some alternation
      end
    }x
    ```

## Percent Literals

* Use `%w` freely.

    ```
    STATES = %w(draft open closed)
    ```

* Use `%()` for single-line strings which require both interpolation
  and embedded double-quotes. For multi-line strings, prefer heredocs.

    ```
    # bad (no interpolation needed)
    %(<div class="text">Some text</div>)
    # should be '<div class="text">Some text</div>'

    # bad (no double-quotes)
    %(This is #{quality} style)
    # should be "This is #{quality} style"

    # bad (multiple lines)
    %(<div>\n<span class="big">#{exclamation}</span>\n</div>)
    # should be a heredoc.

    # good (requires interpolation, has quotes, single line)
    %(<tr><td class="name">#{name}</td>)
    ```

* Use `%r` only for regular expressions matching *more than* one '/' character.

    ```
    # bad
    %r(\s+)

    # still bad
    %r(^/(.*)$)
    # should be /^\/(.*)$/

    # good
    %r(^/blog/2011/(.*)$)
    ```

## Be Consistent

If you're editing code, take a few minutes to look at the code around you and
determine its style. If they use spaces around all their arithmetic
operators, you should too. If their comments have little boxes of hash marks
around them, make your comments have little boxes of hash marks around them
too.

The point of having style guidelines is to have a common vocabulary of coding
so people can concentrate on what you're saying rather than on how you're
saying it. We present global style rules here so people know the vocabulary,
but local style is also important. If code you add to a file looks
drastically different from the existing code around it, it throws readers out
of their rhythm when they go to read it. Avoid this.

## Rails Conventions and Object Oriented Design

### Instance methods

Changes to an object should occur in instance methods, not in class methods, controller methods, etc.


    # bad
    profile_views = ProfileView.where(:article_id => article_id)

    profile_views.each do |pv|
      if pv.politician_id == politician_id
        pv.destroy
      elsif pv.seen_with.include?(politician_id)
        pv.seen_with.delete(politician_id)
        pv.save!
      end
    end

    # good
    profile_views = ProfileView.where(:article_id => params[:id])

    profile_views.each do |pv|
      pv.remove_politician(params[:politician_id])
    end


### HTTP Requests

HTTP Requests should always have handling for timeouts, client errors, and
server errors. If the requirements entail serving a client web page
immediately after the response, the timeout limit should be no longer than
3 seconds. How this is done depends on the library being used. Here is an
example using HTTParty:

  ```
  begin
    response = Timeout::timeout(3) { self.get(query_url) }
  rescue Timeout::Error => e
    Rails.logger.warn "#{e.message} for #{query_url}"
    # other handling as needed...
  end

  # HTTParty doesn't throw exceptions for client errors and server errors
  if (defined? response.code) && response.code >= 400
    Rails.logger.warn "#{response.code} for #{query_url}"
    # other handling as needed...
  end
  ```


[airbnb-ruby]: https://github.com/airbnb/ruby
[bbatsov-ruby]: https://github.com/bbatsov/ruby-style-guide
[github-ruby]: https://github.com/styleguide/ruby
[google-c++]: http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml
[google-c++-comments]: http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml#Comments
[google-python-comments]: http://google-styleguide.googlecode.com/svn/trunk/pyguide.html#Comments
[ruby-naming-bang]: http://dablog.rubypal.com/2007/8/15/bang-methods-or-danger-will-rubyist
