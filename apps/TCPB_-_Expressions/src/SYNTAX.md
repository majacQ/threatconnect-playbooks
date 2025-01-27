
# EBNF-Syntax

    The grammar for the expressions is below.  Production rules are prefixed by -> and are used
    to tell the parser what to do when that construct is identified.

    start:  eval

    eval: sum
        | eval "||" sum -> logical_or
        | eval "&&" sum -> logical_and
        | eval "or" sum -> logical_or
        | eval "and" sum -> logical_and
        | eval "==" sum -> equals
        | eval "!=" sum -> not_equals
        | eval "<" sum -> less_than
        | eval ">" sum -> greater_than
        | eval "<=" sum -> less_than_equal_to
        | eval ">=" sum -> greater_than_equal_to
        | "not" eval -> not_
        | eval "in" product -> in_
        | eval "not" "in" product -> not_in_

    sum: product
        | sum "+" product -> add
        | sum "-" product -> sub

    product: raise
        | product "*" raise -> mul
        | product "/" raise -> div
        | product "%" raise -> mod

    raise: atom
        | raise "**" atom -> pow

    atom: FLOAT    -> num_float
        | INT       -> num_int
        | "-" atom  -> neg
        | NAME      -> var
        | string
        | "(" eval_list ")" -> tuple_freeze
        | "[" eval_list "]" -> list_freeze
        | "{" dict_list "}" -> dict_freeze
        | NAME "(" arg_list ")" -> function
        | atom "[" atom "]" -> get
        | atom "[" optional_atom ":" optional_atom "]" -> get_slice
        | atom "." NAME -> getattr

    string: STRING     -> literal_
        | TCVARIABLE    -> tcvariable
        | SQUOTE_STRING -> literal_
        | string string -> concat_string

    dict_list: dict_assign         -> list_
        | dict_list "," dict_assign -> list_
        |                           -> list_

    dict_assign: eval ":" eval -> set_kwarg

    eval_list: eval
        | eval_list "," eval -> list_
        | eval_list ","      -> list_
        |                    -> list_

    arg: eval
        | NAME "=" eval -> set_kwarg

    arg_list: arg
        | arg_list "," arg  -> list_
        | arg_list ","      -> list_
        |                   -> list_

    optional_atom:  atom
        | -> none

    TCVARIABLE: /#[A-Za-z]+:\d+:[A-Za-z0-9_.]+!\w+/
    _STRING_INNER: /.*?/
    _STRING_ESC_INNER: _STRING_INNER /(?<!\\)(\\\\)*?/
    SQUOTE_STRING: "'" _STRING_ESC_INNER "'"
