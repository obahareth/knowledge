# Cheatsheet

*Taken from https://fleeblewidget.co.uk/2014/05/byebug-cheatsheet/*

## Starting Byebug



If you’re running Byebug on a Rails application in development mode, you no longer need to start the server with `--debugger` – the debugger is on by default.

To get going, simply type `byebug` (or `debugger`) into your source file at the line you’re interested in and run the program. If you’re running it on a Rails application, remember to switch to your terminal window to look at debugger output.

**Note:** `byebug` invocations are just method calls, so you can make them conditional:

```rb
byebug if foo == “bar”
```

**Another Note:** As is common with debuggers, hitting ‘Enter’ on an empty line in Byebug repeats the last command.

## Stopping Again



### q[uit] — a.k.a. “exit” *unconditionally*

Quit. It stops the thing running. Also exits your program.Note:** To quit without an ‘are you sure?’ prompt, use `quit unconditionally` (shortened to `q!`)

### kill

*Really* quit. This uses `kill -9`, for situations where quit just isn’t fierce enough.

## Essential Commands



### c[ontinue] \<line_number>

Carry on running until program ends, hits a breakpoint or reaches line *line-number* (if specified).

### n[ext] \<number>

Go to next line, stepping over function calls. If *number* specified, go forward that number of lines.

### s[tep] \<number>

Go to next line, stepping into function calls. If *number*is specified, make that many steps.

### b[ack]t[race] — a.k.a. “w[here]”

Display [stack trace](http://en.wikipedia.org/wiki/Stack_trace).

### h[elp] \<command_name>

Get help. With no arguments, returns a list of all the commands Byebug accepts. When passed the name of a command, gives help on using that command.

## Breakpoints and Catchpoints



### b[reak]

Sets a [breakpoint](http://en.wikipedia.org/wiki/Breakpoint) at the current line. These can be conditional: `break if foo != bar`. Keep reading for more ways to set breakpoints!

### b[reak] \<filename>:\<line_number>

Puts a breakpoint at *line-number* in *filename* (or the current file if *filename* is blank). Again, can be conditional: `b myfile.rb:15 unless my_var.nil?`

### b[reak] \<class>(.|#)\<method>

Puts a breakpoint at the start of the method *method* in class *class*. Accepts an optional condition: `b MyClass#my_method if my_boolean`

### info breakpoints

List all breakpoints, with status.

### cond[ition] \<number> \<expression>

Add condition *expression* to breakpoint \<number\<>>. If no *expression* is given, removes any conditions from that breakpoint.

### del[ete] \<number>

Deletes breakpoint \<number>. With no arguments, deletes all breakpoints.

### disable breakpoints \<number>

Disable (but don’t delete) breakpoint \<number>. With no arguments, disables all breakpoints.

### cat[ch] \<exception> off

Enable or (with *off* argument) disable catchpoint on \<exception>.

### cat[ch]

Lists all catchpoints.

### cat[ch] off

Deletes all catchpoints.

### sk[ip]

Passes a caught exception back to the application, skipping the catchpoint.

## Program Stack



### b[ack]t[race] — a.k.a. “w[here]”

Display [stack trace](http://en.wikipedia.org/wiki/Stack_trace).

### f[rame] \<frame_number>

Moves to \<frame-number> (frame numbers are shown by `bt`). With no argument, shows the current frame.

### up \<number>

Move up \<number> frames (or 1, if no number specified).

### down \<number>

Move down \<number> frames (or 1, if no number specified).

### info args

Arguments of the current frame.

### info locals

Local variables in the current stack frame.

### info instance_variables

Instance variables in the current stack frame.

### info global_variables

Current global variables.

### info variables

Local and instance variables of the current frame.

### m[ethod] \<class|module>

Shows instance methods of the given class or module.

### m[ethod] i[nstance] \<object>

Shows methods of \<object>.

### m[ethod] iv \<object>

Shows instance variables of \<object>.

### v[ar] cl[ass]

Shows class variables of self.

### v[ar] co[nst] \<object>

Shows constants of \<object>.

### v[ar] g[lobal]

Shows global variables (same as `info global_variables`).

### v[ar] i[nstance] \<object>

Shows instance variables of \<object> (same as `method iv <object`).

### v[ar] l[ocal]

Shows local variables (same as `info locals`).

## Execution Control



### c[ontinue] \<line_number>

Carry on running until program ends, hits a breakpoint or reaches line \<line_number> (if specified).

### n[ext] \<number>

Go to next line, stepping over function calls. If \<number> specified, go forward that number of lines.

### s[tep] \<number>

Go to next line, stepping into function calls. If \<number> is specified, make that many steps.

### fin[ish] \<num_frames>

With no argument, run until the current frame returns. Otherwise, run until \<num_frames> have returned.

### irb

Start an IRB session. This will have added commands `cont`, `n` and `step`, but these can’t take arguments (unlike the proper byebug commands of the same name).

### restart

Restart the program. This also restarts byebug.

## Threads



### th[read]

Show current thread.

### th[read] l[ist]

List all threads.

### th[read] stop \<number>

Stop thread number \<number>.

### th[read] resume \<number>

Resume thread number \<number>.

### th[read] \<number>

Switch context to thread \<number>.

## Display



### e[val] — a.k.a. “p” \<expression>

Evaluate \<expression> and display result. By default, you can also just type the expression without any command and get the same thing (disabled by using `set noautoeval`).

### pp

Evaluate expression and pretty-print the result.

### putl

Evaluate an expression with an array result and columnize the output.

### ps

Evaluate an expression with an array result, sort and columnize the output.

### disp[lay] \<expression>

Automatically display \<expression> every time the program halts. With no argument, lists the current display expressions.

### info display

List all current display expressions.

### undisp[lay] \<number>

Remove display expression number \<number> (as listed by `info display`). With no argument, cancel all current display expressions.

### disable display \<number>

Stop displaying expression number \<number>. The display expression is kept in the list, though, and can be turned back on again using `enable display `.

### enable display \<number>

Re-enable previously disabled display expression \<number>.

## Controlling Byebug



### hist[ory] \<num_commands>

View last \<num_commands> byebug commands (or all, if no argument given).

### save \<file>

Saves current byebug session options as a script file in \<file>.

### source \<file>

Loads byebug options from a script file at \<file>

### set \<option>

Change value of byebug option \<option>.

### show \<option>

View current value of byebug option \<option>.

Options are: 

- `autoeval`
- `autoirb`
- `autolist`
- `autoreload`
- `autosave`
- `basename`
- `callstyle`
- `forcestep`
- `fullpath`
- `histfile`
- `histsize`
- `linetrace`
- `tracing_plus`
- `listsize`
- `post_mortem`
- `stack_on_error`
- `testing`
- `verbose`
- `width`

## Source Files and Code



### reload	

Reload source code.

### info file

Information about the current source file.

### info files

All currently loaded files.

### info line

Shows the current line number and filename.

### l[ist]

Shows source code after the current point. Keep reading for more list options.

### l[ist] –

Shows source code before the current point.

### l[ist] =

Shows source code centred around the current point.

### l[ist] \<first>-\<last>

Shows all source code from \<first> to \<last> line numbers.

### edit \<file:line_no>

Edit \<file>. With no arguments, edits the current file.