# MVSM
Computing Stable Models of Multi-Valued Propositional Formulas using Propositional Answer Set Solvers

System mvsm is a prototype implementation multi-valued propositional formulas under the stable model semantics computed by grounder and solver gringo and claspD. This reduction is based on the intensional function elimination theorem in Bartholomew & Lee 2012. The system is a toolchain that includes mvsm-compiler, f2lp, gringo, and claspD, as2transition.

The implementation first compiles the multi-valued formula into a propositional formula. F2lp is used to turn this propositional formula into a logic program. Gringo and claspD are then used to ground the logic program and find the stable models of the program. Finally, as2transition syntactically converts propositional atoms back into multi-valued atoms.
