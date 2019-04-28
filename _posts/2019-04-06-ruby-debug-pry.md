---
layout: post
title: Ruby Debugging with Pry
---


# {{ page.title }}

You can debug in Ruby using a few different ways:

# Debug

## Embedded Ruby Debugger

    $ruby -r debug filename

Example Run debugger with:

    $ruby -r debug my_ruby_file.rb

List of debug commands

* `v l` to view local variables
* `v g` to view global variables
* `w` to display stack frame

## byebug

Install

    gem install byebug

## pry-byebug

github.com/deivid-rodriguez/pry-byebug

    $gem install pry-byebug --user-install


## Pry

Pry is a runtime developer console and IRB alternative with introspection capabilities.

Install Pry

    sudo apt-get install pry 
    gem install pry --user-install

Using Pry

    require 'pry' # equivalent of python's `import pdb`
    # Misc code
    binding.pry  # equivalent of `pdb.set_trace()`

To run a program with pry (and require `pry`, add `-rpry`) and run:

    ruby -rpry myrubyprogram.rb

Example usage (basically `binding.pry` is the Python `pdb.set_trace()` equivalent)

    william.liu@us-wliu ~/workspace/ruby-examples/ruby_lang (master) $ ruby -rpry pry_example.rb

    From: /Users/william.liu/workspace/ruby-examples/ruby_lang/pry_example.rb @ line 15 :

        10: x = 10
        11:
        12: # start a REPL session
        13: binding.pry
        14:
     => 15: puts "Program will resume here. Value of x is: #{x}."
        16:
        17: puts "End here!"

    [1] pry(main)> ls
    self.methods: inspect  to_s
    locals: _  __  _dir_  _ex_  _file_  _in_  _out_  _pry_  a  x
    [2] pry(main)> puts a
    #<A:0x007fcc981925f0>
    => nil
    [3] pry(main)> a
    => #<A:0x007fcc981925f0>
    [4] pry(main)> exit
    Program will resume here. Value of x is: 10.
    End here!

### Pry Commands

Pry Help to see a list of commands inside of pry:

    $ pry
    [1] pry(main)> help
    Help
      help               Show a list of commands or information about a specific command.

    Context
      cd                 Move into a new context (object or scope).
      find-method        Recursively search for a method within a class/module or the current namespace.
      ls                 Show the list of vars and methods in the current scope.
      pry-backtrace      Show the backtrace for the pry session.
      raise-up           Raise an exception out of the current pry instance.
      reset              Reset the repl to a clean state.
      watch              Watch the value of an expression and print a notification whenever it changes.
      whereami           Show code surrounding the current context.
      wtf?               Show the backtrace of the most recent exception.

    Editing
      /^\s*!\s*$/        Clear the input buffer.
      amend-line         Amend a line of input in multi-line mode.
      edit               Invoke the default editor on a file.
      hist               Show and replay readline history.
      play               Playback a string variable, method, line, or file as input.
      show-input         Show the contents of the input buffer for the current multi-line expression.

    Introspection
      ri                 View ri documentation.
      show-doc           Show the documentation for a method or class.
      show-source        Show the source for a method or class.
      stat               View method information and set _file_ and _dir_ locals.

    Gems
      gem-cd             Change working directory to specified gem's directory.
      gem-install        Install a gem and refresh the gem cache.
      gem-list           List and search installed gems.
      gem-open           Opens the working directory of the gem in your editor.
      gem-readme         Show the readme bundled with a rubygem
      gem-search         Search for a gem with the rubygems.org json api
      gem-stat           Show the statistics of a gem (requires internet connection)

    Commands
      import-set         Import a pry command set.
      install-command    Install a disabled command.

    Aliases
      !!!                Alias for `exit-program`
      !!@                Alias for `exit-all`
      $                  Alias for `show-source`
      (?-mix:whereami[!?]+) Alias for `whereami`
      ?                  Alias for `show-doc`
      @                  Alias for `whereami`
      clipit             Alias for `gist --clip`
      file-mode          Alias for `shell-mode`
      history            Alias for `hist`
      quit               Alias for `exit`
      quit-program       Alias for `exit-program`
      reload-method      Alias for `reload-code`
      show-method        Alias for `show-source`

    Byebug
      backtrace          Display the current stack.
      break              Set or edit a breakpoint.
      continue           Continue program execution and end the pry session.
      down               Move current frame down.
      finish             Execute until current stack frame returns.
      frame              Move to specified frame #.
      next               Execute the next line within the current stack frame.
      step               Step execution into the next line or method.
      up                 Move current frame up.

    Input and output
      .<shell command>   All text following a '.' is forwarded to the shell.
      cat                Show code from a file, pry's input buffer, or the last exception.
      change-inspector   Change the current inspector proc.
      change-prompt      Change the current prompt.

#### Pry Commands

Good commands to know:

* `help`
* `ls --help`
* `ls`, `ls some_object`
* `cd`
* `whereami`
* `some_object.inspect`
* `some_object.class`
* `show-method some_function`
* `edit some_function`  # or some class method, e.g. `edit String#my_func`
* `.ls`
* `.cat`
* `.pwd`
* `self`


## dir() equivalent

Inspect an object with:

    my_object.class
    "MyStringObject".methods.sort
    my_object.instance_of? String  #=> True
    my_object.is_a?(String)

## Methods

You can remove an object's methods, e.g.

    >my_object.methods
    :public_foo, :public_bar

    >my_object.methods-Object.methods
    :something, :another

### Debugger

If you run a program with `debugger` instead of `binding.pry`, you'll get the debugger. You then run with
`ruby --debug my_progrma.rb`

`set autolist on` - will show where you are
`n` to skip down a line
`e` to evaluate an object (e[val] <expression>)
Hitting `return` runs the last command
`s` to step into (`s[tep]`)
`dis` to display an expression list (e.g. going through a loop and watch it change)

