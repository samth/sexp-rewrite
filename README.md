# sexp-rewrite

sexp-rewrite (sexprw for short) is an Emacs package for doing
pattern-based rewriting of sexp-structured code---ie, code in Lisp,
Scheme, and primarily Racket.

Some examples of "pattern-based rewriting" are:

 - Turn a chain of `if` expressions into a `cond` expression.

 - Rewrite an application of `map` with `lambda` to `for/list`---or
   `andmap` to `for/and`, etc.

 - Turn a `letrec` into a sequence of internal definitions.

The pattern language is simple enough that you can define your own ad
hoc rewriting rules.

Transformations preserve comments and are halfway decent at preserving
formatting, with the occasional help of appropriate spacing
annotations.

## Try it out

Visit `sexp-rewrite.el` and evaluate the buffer (`M-x eval-buffer`).
Then visit `racket-rewrites.el` and evaluate that buffer too.

Most rewriting rules (called "tactics") have examples after them.
Go to the example labeled "`example for if-to-cond...`" and place the
cursor at the first left parenthesis---that is, at `(if (< x 10)...`.

Run the `if-to-cond` tactic by entering `M-x sexprw-execute-tactic`
and then `if-to-cond`.
The `if` expression gets rewritten to a `cond` expression---but only
the first `if`; there are still `if`s left in the `else` branch.
Now run the `cond-else-absorb-if` tactic. There's a keybinding for
`sexprw-execute-tactic` that makes executing a specific tactic
quicker: `C-c C-d x`. Then type `cond-else-absorb-if` at the prompt
(you can use tab completion to save typing).
The `if` expression gets "absorbed" into the `cond`. There's just one
`if` left, but it's inside a `let`, so let's leave it alone for now.

What a bother to have to type in the tactic names. Fortunately,
there's an even quicker way.

Undo twice (`C-/ C-/`) to reset the example to the original form,
and make sure the cursor is back at the beginning of the example.
Now enter `C-c C-d e` (which runs the `sexprw-auto-expression`
command). The command automatically tries a bunch of tactics until one
works, and it reports the name of the tactic in the minibuffer.

Faster?

Undo once (`C-/`) to reset the example again.  Now type `C-c C-d r e`,
which repeatedly tries tactics (up to about a hundred times) until
they stop working. This time we get the first two `if` expressions in
one shot, as well as the `if` under the `let`, which can be converted
to an `=>` clause.

Note that the rewrite that produced the `=>` can be unsafe: the
else-branch expression was originally in the scope of the `let`-bound
variable, but after rewriting it is in a separate clause. That's
*usually* not a problem, because the variable is always known to be
false in the else, branch (unless it's mutated...) so there's no
reason to refer to it. But it's always a good idea to keep an eye on
the tactics applied to make sure they don't break your code.

## How to define tactics

To be continued.

## Other features

Type `C-c C-d s` or `M-x sexprw-search-pattern` to search forward for
sexps matching a given pattern.

Type `C-c C-d [` or `M-x sexprw-squarify` to convert round parentheses
into square brackets.

## Known bugs and limitations

This library has a very vague notion of sexp syntax. Notions like
improper lists are not supported, and `.` is treated as an
atom. Reader abbreviations like `'` for `quote` are not recognized.
