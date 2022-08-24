# Overview

!!! warning "Warning"

    This project is no longer maintained

In this work we aim at providing an effective support for reasoning about
imperative programs with data structures and aliasing, by extending the
expressiveness of more familiar type-based verification towards more informative
logical reasoning, without compromising soundness and completeness.

Currently, is hard to reason about global and shape properties of data
structures. Existing tools require too much effort, a lot of experience from the
user and rely on mechanisms that don't scale.

We develop an assertion language that is closer to a programming language, that
we call, functional assertion language, which is easier to use for programmers,
and amenable to automatic verification.

This prototype checks that programs comply to their specification, that is, if
we satisfy the pre-condition of a program, after the execution of the program we
ensure that the post-condition holds.

## Downloads

- [Packaged Version][prototype]

## Installation

- Download the packaged version above

- Unpack the `tar.gz` file

- Execute the `improve` script following the instructions [below](#usage)

## Usage

```
ImProVe [version 0.2.0]. (C) Copyright 2015
Usage: sh improve [OPTION]...

Options:

  -a, --api                Force smt api usage

  -d, --debug  <mode>      Debug mode (default = NONE)

  -r, --fileInteractive    File interactive mode

  -f, --files  <file>...   Input file list

  -i, --interactive        Interactive mode

  -o, --output  <file>     Redirect program output to a file

  -p, --process            Force smt process usage

  -v, --shortVersion       Version number

  -s, --stdin              Read from standard input

  -t, --test               Test mode

      --help               Show help message

      --version            Show version of this program
```

### Notes

For option `-f`, `--files`, if `<file>` is a directory all files with extension
.imp in the directory <file> are executed.

By default, if you don't provide arguments the program will execute in
interactive mode.

An ImProVe program is composed by a sequence (possibly empty) of type
definitions, predicate definitions or triples (pre-condition, imperative program
and post-condition), as illustrated in the concrete syntax [below](#syntax).

## Quick Example

```
x:ref int

{ let a = !x in a > 0 } x := -3 { let b = !x in b < 0 }

;;
```

## Syntax

$$
$$\begin{array}{l l l l}
\mathit{program} & :: = & program\_element^* & \mbox{(Programs)}\\
\\
\mathit{program\_element} & ::= & & \mbox{(Program Elements)}\\
& \mid & \mathit{type\_definition} & \\
& \mid & \mathit{predicate\_definition} & \\
& \mid & \mathit{triple} &\\
& \mid & \mathit{clear\_env} \mid \mathit{show\_env}\\
\\
\mathit{clear\_env} & ::= & & \mbox{(Clear Environments)}\\
& \mid & \mathbf{clear} & \mbox{(Clear All)}\\
& \mid & \mathbf{clear}\; \mathbf{vars} & \mbox{(Clear Variables)}\\
& \mid & \mathbf{clear}\; \mathbf{types} & \mbox{(Clear Type Definitions)}\\
\\
\mathit{show\_env} & ::= & & \mbox{(Show Environments)}\\
& \mid & \mathbf{show} & \mbox{(Show All)}\\
& \mid & \mathbf{show}\; \mathbf{vars} & \mbox{(Show Variables)}\\
& \mid & \mathbf{show}\; \mathbf{types} & \mbox{(Show Type Definitions)}\\
\\
\mathit{triple} & ::= & \{ \;\mathit{assertion}\; \} \;\mathit{imp}\; \{ \;\mathit{assertion}\; \} & \mbox{(Triple)}\\
\\
\mathit{imp} & ::= & & \mbox{(Imperative Program)}\\
& \mid & \mathbf{skip} & \mbox{(Skip)}\\
& \mid & \mathbf{if} \; \mathit{term} \; \mathbf{then} \; \mathit{imp} \; \mathbf{else} \; \mathit{imp} & \mbox{(Conditional)}\\
& \mid & \mathbf{while} \; \mathop{!}x \; \mathbf{invariant} \; \mathit{assertion} \; \mathbf{do} \; \mathit{imp} & \mbox{(Loop)}\\
& \mid & \mathbf{let} \; x = \mathit{term} \; \mathbf{in} \; \mathit{imp} & \mbox{(Local Declaration)}\\
& \mid & \mathbf{let} \; x = \mathop{!}y \; \mathbf{in} \; \mathit{imp} & \mbox{(Reference Lookup)}\\
& \mid & \mathbf{let} \; x = \mathbf{var}(\mathit{term}) \; \mathbf{in} \; \mathit{imp} & \mbox{(Allocation)}\\
& \mid & x := \mathit{term} & \mbox{(Assignment)}\\
& \mid & \mathit{imp} ; \mathit{imp} & \mbox{(Sequential Composition)}\\
\\
\mathit{assertion} & ::= & & \mbox{(Functional Assertions)}\\
& \mid & \mathbf{if} \; \mathit{term} \; \mathbf{then} \; \mathit{assertion} \; \mathbf{else} \; \mathit{assertion} & \mbox{(Conditional)}\\
& \mid & \mathbf{let} \; x = \mathit{term} \; \mathbf{in} \; \mathit{assertion} & \mbox{(Local Declaration)}\\
& \mid & \mathbf{let} \; x = \mathop{!}y \; \mathbf{in} \; \mathit{assertion} & \mbox{(Reference Lookup)}\\
& \mid & \mathbf{rec} \; R\left(\overline{x}\right).\; \mathit{assertion} & \mbox{(Recursive Assertion)}\\
& \mid & \mathit{assertion}\left[\overline{\mathit{term}}\right] & \mbox{(Application)}\\
& \mid & \mathit{assertion} \mathbin{*} \mathit{assertion} & \mbox{(Separating Conjunction)}\\
& \mid & \mathit{assertion} \mathbin{\mathbf{and}} \mathit{assertion} & \mbox{(Conjunction)}\\
& \mid & \mathit{term} = \mathit{term} & \mbox{(Equality)}\\
& \mid & \mathbf{not}\; \mathit{assertion} & \mbox{(Negation)}\\
& \mid & \mathbf{emp} & \mbox{(Empty)}\\
& \mid & \mathbf{true} \mid \mathbf{false} & \mbox{(Boolean Values)}\\
\\
\mathit{term} & ::= & & \mbox{(Terms)}\\
& \mid & \left[{\mathit{label}_i = \mathit{term}_i}^{i \in \{ 1 \dots n \}}\right] & \mbox{(Record)}\\
& \mid & \mathit{term}.\mathit{label} & \mbox{(Projection)}\\
& \mid & \mathit{term}\odot\mathit{term} & \mbox{(Binary Operations)}\\
& \mid & \ominus\mathit{term} & \mbox{(Unary Operations)}\\
& \mid & \mathbf{fold}\left[\tau\right]\; \mathit{term} & \mbox{(Fold)}\\
& \mid & \mathbf{unfold}\left[\tau\right]\; \mathit{term} & \mbox{(Unfold)}\\
& \mid & x & \mbox{(Identifier)}\\
& \mid & value & \mbox{(Value)}\\
\\
\mathit{value} & ::= & & \mbox{(Values)}\\
& \mid & \left[{\mathit{label}_i = \mathit{value}_i}^{i \in \{ 1 \dots n \}}\right] & \mbox{(Record)}\\
& \mid & n & \mbox{(Number)}\\
& \mid & \mathbf{null} & \mbox{(Null Reference)}\\
& \mid & \mathbf{true} \mid \mathbf{false} & \mbox{(Boolean Values)}\\
\\
\mathit{type\_definition} & ::= & \mathbf{type} \; x = \tau & \mbox{(Type Definition)}\\
\\
\mathit{\tau} & ::= & & \mbox{(Types)}\\
& \mid & \mathbf{int} \mid \mathbf{bool} \mid \mathbf{ref}\; \tau & \\
& \mid & \mathbf{rec} \; X. \tau \mid X \mid \left[{\mathit{label}_i:\mathit{\tau}_i}^{i \in \{ 1 \dots n \}}\right] & \\
\\
\mathit{predicate\_definition} & ::= & \mathbf{let}\; \mathbf{rec} \; x \; \overline{typed\_id} =  \mathit{assertion} & \mbox{(Predicate Definition)}\\
\\
\mathit{typed\_id} & ::= & x:\tau & \mbox{(Typed Identifier)}\\
\end{array}$$
$$

---

<img src="img/fctunl.png" alt="FCT">
<img src="img/logo_novalincs.png" alt="NOVA-LINCS">

[prototype]: prototype/improve.tar.gz
