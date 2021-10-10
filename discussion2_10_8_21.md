Today: Programming in Python and Lisp

### Programming Languages
Levels: 
- high
- Scripting (py, js, shell,..)
- assembly
- machine 

#### Compiled vs. Interpreted
classic advantages and disadvantages

### Lisp Syntax
Atom and and S Expressions

- *atom* -- a piece of data, a string, bool, etc. Not saved to memory anywhere. 
- *S-Expression* ($f$ $x_1, ..., x_n$) function and arguments. 

### Lisp Syntax list
1. `cons` create a linked list. (cons 1 2 3) 1 points to 2, 2 to 3, ... . 
2. `list` create a linked list without writing a ton of `cons` 
3. `car` first element
4. `cdr` all but first element, could be a list 
5. 0 is not false in lisp. `nil` is the only false thing.
6. To compare nums `=` `eql` memory adress, `equal` checks semnatic values. 
7. `defparameter` `defvar` -- sorta const? `setq` -- all global vars, of some sort, or whatever.
8. `(defun NAME ([args], &optional OptArgs) code)
9. `if`, `cond` which is like a switch. Recursion. 
10. you can chain `cdr` and `car` from right to left  


