# Cheatsheet

_Taken from_ [https://fleeblewidget.co.uk/2014/05/byebug-cheatsheet/](https://fleeblewidget.co.uk/2014/05/byebug-cheatsheet/)

## Starting Byebug

If you’re running Byebug on a Rails application in development mode, you no longer need to start the server with `--debugger` – the debugger is on by default.

To get going, simply type `byebug` \(or `debugger`\) into your source file at the line you’re interested in and run the program. If you’re running it on a Rails application, remember to switch to your terminal window to look at debugger output.

**Note:** `byebug` invocations are just method calls, so you can make them conditional:

```ruby
byebug if foo == “bar”
```

**Another Note:** As is common with debuggers, hitting ‘Enter’ on an empty line in Byebug repeats the last command.

## Stopping Again

### q\[uit\] — a.k.a. “exit” _unconditionally_

Quit. It stops the thing running. Also exits your program.Note:\*\* To quit without an ‘are you sure?’ prompt, use `quit unconditionally` \(shortened to `q!`\)

### kill

_Really_ quit. This uses `kill -9`, for situations where quit just isn’t fierce enough.

## Essential Commands

### c\[ontinue\] &lt;line\_number&gt;

Carry on running until program ends, hits a breakpoint or reaches line _line\_number_ \(if specified\).

### n\[ext\] &lt;number&gt;

Go to next line, stepping over function calls. If _number_ specified, go forward that number of lines.

### s\[tep\] &lt;number&gt;

Go to next line, stepping into function calls. If _number_ is specified, make that many steps.

### b\[ack\]t\[race\] — a.k.a. “w\[here\]”

Display [stack trace](http://en.wikipedia.org/wiki/Stack_trace).

### h\[elp\] &lt;command\_name&gt;

Get help. With no arguments, returns a list of all the commands Byebug accepts. When passed the name of a command, gives help on using that command.

## Breakpoints and Catchpoints

### b\[reak\]

Sets a [breakpoint](http://en.wikipedia.org/wiki/Breakpoint) at the current line. These can be conditional: `break if foo != bar`. Keep reading for more ways to set breakpoints!

### b\[reak\] &lt;filename&gt;:&lt;line\_number&gt;

Puts a breakpoint at _line-number_ in _filename_ \(or the current file if _filename_ is blank\). Again, can be conditional: `b myfile.rb:15 unless my_var.nil?`

### b\[reak\] &lt;class&gt;\(.\|\#\)&lt;method&gt;

Puts a breakpoint at the start of the method _method_ in class _class_. Accepts an optional condition: `b MyClass#my_method if my_boolean`

### info breakpoints

List all breakpoints, with status.

### cond\[ition\] &lt;number&gt; &lt;expression&gt;

Add condition _expression_ to breakpoint &lt;number&lt;&gt;&gt;. If no _expression_ is given, removes any conditions from that breakpoint.

### del\[ete\] &lt;number&gt;

Deletes breakpoint &lt;number&gt;. With no arguments, deletes all breakpoints.

### disable breakpoints &lt;number&gt;

Disable \(but don’t delete\) breakpoint &lt;number&gt;. With no arguments, disables all breakpoints.

### cat\[ch\] exception&gt; off

Enable or \(with _off_ argument\) disable catchpoint on &lt;exception&gt;.

### cat\[ch\]

Lists all catchpoints.

### cat\[ch\] off

Deletes all catchpoints.

### sk\[ip\]

Passes a caught exception back to the application, skipping the catchpoint.

## Program Stack

### b\[ack\]t\[race\] — a.k.a. “w\[here\]”

Display [stack trace](http://en.wikipedia.org/wiki/Stack_trace).

### f\[rame\] &lt;frame\_number&gt;

Moves to &lt;frame\_number&gt; \(frame numbers are shown by `bt`\). With no argument, shows the current frame.

### up &lt;number&gt;

Move up &lt;number&gt; frames \(or 1, if no number specified\).

### down &lt;number&gt;

Move down &lt;number&gt; frames \(or 1, if no number specified\).

### info args

Arguments of the current frame.

### info locals

Local variables in the current stack frame.

### info instance\_variables

Instance variables in the current stack frame.

### info global\_variables

Current global variables.

### info variables

Local and instance variables of the current frame.

### m\[ethod\] &lt;class\|module&gt;

Shows instance methods of the given class or module.

### m\[ethod\] i\[nstance\] &lt;object&gt;

Shows methods of &lt;object&gt;.

### m\[ethod\] iv &lt;object&gt;

Shows instance variables of &lt;object&gt;.

### v\[ar\] cl\[ass\]

Shows class variables of self.

### v\[ar\] co\[nst\] &lt;object&gt;

Shows constants of &lt;object&gt;.

### v\[ar\] g\[lobal\]

Shows global variables \(same as `info global_variables`\).

### v\[ar\] i\[nstance\] &lt;object&gt;

Shows instance variables of \ \(same as `method iv <object>`\).

### v\[ar\] l\[ocal\]

Shows local variables \(same as `info locals`\).

## Execution Control

### c\[ontinue\] &lt;line\_number&gt;

Carry on running until program ends, hits a breakpoint or reaches line &lt;line\_number&gt; \(if specified\).

### n\[ext\] &lt;number&gt;

Go to next line, stepping over function calls. If &lt;number&gt; specified, go forward that number of lines.

### s\[tep\] &lt;number&gt;

Go to next line, stepping into function calls. If &lt;number&gt; is specified, make that many steps.

### fin\[ish\] &lt;num\_frames&gt;

With no argument, run until the current frame returns. Otherwise, run until &lt;num\_frames&gt; have returned.

### irb

Start an IRB session. This will have added commands `cont`, `n` and `step`, but these can’t take arguments \(unlike the proper byebug commands of the same name\).

### restart

Restart the program. This also restarts byebug.

## Threads

### th\[read\]

Show current thread.

### th\[read\] l\[ist\]

List all threads.

### th\[read\] stop &lt;number&gt;

Stop thread number &lt;number&gt;.

### th\[read\] resume &lt;number&gt;

Resume thread number &lt;number&gt;.

### th\[read\] &lt;number&gt;

Switch context to thread &lt;number&gt;.

## Display

### e\[val\] — a.k.a. “p” &lt;expression&gt;

Evaluate &lt;expression&gt; and display result. By default, you can also just type the expression without any command and get the same thing \(disabled by using `set noautoeval`\).

### pp

Evaluate expression and pretty-print the result.

### putl

Evaluate an expression with an array result and columnize the output.

### ps

Evaluate an expression with an array result, sort and columnize the output.

### disp\[lay\] &lt;expression&gt;

Automatically display &lt;expression&gt; every time the program halts. With no argument, lists the current display expressions.

### info display

List all current display expressions.

### undisp\[lay\] &lt;number&gt;

Remove display expression number &lt;number&gt; \(as listed by `info display`\). With no argument, cancel all current display expressions.

### disable display &lt;number&gt;

Stop displaying expression number &lt;number&gt;. The display expression is kept in the list, though, and can be turned back on again using `enable display`.

### enable display &lt;number&gt;

Re-enable previously disabled display expression &lt;number&gt;.

## Controlling Byebug

### hist\[ory\] &lt;num\_commands&gt;

View last &lt;num\_commands&gt; byebug commands \(or all, if no argument given\).

### save &lt;file&gt;

Saves current byebug session options as a script file in &lt;file&gt;.

### source &lt;file&gt;

Loads byebug options from a script file at &lt;file&gt;.

### set &lt;option&gt;

Change value of byebug option &lt;option&gt;.

### show &lt;option&gt;

View current value of byebug option &lt;option&gt;.

Options are:

* `autoeval`
* `autoirb`
* `autolist`
* `autoreload`
* `autosave`
* `basename`
* `callstyle`
* `forcestep`
* `fullpath`
* `histfile`
* `histsize`
* `linetrace`
* `tracing_plus`
* `listsize`
* `post_mortem`
* `stack_on_error`
* `testing`
* `verbose`
* `width`

## Source Files and Code

### reload

Reload source code.

### info file

Information about the current source file.

### info files

All currently loaded files.

### info line

Shows the current line number and filename.

### l\[ist\]

Shows source code after the current point. Keep reading for more list options.

### l\[ist\] –

Shows source code before the current point.

### l\[ist\] =

Shows source code centred around the current point.

### l\[ist\] &lt;first&gt;-&lt;list&gt;

Shows all source code from &lt;first&gt; to &lt;last&gt; line numbers.

### edit &lt;file:line\_no&gt;

Edit &lt;file&gt;. With no arguments, edits the current file.

