Today: 
- modes of interaction

### Modes of Interaction
GOALS: 
- make the UI simple and global interface where always the same thing, can get things done no matter where you are 
    - easy to learn
    - easy to remember
    - BUT can't tune and specialize the UI for certain tasks
- Have an efficient UI tailored for a particular task. 
    - If you're an expert — you'll go faster.
    - EX: Emacs. 

#### What's a mode
an specific environment tailored for a particular task. 
- good because specialization. 
- bad because stuff that you used to do in a different mode doesn't work in this one. 
- EX in Emcas: the `dired` mode.





*as a guide, think photoshop vs procreate*


### Lisp in \*scratch\* buffer
(+ 2 2), then `C-j` yields 4.


### Compilation and Interpretation
So, what does `C-j` do? 

    Creates a data structure representing your function. Postorder traversal. Too slow for us.  

So how do we make it faster? 

*Byte-code interperter*: instaed of translating into a data structure, we do something smaller and more interpertable. Stack operations instead of a tree. Fetch a byte, do a switch statement to understadn what it does, do it, go onto the next byte, quicker because we don't need the pointers. 

To see this byte compile `M-x byte-compile dist.el RET` -- compiles and shows on buffer. Like java, we are *machine independent*, which is a HUGE advantage. 

### What are your options when executing code? (Compilation and Interpretation) 
- Interpret data structures. -- simpler than 2, easier to debug (know where you are). Debugging machine code is yikes. For development it's convenient to debug.   
- Interpret byte-codes. Never goes to assembly code, you have a c code with a huge switch statement and a stack which runs the code (and that is what the machine directly runs -- exists in machine code). -- much simpler to implement and write a debugger for than 3. 
- Half-way between 2 and 4. *just-in-time compilation* (JIT) compile to machine code at run-time. Could be done from byte code, code directly, etc. Done in browser for JS, from source to machine code. Coming to emacs soon.
- Compile to machine code -- no machine independent. `ek foo.el` to compile lisp. 3 is not as fast, even after compiling. Can take it's "sweet time" to generate really good machine code 

### Lisp Syntax stuff
(global-set-key "@" 'what-cursor-position) --> correspond @ to the command what-cursor-position. 
' is how you quote something in lisp. 

To undo, (global-set-key "@" 'self-insert-command) -- bound the @ character to the self insert command which is the regular command everything is bound to. 

```language='lisp'
(defun function middle-of-buffer (&optional arg) 
  "Move point to the middle of the current buffer -- a function's docstring"
  (interactive "P")
  (goto-char (/  (+ (point-min) (point-max)) 2) 

middle-of-buffer




```


### Random Stuff (mostly commands)
 - ESC SHIFT-1 = M-!:  run shell command
 - C-u + what's above + Put the contents of the shell command output into the main buffer
 - `sed` command that allows you to place stream input in line or something? 
 - per buffer, emacs has two different pointers
    - point - cursor location -- find with `M-: (point)` RET
    - mark - the place you marked. `C-@`
- C-u puts the output of a buffer command wherever the input came from? or somewhere in the main buffer. 
- \*scratch\* buffer always in Emacs, it's a scratchpad.  
- `C-j` run the command in the line before the cursor in scratch for lisp commands in emacs.
- `(kill-buffer "*buffername*"`)
- `(list-buffers)`
- `elc`: file extension for emacs-lisp-bytecode. 
- `M-:` drop into lisp execution mode in the buffer. 
- `C-h f`: find command.  
- `C-x =`: What cursor position shortcut. 
- `C-q` quote the next charecter and don't do any of the fancy global variable stuff. 
- `kbd` creates the commads from what you wrote ( `kbd "C-x C-\\" returns "^X^\")
- `M-x` **debug-on-entry** commands: 
    - `c`: continue (go through the entire tree not line by line)
    - `d`: descend a level in (a lot of details -- way too much details) 
    - `e`: evaluate (know some property of the current function I'm debugging) For example,  `e arg` -- evaluate whatever code you want. 
    - `C-j` exit that debugging session.
### Questions
- what's `sed`? -- stream editior, makes one pass through stdin and does all sorts of basic text transformations, complex ass man page.
