# More on Conditions
#python 

Conditions in clauses such as `if` and `while` can contain any operators, not just comparisons:
- `in` and `not in`
- `is` and `not is`: Compare equivalence of two objects.
- Boolean operators: `and`, `or`, `not`. In terms of priorities: `not` > `and` > `or`.
  `and` and `or` are called *short-circuit* operators, i.e., their arguments are evaluated from left to right and evaluation stops as soon as the outcome is determined. This can be used to assign the last  evaluated argument.