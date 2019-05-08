---
layout: post
title: Ruby
---


# {{ page.title }}

# Summary

Ruby is an object-oriented programming language. A few things to know:

* Ruby is interpreted - you don't need a compiler
* Ruby is object-oriented, meaning it allows users to manipulate dat structures called objects.
  Everything in Ruby is an object
* Ruby files end in a `.rb` (e.g. `myrubyfile.rb`)

# Interactive Ruby (REPL)

You can run `irb` to get into an interactive ruby session using a unix environment

    $irb

This is the equivalent of typing `python`

## p and puts

`p` is a more detailed print (more suitable for debugging) because you can tell the difference
between a `1`, `"1"` and `"2\b1"`.

### puts

Basically, this is printing out the info you need

    put myobject
    puts caller  # generate a backtrace without raising an exception
    puts object.methods  # list all methods of an object

### print

Print an object (without a new line)

### p

`p myobject` is basically `prints myobject.inspect` followed by a newline.

## Debugging with IntelliJ RubyMine


# Installing gems

__Gems__ is the equivalent of python pip.

    gem install my_gem --user-install

## Comments

    # Single line comments

    =begin
        Multi line comments 
    =end

# Example Code

Here is a class called `Person` with a name and age

    class Person
      attr_accessor :name, :age
    
      def initialize(name, age)
        @name = name
        @age  = age.to_i


Let's break this down:

* `class` means we're defining a Class
* `attr_accessor` is the shortcut to `attr_reader` and `attr_writer` put together, meaning can read and write
* `$` means a variable that has global scope
* `@` means a variable that has instance scope (i.e. shared by all instances of this class)
* `@@` means a variable that has class scope
* `puts` means put on the screen
* `"Will".reverse` returns the reverse of the string Will

# Strings

Double quote or single quote (go double quote)

    puts "Add Them #{4 + 5} \n\n"
    # Outputs: Add Them 9

    puts 'Add Them #{4 + 5} \n\n'
    # Outputs: Add Them #{4 + 5} \n\n
    # So don't use single quotes on strings

Note: The `#{4 + 5}` is called __interpolation__

Multiline strings

    multiline_string = <<EOM
    This is a very long string
    that contains interpolation
    like #{4 + 5} \n\n
    EOM
    puts multiline_string

    # Outputs
    This is a very long string
    that contains interpolation
    like 9

## String Methods

    first_name = "William"
    last_name = "Liu
    full_name = first_name + last_name

    puts full_name.include?("Liu")
    puts full_name.size
    puts "Vowels : " + full_name.count("aeiou").to_s
    puts "Consonants: " + full_name.count("^aeiou").to_s  # looks for opposite
    puts full_name.start_with?("Will")
    puts full_name.length
    puts "Index: " + full_name.index("Liu")
    puts full_name.upcase
    puts full_name.downcase
    puts full_name.lstrip
    puts full_name.rstrip
    puts full_name.strip
    puts full_name.rjust(20, '.')  # makes string 20 spaces with `.` to fill in
    puts full_name.ljust(20, '.')  # makes string 20 spaces with `.` to fill in
    puts full_name.chop  # removes trailing newline characters
    puts full_name.delete("a")  # deletes every occurrence of 'a'
    name_array = full_name.split(//)  # split every new character into an array
    name_array = full_name.split(/ /)  # split every space into an array

# Symbols

__Symbols__ are like strings, except you can't change the value of a symbol and
you don't need a string method. Symbols are used for things like keys inside hashes because 
they're much faster.

    :will
    puts :will
    puts :will.to_s  # will
    puts :will.class  # Symbol
    puts :will.object_id  # 933628

Symbols are used a lot in Ruby, e.g.

    attr_accessor :name, :weight

# Special Characters

After a method, you might see a `?` (e.g. `include?`). You might 
also see a `!` (meaning this is an unusual version of the method).

    ? - check if true/false for object
    ! - used for methods that raise an exception when the method without does not
        e.g. `save` and `save!` in ActiveRecord

## ?

`?` are __predicate procedures__. `?` always returns a boolean value. Examples include:

    +2.6.2 :019 > name = "William Liu"
     => "William Liu"
    +2.6.2 :020 > name.include?("Liu")
     => true

## !

If `!` is before a variable, it's a negation. If `!` is after a method name, it's a mutation procedure.

`!` are __mutation procedures__, which store values into previously allocated locations.
I.e. Things are modified.

`!` indicates a __dangerous method__ as opposed to a __safe method__ (without bang).
The `!` indicates that the method will modify the object it's called on.

    foo = "A STRING"  # a string called foo
    foo.downcase!  # modifies foo itself
    puts foo    # prints the modified foo: 'a string'

For Ruby on Rails, the bang means:

* It's not necessary to save the object after calling the method
* When you call the method, the db is going to be changed

## 

# Boolean expressions

Boolean expressions evaluate to a truth value. By default, ruby values are truthy.

## And, &&

The `and` and `&&` operators evaluate their first operand. If false, then the expression returns false.
Otherwise, the expression returns the value of the second operand. `&&` has higher precedence than `and`.
That means if you have an assignment `=`, you probably want to use `&&`

## Or, ||

The `or` and `||` operators evaluate their first operand. If true, the expression returns true.
Otherwise, the expression returns the value of the second operand. `||` has higher precedence than `or`.
That means if you have an assignment `=`, you probably want to use `||`.

## Precedence

One thing to note about operators is that there is precedence. You can force precedence with `()`, otherwise
they will follow this order here: https://www.techotopia.com/index.php/Ruby_Operator_Precedence

This might get confusing because assignment (`=`) has higher precedence than a low boolean evaluation of
(`or`, `and`), but isn't as high as (`||`, `&&`).

For example:

    +2.6.2 :013 > a = false || true
     => true
    +2.6.2 :014 > a
     => true
    +2.6.2 :015 > a = false or true
     => true
    +2.6.2 :016 > a
     => false

# Arrays

    array_1 = Array.new  # prints nothing
    array_2 = Array.new(5) # prints nothing 5 times
    array_3 = Array.new(5, "empty")  # prints 'empty' 5 times
    array_4 = [1, "two", 3, 5.5]  # prints 1, two, 3, 5.5
    puts array_4[2]  # 3
    puts array_4[2,2].join(", ")  # 3, 5.5
    puts array_4.values_at(0,1,3).join(", ")  # 1, two, 5.5
    array_4.unshift(0)  # add a value (in this case: 0) to the beginning of the array
    array_4.shift() # remove first item in the array
    array_4.push(100,200)  # add two values to the end of the array
    array_4.pop # remove last element of the array
    array_4.concat([10,20,30])  # add 10,20,30 to the end of the array
    puts "Array Size: " + array_4.size().to_s
    puts "Array contains 100 : " + array_4.include?(100).to_s
    puts "How many 100s : " + array_4.count(100).to_s
    puts "Array Empty : " + array_4.empty?.to_s
    p array_4  # prints out entire array

    array_4.each do |value|  # loops through all values and temporarily places to temp 'value'
      puts value
    end

# Hashes

    number_hash = {"PI" => 3.14,
                   "Golden" => 1.618,
                   "e" => 2.718}

    puts number_hash["PI"]  # 3.14
    superheroes = Hash["Clark Kent", "Superman", 
                       "Bruce Wayne", "Batman"]
    puts superheroes["Clark Kent"]  # Superman
    superheroes["Barry Allen"] = "Flash"  # Assign a new key and value

    samp_hash = Hash.new("No Such Key")  # default key
    puts samp_hash["Dog"]  # No Such Key

    superheroines = Hash["Lisa Morel", "Aquagirl",
                         "Betty Kane", "Batgirl"]
    superheroes.update(superheroines)  # A destructive merge - eliminates duplicates, normally use this
    superheroes.merge(superheroines)  # Non destructive merge - does not eliminate duplicates

    superheroes.each do |key, value|
      puts key.to_s + " : " + value
    end
    # Clark Kent : Superman
    # Bruce Wayne : Batman
    # Barry Allen : Flash
    # Lisa Morel : Aquagirl
    # Betty Kane : Batgirl

    puts "Has Key Lisa Morel " + superheroes.has_key?("Lisa Morel")
    puts "Has Value Batman " + superheroes.has_value?("Batman")
    puts "Is Hash Empty " + superheroes.empty?.to_s
    puts "Size of Hash " + superheroes.size.to_s
    
    superheroes.delete("Barry Allen")  # deletes key

# Enumerable

    Class Menu
      include Enumerable

      def each
        yield "pizza"
        yield "spaghetti"
        yield "salad"
        yield "water"
        yield "bread"
      end
    end

    menu_options = Menu.new

    menu_options.each do |item|
      puts "would you like : #{item}"
    end

    p menu_options.find(|item| item = "pizza"}  # "pizza"
    p menu_options.select {|item| item.size <= 5}  # select items <= 5 chars  ["pizza", "salad", "water", "bread"]
    p menu_options.reject {|item| item.size <= 5}  # "[spaghetti"]
    p menu_options.first  # "pizza"
    p menu_options.take(2)  # ["pizza", "spaghetti"]
    p menu_options.drop(2)
    p menu_options.min  # based off numbers or alpha - "bread"
    p menu_options.max  # "water"
    p menu_options.sort
    menu_options.reverse_each {|item| puts item}

# Class

A simple class without getter or setting functions

## animal.rb

    class Animal
      def initialize
        puts "Creating a New Animal"
      end

      def set_name(new_name)
        @name = new_name
      end

      def get_name
        @name
      end

      def name
        @name
      end

      def name=(new_name)
        if new_name.is_a?(Numeric)
          puts "Name Can't Be a Number"
        else
          @name = new_name
        end
      end
    end

    cat = Animal.new
    cat.set_name("Peekaboo")
    puts cat.get_name
    puts cat.name
    cat.name = "Messi"
    puts cat.name

## dog.rb

A Dog Class with `attr_reader`, `attr_writer`, `attr_accessor`

    class Dog
      attr_accessor :name, :height, :weight
        
      def bark
        return "Generic Bark"
      end
    end

    rover = Dog.new
    rover.name = "Rover"
    puts rover.name

## Class Inheritance

Here we override the `bark` method

    class GermanShepard < Dog
      def bark
        return "Loud Bark"
      end
    end

    max = GermanShepard.new
    max.name = "Max"  # name is inheriated from Dog
    printf "%s goes %s \n", max.name, max.bark()

# Modules

Modules are made up of methods and instance variables like Classes, except you cannot instantiate Modules.
Modules add functionality to a Class. You can inherit multiple modules.

## human.rb

    module Human
      attr_accessor :name, :height, :weight

      def run
        puts self.name + " runs"
      end
    end

## smart.rb

    module Smart
      def act_smart
        return "E = mc^2"
      end
    end

## relative import

    require_relative "human"
    require_relative "smart"

    module Animal
      def make_sound
        puts "Grrr"
      end
    end

    class Dog
      include Animal
    end

    rover = Dog.new
    rover.make_sound

    class Scientist
      include Human
      prepend Smart  # Smart act_smrt will supercede the below act_smart since its prepended instead of include
    
      def act_smart
        return "E = mc something"
      end
    end

    einstein = Scientist.new
    einstain.name = "Albert"
    puts einstein.name
    einstein.run
    puts einstein.name

## Polymorphism

Polymorphism works a little different in Ruby than other languages.
Statically typed languages uses __duck typing__ (if it walks like a duck and quacks like a duck,
then it must be a duck); basically, an object's suitability is determined by the presence of certain
methods and properties instead of the object type.

    class Bird
      def tweet(bird_type)
        bird_type.tweet
      end
    end

    class Cardinal < Bird
      def tweet
        puts "Tweet Tweet"
      end
    end

    class Parrot < Bird
      def tweet
        puts "Squawk"
      end
    end

    generic_bird = Bird.new
    generic_bird.tweet(Cardinal.new)  # Tweet Tweet
    generic_bird.tweet(Parrot.new)  # Squawk

# Iteration

## for-loop

    numbers = [1,2,3,4,5]
    for number in numbers:
      puts "#{number},"  # use print if you don't want a newline
    end

    # Result
    1,
    2,
    3,
    4,
    5,

## loop

    x = 1

    loop do
      x += 1
      next unless (x % 2) == 0
      puts x

      break if x >= 10
    end

    # Results
    2
    4
    6
    8
    10


## while

    y = 1
    while y <= 10
      y += 1
      next unless (y % 2) == 0
      puts y
    end 

    # Results 
    2
    4
    6
    8
    10

# Functions

    def add_nums(num_1, num_2)  # variables are passed by value
      return num_1.to_i + num_2.to_i  # returns to integer
    end

    puts add_nums(3, 4)  # 7

# Exceptions

## rescue

Rescue is a catchall

    print "Enter a Number : "
    first_num = gets.to_i
    print "Enter Another Number : "
    second_num = gets.to_i

    begin
      answer = first_num / second_num
    
    rescue # this is called if there's an error
      puts "You cannot divide by zero"
      exit
    end 

    puts "#{first_num} / #{second_num} = #{answer}"

## raise

Raise is a specific rescue

    def check_age(age)
      raise ArgumentError, "Enter Positive Number" unless age > 0
    end

    begin
      check_age(-1)
    rescue ArgumentError
      puts "That is an impossible age"
    end

# Files

    file = File.new("authors.out", "w")
    file.puts "William Shakespeare"
    file.puts "Agatha Christie"
    file.puts "Barbara Cartland"
    file.close
    puts File.read("authors.out")

    file = File.new("authors.out", "a")  # append
    file.puts "Danielle Steel"
    file.close
    puts File.read("authors.out")

    file = File.new("authors_info.out", "w")
    file.puts "William Shakespeare,English,plays and poetry,4 billion"
    file.puts "Agatha Christie,English,who done its,4 billion"
    file.puts "Barbara Cartland,English,romance novels,1 billion"
    file.puts "Danielle Steel,English,romance novels,800 million"
    file.close

    File.open("authors_info.out") do |record|
      record.each do |item|
        name, lang, specialty, sales = item.chomp.split(',')
        puts "#{name} was an #{lang} author that specialized in #{specialty} with #{sales} sales"
      end
    end

# Classes

## Class Structure

    class Person
      # extend and include go first
      extend SomeModule
      include AnotherModule

      # inner classes
      CustomError = Class.new(StandardError)

      # constants are next
      SOME_CONSTANT = 20

      # afterwards we have attribute macros
      attr_reader :name

      # followed by other macros (if any)
      validates :name

      # public class methods are next in line
      def self.some_method
      end

      # initialization goes between class methods and other instance methods
      def initialize
      end

      # followed by other public instance methods
      def some_method
      end

      # protected and private methods are grouped near the end
      protected

      def some_protected_method
      end

      private

      def some_private_method
      end
    end

## instance methods vs class methods

Class methods are methods that are called on a class.
Instance methods are methods that are called on an instance of a class.

    class Foo
      def self.bar
        puts 'class method'
      end
      
      def baz
        puts 'instance method'
      end
    end

    Foo.bar # => "class method"
    Foo.baz # => NoMethodError: undefined method ‘baz’ for Foo:Class

    Foo.new.baz # => instance method
    Foo.new.bar # => NoMethodError: undefined method ‘bar’ for #<Foo:0x1e820>

## Ways of declaring class methods

There's a few ways of declaring class methods. I prefer the first way. The second way is harder to see if
something is a class method if you have a lot of class methods. The third seems uncommon.

    # Way 1
    class Foo
      def self.bar
        puts 'class method'
      end
    end

    Foo.bar # "class method"

    # Way 2
    class Foo
      class << self
        def bar
          puts 'class method'
        end
      end
    end

    Foo.bar # "class method"

    # Way 3
    class Foo; end
    def Foo.bar
      puts 'class method'
    end

    Foo.bar # "class method"

### self inside the metaclass

Lets you access the object's metaclass (aka the 'singleton class', the 'eigenclass')

    class Ghost
      class << self 
        def method1
        end

        def method2
        end
      end
    end

## Ways of declaring instance methods

The main difference between instance methods and class methods is that
with an instance method, you have to declare an instance and then you
can use the method.

    Way 1
    class Foo
      def baz
        puts 'instance method'
      end
    end

    Foo.new.baz # "instance method"

    # Way 2
    class Foo
      attr_accessor :baz
    end

    foo = Foo.new
    foo.baz = 'instance method'
    puts foo.baz

    # Way 3
    class Foo; end

    foo = Foo.new
    def foo.bar
      puts 'instance method'
    end

    Foo.new.baz # "instance method"

## `include` vs `extend`

`include` makes the foo method available to an instance of a class and
`extend` makes the foo method available to the class itself

    module Foo
      def foo
        puts 'heyyyyoooo!'
      end
    end

    class Bar
      include Foo
    end

    Bar.new.foo # heyyyyoooo!
    Bar.foo # NoMethodError: undefined method ‘foo’ for Bar:Class

    class Baz
      extend Foo
    end

    Baz.foo # heyyyyoooo!
    Baz.new.foo # NoMethodError: undefined method ‘foo’ for #<Baz:0x1e708>

# Setup IntelliJ with Ruby

* Make sure to setup 'Project Settings' > 'Project' to point to the correct SDK
* Make sure to setup 'Modules' > 'Add Content Root' if you have multiple projects (e.g. one project in another folder
  references outside that project)
* When picking 'Modules', do ues JRuby (use Ruby)   
* Use 'Script Arguments' (not 'Ruby arguments') when setting up Configurations

# Linting with Rubocop

Install with `gem install rubocop`

Lint with `rubocop` (can pass in files or it'll check that current dir)

# Ruby Style Guide

https://github.com/rubocop-hq/ruby-style-guide
