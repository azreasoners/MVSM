# MVSM
Computing Stable Models of Multi-Valued Propositional Formulas using Propositional Answer Set Solvers

## Introduction
System mvsm is a prototype implementation multi-valued propositional formulas under the stable model semantics computed by grounder and solver gringo and claspD. This reduction is based on the intensional function elimination theorem in Bartholomew & Lee 2012. The system is a toolchain that includes mvsm-compiler, f2lp, gringo, and claspD, as2transition.

The implementation first compiles the multi-valued formula into a propositional formula. F2lp is used to turn this propositional formula into a logic program. Gringo and claspD are then used to ground the logic program and find the stable models of the program. Finally, as2transition syntactically converts propositional atoms back into multi-valued atoms.

## Tutorial
An input encoding consists of a declaration section and a set of multi-valued propositional rules. 
The declaration includes sort declarations, object declarations, constant declarations, and variable declarations. 
These define the names of the user-defined sorts, the elements in the sorts, the argument and value sorts for constants, 
and the sorts of variables respectively. See the examples for details on syntax. The multi-valued propositional rules 
are formed using the constants, variables, objects, and propositional connectives, which include & (conjunction), 
| (disjunction), -> (implication), <- (reverse direction implication), not (negation). See the examples for more detailed usage. 

Typical invocation of the toolchain script will be aspmt2smt where is the name of the input file, are of the form "-c =". 
See the example outputs for specific examples of the invocations. Versions used for external tools in the toolchain: f2lp: v1.3 gringo: v3.0.4 claspD: 1.1.1

## Example Usage
### MVSM Declarations
The tutorial describes the leaking bucket example. Consider a leaking bucket with maximum capacity c that loses one unit of water every time step by default. The bucket can be refilled to its maximum capacity by the action fill. The initial capacity is 5 and the desired capacity is 10. Here, the argument variable corresponding to the length of the plan increases so both systems suffer scalability issues.

Declarations in MVSM are used to specify the valid ranges of values for arguments of functions and for functions themselves. In addition, they allow specification of user-defined sorts. They also specify the sorts of variables (if a variable is not declared it is understood to be a numeric variable). To declare two user defined sorts "atime" and "time", we write
```
:-sorts
  atime;time.
```
To specify that "time" ranges over the number range 0..10, "atime" ranges of the number range 0..9, and "amount" ranges over the number range 0..10, we write
```
:-objects
  0..10 :: time;
  0..9  :: atime;
  0..10 :: amount.
```
To declare "amt" as a function from "time" to the sort "amount", and "fill" as a function from "atime" to boolean values, we write
```
:-constants
  amt(time) :: amount;
  fill(atime) :: boolean.
```
To declare T as a variable of sort "time", ST as a variable of sort "atime", and X as a variable of sort "amount", we write
```
:-variables
  T :: time;
  ST :: atime;
  X :: amount.
```

### MVSM Formulas
The multi-valued propositional rules are formed using the constants, variables, objects, and propositional connectives, which include & (conjunction), | (disjunction), -> (implication), <- (reverse direction implication), not (negation). Below is an annotated example.
```
%By default, the amount at the next timestep is 1 less than the amount at this timestep
{amt(ST+1) = X-1} <- amt(ST) = X.

%The fill action is exogenous at all timesteps (other than the final timestep, when actions are not executed).
{fill(ST) = true}.
{fill(ST) = false}.

%If the fill action is executed at the current timestep, then the amount at the next timestep is 10.
amt(ST+1) = X <- fill(ST) = true & X = 10.

%Do not allow the amount to become less than 2 at any time.
<- amt(T) = X & X < 2.

%Initially, the amount is 5.
amt(0) = 5.

%A plan should result in the amount being 8 at time 10.
<- not amt(10) = 8.
```

### Running MVSM
Typical invocation of the toolchain script will be mvsm [FILENAME] [CONSTANTS] [NUMBER OF SOLUTIONS] where [FILENAME] is the name of the input file, [CONSTANTS] are of the form "-c [CONST]=[VALUE]". If the number of solutions is omitted, the default value 1 is used. To show all solutions, specify 0 for [NUMBER OF SOLUTIONS]. For example, to obtain one stable model of the leaking bucket example, we write
```
mvsm amt.mvsm
```
