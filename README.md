Based on https://github.com/bbatsov/ruby-style-guide

## Table of Contents

* [Source Code Layout](#source-code-layout)
* [Syntax](#syntax)
* [Naming](#naming)
* [Comments](#comments)
    * [Comment Annotations](#comment-annotations)
* [Classes](#classes)
* [Exceptions](#exceptions)
* [Collections](#collections)
* [Strings](#strings)
* [Regular Expressions](#regular-expressions)
* [Percent Literals](#percent-literals)
* [Metaprogramming](#metaprogramming)
* [Misc](#misc)

## Source Code Layout

* Use `UTF-8` as the source file encoding.
* Use two **spaces** per indentation level. No hard tabs.
* Use spaces around operators, after commas, colons and semicolons, around `{`
  and before `}`. Whitespace might be (mostly) irrelevant to the Ruby
  interpreter, but its proper use is the key to writing easily
  readable code.

    ```Ruby
    sum = 1 + 2
    [1, 2, 3].each { |n| puts n }
    ```

    The only exception is when using the exponent operator:

    ```Ruby
    # Bad.
    e = M * c ** 2

    # Good.
    e = M * c**2
    ```

* No spaces after `(`, `[` or before `]`, `)`.

    ```Ruby
    some(arg).other
    [1, 2, 3].length
    ```

* Indent `when` as deep as `case`.

    ```Ruby
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

* Use empty lines between `def`s and to break up a method into logical
  paragraphs.

    ```Ruby
    def some_method
      data = initialize(options)

      data.manipulate!

      data.result
    end

    def some_method
      result
    end
    ```

* Indent parameters on newlines if they are too long to fit on one line.

    ```Ruby
    # Bad (too long).
    def send_mail(source)
      Mailer.deliver(to: 'bob@example.com', from: 'us@example.com', subject: 'Important message', body: source.text)
    end
    
    # Bad (doesn't separate each parameter in a method call with lots of parameters).
    def send_mail(source)
      Mailer.deliver(to: 'bob@example.com', from: 'us@example.com',
        subject: 'Important message', body: source.text)
    end

    # Good.
    def send_mail(source)
      Mailer.deliver(
        to: 'bob@example.com',
        from: 'us@example.com',
        subject: 'Important message',
        body: source.text)
    end

* Add underscores to large numeric literals to improve their readability.

    ```Ruby
    # Bad: how many 0s are there?
    num = 1000000

    # Good.
    num = 1_000_000
    ```

* Don't put an empty line between the comment block and the `def`.
* Limit lines to 80 characters.
* Avoid trailing whitespace.

## Syntax

* Use `def` with parentheses when there are arguments. Omit the
  parentheses when the method doesn't accept any arguments.

     ```Ruby
     def some_method
       # body omitted
     end

     def some_method_with_arguments(arg1, arg2)
       # body omitted
     end
     ```

* Never use `for`, unless you know exactly why. Most of the time iterators
  should be used instead. `for` is implemented in terms of `each` (so
  you're adding a level of indirection), but with a twist - `for`
  doesn't introduce a new scope (unlike `each`) and variables defined
  in its block will be visible outside it.

    ```Ruby
    arr = [1, 2, 3]

    # Bad.
    for elem in arr do
      puts elem
    end

    # Good.
    arr.each { |elem| puts elem }
    ```

* Never use `then` for multi-line `if/unless`.

    ```Ruby
    # Bad.
    if some_condition then
      # body omitted
    end

    # Good.
    if some_condition
      # body omitted
    end
    ```

* Favor the ternary operator(`?:`) over `if/then/else/end` constructs.
  It's more common and obviously more concise.

    ```Ruby
    # Bad.
    result = if some_condition then something else something_else end

    # Good.
    result = some_condition ? something : something_else
    ```

* Use one expression per branch in a ternary operator. This
  also means that ternary operators must not be nested. Prefer
  `if/else` constructs in these cases.

    ```Ruby
    # Bad.
    some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

    # Good.
    if some_condition
      nested_condition ? nested_something : nested_something_else
    else
      something_else
    end
    ```

* Never use `if x ...` or `when x ...`. Use
  the ternary operator instead.

    ```Ruby
    # Bad.
    result = if some_condition: something else something_else end

    # Good.
    result = some_condition ? something : something_else
    ```

* Use `&&/||` for boolean expressions, `and/or` for control flow.  (Rule
  of thumb: If you have to use outer parentheses, you are using the
  wrong operators.)

    ```Ruby
    # Boolean expression.
    if some_condition && some_other_condition
      do_something
    end

    # Control flow.
    document.saved? or document.save!
    ```

* Favor modifier `if/unless` usage when you have a simple, single-line
  body.

    ```Ruby
    # Bad.
    if some_condition
      do_something
    end

    # Good.
    do_something if some_condition

    # Bad. (complex expression)
    user.haves.available_only.first.pending! if update_have

    # Good.
    if update_have
      have = user.haves.available_only.first
      have.pending
    end
    ```

* Favor `unless` over `if` for negative conditions (or control
  flow `or`).

    ```Ruby
    # Bad.
    do_something if !some_condition

    # Good.
    do_something unless some_condition
    ```

* Never use `unless` with `else`. Rewrite these with the positive case first.

    ```Ruby
    # Bad.
    unless success?
      puts 'failure'
    else
      puts 'success'
    end

    # Good.
    if success?
      puts 'success'
    else
      puts 'failure'
    end
    ```

* Don't use parentheses around the condition of an `if/unless/while`.

    ```Ruby
    # Bad.
    if (x > 10)
      # body omitted
    end

    # Good.
    if x > 10
      # body omitted
    end
    ```

* Favor modifier `while/until` usage when you have a single-line
  body.

    ```Ruby
    # Bad.
    while some_condition
      do_something
    end

    # Good.
    do_something while some_condition
    ```

* Favor `until` over `while` for negative conditions.

    ```Ruby
    # Bad.
    do_something while !some_condition

    # Good.
    do_something until some_condition
    ```

* Omit parentheses around parameters for methods that are part of an
  internal DSL (e.g. Rake, Rails, RSpec), methods that have
  "keyword" status in Ruby (e.g. `attr_reader`, `puts`) and attribute
  access methods. Use parentheses around the arguments of all other
  method invocations.

    ```Ruby
    class Person
      attr_reader :name, :age
    end

    temperance = Person.new('Temperance', 30)
    temperance.name

    puts temperance.age

    x = Math.sin(y)
    array.delete(e)
    ```

* Prefer `{...}` over `do...end` for single-line blocks.  Avoid using
  `{...}` for multi-line blocks (multiline chaining is always
  ugly). Always use `do...end` for "control flow" and "method
  definitions" (e.g. in Rakefiles and certain DSLs).  Avoid same-line chaining.

    ```Ruby
    names = ['Bozhidar', 'Steve', 'Sarah']

    # Good.
    names.each { |name| puts name }

    # Bad.
    names.each do |name|
      puts name
    end

    # Bad: same-line chain.
    names.select { |name| name.start_with?('S') }.map { |name| name.upcase }

    # Bad: combining do/end and {}.
    names.select do |name|
      name.start_with?('S')
    end.map { |name| name.upcase }
    
    # Good.
    names.select { |name| name.start_with('S')
      .map { |name| name.upcase }
    ```

* Avoid `return` where not required for flow of control.

    ```Ruby
    # Bad.
    def some_method(some_arr)
      return some_arr.size
    end

    # Good.
    def some_method(some_arr)
      some_arr.size
    end
    ```

* Avoid `self` where not required. (It is only required when calling a self write accessor.)

    ```Ruby
    # Bad.
    def ready?
      if self.last_reviewed_at > self.last_updated_at
        self.worker.update(self.content, self.options)
        self.status = :in_progress
      end
      self.status == :verified
    end

    # Good.
    def ready?
      if last_reviewed_at > last_updated_at
        worker.update(content, options)
        self.status = :in_progress
      end
      status == :verified
    end
    ```

* As a corollary, avoid shadowing methods with local variables unless they are both equivalent.

    ```Ruby
    class Foo
      attr_accessor :options

      # Okay: both options and self.options are equivalent here.
      def initialize(options)
        self.options = options
        
      end

      # Bad.
      def do_something(options = {})
        unless options[:when] == :later
          output(self.options[:message])
        end
      end

      # Good.
      def do_something(params = {})
        unless params[:when] == :later
          output(options[:message])
        end
      end
    end
    ```

* Use spaces around the `=` operator when assigning default values to method parameters:

    ```Ruby
    # Bad.
    def some_method(arg1=:default, arg2=nil, arg3=[])
      # do something...
    end

    # Good.
    def some_method(arg1 = :default, arg2 = nil, arg3 = [])
      # do something...
    end
    ```

* Avoid line continuation (\\) where not required. In practice, avoid using
  line continuations at all.

    ```Ruby
    # Bad.
    result = 1 - \
             2

    # Good, but still ugly.
    result = 1 \
             - 2
    ```

* Avoid using the return value of `=` (an assignment).

    ```Ruby
    # Bad: shows intended use of assignment.
    if (foo = array.grep(/foo/))
      ...
    end

    # Good.
    foo = array.grep(/foo/)
    if foo
      ...
    end
    ```

* Use `||=` freely to initialize variables.

    ```Ruby
    # Set name to Bozhidar, only if it's nil or false.
    name ||= 'Bozhidar'
    ```

* Don't use `||=` to initialize boolean variables. (Consider what
would happen if the current value happened to be `false`.)

    ```Ruby
    # Bad: would set enabled to true even if it was false.
    enabled ||= true

    # Good.
    enabled = true if enabled.nil?
    ```

* Never put a space between a method name and the opening parenthesis.

    ```Ruby
    # Bad.
    f (3 + 2) + 1

    # Good.
    f(3 + 2) + 1
    ```

* The new hash literal syntax is preferred in Ruby 1.9 when your hash keys are symbols.

    ```Ruby
    # Bad.
    hash = { :one => 1, :two => 2 }

    # Good.
    hash = { one: 1, two: 2 }
    ```

* The new lambda literal syntax is preferred in Ruby 1.9.

    ```Ruby
    # Bad.
    lambda = lambda { |a, b| a + b }
    lambda.call(1, 2)

    # Good.
    lambda = ->(a, b) { a + b }
    lambda.(1, 2)
    ```

* Use `_` for unused block parameters.

    ```Ruby
    # Bad.
    result = hash.map { |k, v| v + 1 }

    # Good.
    result = hash.map { |_, v| v + 1 }
    ```

## Naming

> The only real difficulties in programming are cache invalidation and
> naming things. <br/>
> -- Phil Karlton

* Use `snake_case` for methods and variables.
* Use `CamelCase` for classes and modules.  (Keep acronyms like HTTP,
  RFC, XML uppercase.)
* Use `SCREAMING_SNAKE_CASE` for other constants.
* The names of predicate methods (methods that return a boolean value)
  should end in a question mark.
  (i.e. `Array#empty?`).
* The names of potentially "dangerous" methods (i.e. methods that modify `self` or the
  arguments, `exit!` (doesn't run the finalizers like `exit` does), etc.) should end with an exclamation mark if
  there exists a safe version of that *dangerous* method.

    ```Ruby
    # Bad. There is not matching 'safe' method.
    class Person
      def update!
      end
    end

    # Good.
    class Person
      def update
      end
    end

    # Good.
    class Person
      def update!
      end

      def update
      end
    end
    ```

* Define the non-bang (safe) method in terms of the bang (dangerous)
  one if possible.

    ```Ruby
    class Array
      def flatten_once!
        res = []

        each do |e|
          [*e].each { |f| res << f }
        end

        replace(res)
      end

      def flatten_once
        dup.flatten_once!
      end
    end
    ```

* When using `reduce` with short blocks, name the arguments `|acc, e|`
  (accumulator, element).

* Prefer `map` over `collect`, `find` over `detect`, `select` over
  `find_all`, `reduce` over `inject` and `size` over `length`. This is
  not a hard requirement; if the use of the alias enhances
  readability, it's ok to use it.

* Use `flat_map` instead of `map` + `flatten`.

    ```Ruby
    # Bad.
    all_songs = users.map(&:songs).flatten.uniq

    # Good.
    all_songs = users.flat_map(&:songs).uniq
    ```

## Comments

* Write code that is as self-documenting as possible.
* When needed, comments should reflect the *intent* of your code.
* Avoid writing comments to explain bad code. Refactor the code to
  make it self-explanatory. (Do or do not - there is no try. --Yoda)
* Avoid superfluous comments.

    ```Ruby
    # Bad.
    counter += 1 # increments counter by one
    ```

* Keep existing comments up-to-date. An outdated is worse than no comment
at all.

* Comments capitalized and use punctuation. Use [one space](http://en.wikipedia.org/wiki/Sentence_spacing) after periods.

    ```Ruby
    # this isn't a good comment

    # This is a better comment.

* Write comments on the line above the code, not in-line.

    ```Ruby
    # Bad.
    item.update_attribute :price, price_with_shipping # Include shipping in item price.

    # Good.

    # Include shipping in item price.
    item.update_attribute :price, price_with_shipping

### Comment Annotations

* Annotations should usually be written on the line immediately above
  the relevant code.
* The annotation keyword is followed by a colon and a space, then a note
  describing the problem.
* If multiple lines are required to describe the problem, subsequent
  lines should be indented two spaces after the `#`.

    ```Ruby
    def bar
      # FIXME: This has crashed occasionally since v3.2.1. It may
      #   be related to the BarBazUtil upgrade.
      baz(:quux)
    end
    ```

* In cases where the problem is so obvious that any documentation would
  be redundant, annotations may be left at the end of the offending line
  with no note. This usage should be the exception and not the rule.

    ```Ruby
    def bar
      sleep 100 # OPTIMIZE
    end
    ```

* Use `TODO` to note missing features or functionality that should be
  added at a later date.
* Use `FIXME` to note broken code that needs to be fixed.
* Use `OPTIMIZE` to note slow or inefficient code that may cause
  performance problems.
* Use `HACK` to note code smells where questionable coding practices
  were used and should be refactored away.
* Use `REVIEW` to note anything that should be looked at to confirm it
  is working as intended. For example: `REVIEW: Are we sure this is how the
  client does X currently?`

## Classes

* When designing class hierarchies make sure that they conform to the
  [Liskov Substitution Principle](http://en.wikipedia.org/wiki/Liskov_substitution_principle).
* Try to make your classes as
  [SOLID](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design\))
  as possible.
* Always supply a proper `to_s` method for classes that represent
  domain objects.

    ```Ruby
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      def to_s
        "#@first_name #@last_name"
      end
    end
    ```

* Use the `attr` family of functions to define trivial accessors or
mutators.

    ```Ruby
    # Bad.
    class Person
      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      def first_name
        @first_name
      end

      def last_name
        @last_name
      end
    end

    # Good.
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end
    end
    ```
* Consider using `Struct.new`, which defines the trivial accessors,
constructor and comparison operators for you.

    ```Ruby
    # Good.
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end
    end

    # better
    class Person < Struct.new(:first_name, :last_name)
    end
    ````

* Consider adding factory methods to provide additional sensible ways
to create instances of a particular class.

    ```Ruby
    class Person
      def self.create(options_hash)
        # body omitted
      end
    end
    ```

* Prefer [duck-typing](http://en.wikipedia.org/wiki/Duck_typing) over inheritance.

    ```Ruby
    # Bad.
    class Animal
      # abstract method
      def speak
      end
    end

    # extend superclass
    class Duck < Animal
      def speak
        puts 'Quack! Quack'
      end
    end

    # extend superclass
    class Dog < Animal
      def speak
        puts 'Bau! Bau!'
      end
    end

    # Good.
    class Duck
      def speak
        puts 'Quack! Quack'
      end
    end

    class Dog
      def speak
        puts 'Bau! Bau!'
      end
    end
    ```

* Avoid the usage of class (`@@`) variables due to their "nasty" behavior
in inheritance.

    ```Ruby
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

* Assign proper visibility levels to methods (`private`, `protected`)
in accordance with their intended usage. Don't go off leaving
everything `public` (which is the default).

* Indent the `public`, `protected`, and `private` methods as much the
  method definitions they apply to. Leave one blank line above the
  visibility modifier
  and one blank line below in order to emphasize that it applies to all
  methods below it.

    ```Ruby
    class SomeClass
      def public_method
        # ...
      end

      private

      def private_method
        # ...
      end

      def another_private_method
        # ...
      end
    end
    ```

* Use `def self.method` to define singleton methods. This makes the code
  easier to refactor since the class name is not repeated.

    ```Ruby
    class TestClass
      # Bad.
      def TestClass.some_method
        # body omitted
      end

      # Good.
      def self.some_other_method
        # body omitted
      end

      # Also possible and convenient when you
      # have to define many singleton methods.
      class << self
        def first_method
          # body omitted
        end

        def second_method_etc
          # body omitted
        end
      end
    end
    ```

## Exceptions

* Signal exceptions using the `fail` method. Use `raise` only when
  catching an exception and re-raising it (because here you're not
  failing, but explicitly and purposefully raising an exception).

    ```Ruby
    begin
      fail 'Oops';
    rescue => error
      raise if error.message != 'Oops'
    end
    ```

* Never return from an `ensure` block. If you explicitly return from a
  method inside an `ensure` block, the return will take precedence over
  any exception being raised, and the method will return as if no
  exception had been raised at all. In effect, the exception will be
  silently thrown away.

    ```Ruby
    def foo
      begin
        fail
      ensure
        return 'very bad idea'
      end
    end
    ```

* Use *implicit begin blocks* where possible.

    ```Ruby
    # Bad.
    def foo
      begin
        # main logic goes here
      rescue
        # failure handling goes here
      end
    end

    # Good.
    def foo
      # main logic goes here
    rescue
      # failure handling goes here
    end
    ```

* Mitigate the proliferation of `begin` blocks by using
  *contingency methods* (a term coined by Avdi Grimm).

    ```Ruby
    # Bad.
    begin
      something_that_might_fail
    rescue IOError
      # handle IOError
    end

    begin
      something_else_that_might_fail
    rescue IOError
      # handle IOError
    end

    # Good.
    def with_io_error_handling
       yield
    rescue IOError
      # handle IOError
    end

    with_io_error_handling { something_that_might_fail }

    with_io_error_handling { something_else_that_might_fail }
    ```

* Don't suppress exceptions.

    ```Ruby
    # Bad.
    begin
      # an exception occurs here
    rescue SomeError
      # the rescue clause does absolutely nothing
    end

    # Bad.
    do_something rescue nil
    ```

* Avoid using `rescue` in its modifier form.

    ```Ruby
    # Bad. This catches all StandardError exceptions.
    do_something rescue nil
    ```


* Don't use exceptions for flow of control.

    ```Ruby
    # Bad.
    begin
      n / d
    rescue ZeroDivisionError
      puts 'Cannot divide by 0!'
    end

    # Good.
    if d.zero?
      puts 'Cannot divide by 0!'
    else
      n / d
    end
    ```

* Avoid rescuing the `Exception` class.  This will trap signals and calls to
  `exit`, requiring you to `kill -9` the process.

    ```Ruby
    # Bad.
    begin
      # calls to exit and kill signals will be caught (except kill -9)
      exit
    rescue Exception
      puts "you didn't really want to exit, right?"
      # exception handling
    end

    # Good.
    begin
      # a blind rescue rescues from StandardError, not Exception as many
      # programmers assume.
    rescue => e
      # exception handling
    end

    # Also good.
    begin
      # an exception occurs here

    rescue StandardError => e
      # exception handling
    end

    ```

* Put more specific exceptions higher up the rescue chain, otherwise
  they'll never be rescued from.

    ```Ruby
    # Bad.
    begin
      # some code
    rescue Exception => e
      # some handling
    rescue StandardError => e
      # some handling
    end

    # Good.
    begin
      # some code
    rescue StandardError => e
      # some handling
    rescue Exception => e
      # some handling
    end
    ```

* Release external resources obtained by your program in an ensure
block.

    ```Ruby
    f = File.open('testfile')
    begin
      # .. process
    rescue
      # .. handle error
    ensure
      f.close unless f.nil?
    end
    ```

* Favor the use of exceptions for the standard library over
introducing new exception classes.

## Collections

* Prefer literal array and hash creation notation (unless you need to
pass parameters to their constructors, that is).

    ```Ruby
    # Bad.
    arr = Array.new
    hash = Hash.new

    # Good.
    arr = []
    hash = {}
    ```

* Prefer `%w` to the literal array syntax when you need an array of
strings.

    ```Ruby
    # Bad.
    STATES = ['draft', 'open', 'closed']

    # Good
    STATES = %w(draft open closed)
    ```

* Avoid the creation of huge gaps in arrays.

    ```Ruby
    arr = []
    arr[100] = 1 # now you have an array with lots of nils
    ```

* Use `Set` instead of `Array` when dealing with unique elements. `Set`
  implements a collection of unordered values with no duplicates. This
  is a hybrid of `Array`'s intuitive inter-operation facilities and
  `Hash`'s fast lookup.
* Prefer symbols instead of strings as hash keys.

    ```Ruby
    # Bad.
    hash = { 'one' => 1, 'two' => 2, 'three' => 3 }

    # Good
    hash = { one: 1, two: 2, three: 3 }
    ```

* Avoid the use of mutable objects as hash keys.
* The new hash literal syntax is preferred in Ruby 1.9 when your hash keys are symbols.

    ```Ruby
    # Bad.
    hash = { :one => 1, :two => 2, :three => 3 }

    # Good.
    hash = { one: 1, two: 2, three: 3 }
    ```

* Rely on the fact that hashes in Ruby 1.9 are ordered.
* Never modify a collection while traversing it.

## Strings

* Prefer string interpolation instead of string concatenation:

    ```Ruby
    # Bad.
    email_with_name = user.name + ' <' + user.email + '>'

    # Good.
    email_with_name = "#{user.name} <#{user.email}>"
    ```

* Prefer single-quoted strings when you don't need string interpolation or
  special symbols such as `\t`, `\n`, `'`, etc.

    ```Ruby
    # Bad.
    name = "Bozhidar"

    # Good.
    name = 'Bozhidar'
    ```

* Avoid using `String#+` when you need to construct large data chunks.
  Instead, use `String#<<`. Concatenation mutates the string instance in-place
  and is always faster than `String#+`, which creates a bunch of new string objects.

    ```Ruby
    # Good and also fast.
    html = ''
    html << '<h1>Page title</h1>'

    paragraphs.each do |paragraph|
      html << "<p>#{paragraph}</p>"
    end
    ```

## Regular Expressions

> Some people, when confronted with a problem, think
> "I know, I'll use regular expressions." Now they have two problems.<br/>
> -- Jamie Zawinski

* Don't use regular expressions if you just need plain text search in string:
  `string['text']`
* For simple constructions you can use regexp directly through string index.

    ```Ruby
    match = string[/regexp/]             # get content of matched regexp
    first_group = string[/text(grp)/, 1] # get content of captured group
    string[/text (grp)/, 1] = 'replace'  # string => 'text replace'
    ```

* Use non-capturing groups when you don't use captured result of parentheses.

    ```Ruby
    # Bad.
    /(first|second)/

    # Good.
    /(?:first|second)/
    ```

* Avoid using $1-9 as it can be hard to track what they contain. Named groups
  can be used instead.

    ```Ruby
    # Bad.
    /(regexp)/ =~ string
    ...
    process $1

    # Good.
    /(?<meaningful_var>regexp)/ =~ string
    ...
    process meaningful_var
    ```

* Character classes have only a few special characters you should care about:
  `^`, `-`, `\`, `]`, so don't escape `.` or brackets in `[]`.

* Be careful with `^` and `$` as they match start/end of line, not string endings.
  If you want to match the whole string use: `\A` and `\z` (not to be
  confused with `\Z` which is the equivalent of `/\n?\z/`).

    ```Ruby
    string = "some injection\nusername"
    string[/^username$/]   # matches
    string[/\Ausername\z/] # don't match
    ```

* Use `x` modifier for complex regexps. This makes them more readable and you
  can add some useful comments. Just be careful as spaces are ignored.

    ```Ruby
    regexp = %r{
      start         # some text
      \s            # white space char
      (group)       # first group
      (?:alt1|alt2) # some alternation
      end
    }x
    ```

* For complex replacements `sub`/`gsub` can be used with block or hash.

## Percent Literals

* Use `%w` freely.

    ```Ruby
    STATES = %w(draft open closed)
    ```

* Use `%()` for single-line strings which require both interpolation
  and embedded double-quotes. For multi-line strings, prefer heredocs.

    ```Ruby
    # Bad. No interpolation needed.
    %(<div class="text">Some text</div>)
    # should be '<div class="text">Some text</div>'

    # Bad. No double-quotes.
    %(This is #{quality} style)
    # should be "This is #{quality} style"

    # Bad. Multiple lines.
    %(<div>\n<span class="big">#{exclamation}</span>\n</div>)
    # should be a heredoc.

    # Good (requires interpolation, has quotes, single line).
    %(<tr><td class="name">#{name}</td>)
    ```

* Use `%r` only for regular expressions matching *more than* one '/' character.

    ```Ruby
    # Bad.
    %r(\s+)

    # Still bad.
    %r(^/(.*)$)
    # should be /^\/(.*)$/

    # Good.
    %r(^/blog/2011/(.*)$)
    ```

* Avoid `%q`, `%Q`, `%x`, `%s`, and `%W`.

* Prefer `()` as delimiters for all `%` literals.

## Metaprogramming

* Avoid needless metaprogramming.

* Do not mess around in core classes when writing libraries.
  (Do not monkey-patch them.)

* The block form of `class_eval` is preferable to the string-interpolated form.
  - when you use the string-interpolated form, always supply `__FILE__` and `__LINE__`, so that your backtraces make sense:

    ```ruby
    class_eval 'def use_relative_model_naming?; true; end', __FILE__, __LINE__
    ```

  - `define_method` is preferable to `class_eval{ def ... }`

* When using `class_eval` (or other `eval`) with string interpolation, add a comment block showing its appearance if interpolated (a practice I learned from the Rails code):

    ```ruby
    # from activesupport/lib/active_support/core_ext/string/output_safety.rb
    UNSAFE_STRING_METHODS.each do |unsafe_method|
      if 'String'.respond_to?(unsafe_method)
        class_eval <<-EOT, __FILE__, __LINE__ + 1
          def #{unsafe_method}(*args, &block)       # def capitalize(*args, &block)
            to_str.#{unsafe_method}(*args, &block)  #   to_str.capitalize(*args, &block)
          end                                       # end

          def #{unsafe_method}!(*args)              # def capitalize!(*args)
            @dirty = true                           #   @dirty = true
            super                                   #   super
          end                                       # end
        EOT
      end
    end
    ```

* avoid using `method_missing` for metaprogramming. Backtraces become messy; the behavior is not listed in `#methods`; misspelled method calls might silently work (`nukes.launch_state = false`). Consider using delegation, proxy, or `define_method` instead.  If you must, use `method_missing`,
  - be sure to [also define `respond_to_missing?`](http://blog.marc-andre.ca/2010/11/methodmissing-politely.html)
  - only catch methods with a well-defined prefix, such as `find_by_*` -- make your code as assertive as possible.
  - call `super` at the end of your statement
  - delegate to assertive, non-magical methods:

    ```ruby
    # Bad.
    def method_missing?(meth, *args, &block)
      if /^find_by_(?<prop>.*)/ =~ meth
        # ... lots of code to do a find_by
      else
        super
      end
    end

    # Good.
    def method_missing?(meth, *args, &block)
      if /^find_by_(?<prop>.*)/ =~ meth
        find_by(prop, *args, &block)
      else
        super
      end
    end

    # best of all, though, would to define_method as each findable attribute is declared
    ```

## Misc

* Write `ruby -w` safe code.
* Avoid hashes as optional parameters. Does the method do too much?
* Avoid methods longer than 10 LOC (lines of code). Ideally, most methods will be shorter than
  5 LOC. Empty lines do not contribute to the relevant LOC.
* Avoid parameter lists longer than three or four parameters.
* If you really need "global" methods, add them to Kernel
  and make them private.
* Use class instance variables instead of global variables.

    ```Ruby
    # Bad.
    $foo_bar = 1

    # Good.
    class Foo
      class << self
        attr_accessor :bar
      end
    end

    Foo.bar = 1
    ```

* Avoid `alias` when `alias_method` will do.
* Use `OptionParser` for parsing complex command line options and
`ruby -s` for trivial command line options.
* Code in a functional way, avoiding mutation when that makes sense.
* Do not mutate arguments unless that is the purpose of the method.
* Avoid more than three levels of block nesting.
* Be consistent. In an ideal world, be consistent with these guidelines.
* Use common sense.

