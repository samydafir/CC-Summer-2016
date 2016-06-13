Copyright (c) 2015-2016, the Selfie Project authors. All rights reserved. Please see the AUTHORS file for details. Use of this source code is governed by a BSD license that can be found in the LICENSE file.

Selfie is a project of the Computational Systems Group at the Department of Computer Sciences of the University of Salzburg in Austria. For further information and code please refer to:

http://selfie.cs.uni-salzburg.at

This is the grammar of the C Star (C*) programming language.

C* is a small Turing-complete subset of C that includes dereferencing (the * operator) but excludes data structures, bitwise and Boolean operators, and many other features. C* is supposed to be close to the minimum necessary for implementing a self-compiling, single-pass, recursive-descent compiler.

Keywords: int, while, if, else, return, void

```
digit            = "0" | ... | "9" .

integer          = digit { digit } .

letter           = "a" | ... | "z" | "A" | ... | "Z" .

identifier       = letter { letter | digit | "_" } .

type             = "int" [ "*" ] .

cast             = "(" type ")" .

call             = identifier "(" [orExpression { ","orExpression } ] ")" .

literal          = integer | "'" ascii_character "'" .

factor           =   struct_acc |
                    ( identifier "["orExpression "]" ) |
                    ( [ cast ]
                    ( [ "*" ] ( identifier | "("orExpression ")" ) |
                      call |
                      literal |
                      """ { ascii_character } """ ) ) .

term             = factor { ( "*" | "/" | "%" ) factor } .

simpleExpression = [ "-" ] term { ( "+" | "-" ) term } .

shiftExpression = simpleExpression { (  "<<" | ">>" ) simpleExression } .

expression       = shiftExpression [ ( "==" | "!=" | "<" | ">" | "<=" | ">=" ) shiftExpression ] .

orExpression     = andExpression { || andExpression } .

andExpression    = expression { && expression } .

while            = "while" "("orExpression ")"
                             ( statement |
                               "{" { statement } "}" ) .

if               = "if" "("orExpression ")"
                             ( statement |
                               "{" { statement } "}" )
                         [ "else"
                             ( statement |
                               "{" { statement } "}" ) ] .

return           = "return" [orExpression ] .

statement        =  ( struct_acc |
                    [ "*" ] identifier | "*" "("orExpression ")" | identifier "["orExpression "]") "="
                     orExpression ";" |
                    call ";" |
                    while |
                    if |
                    return ";" .

variable         = type identifier [ "["orExpression "]" ] .

struct_def       = "struct" identifier  "{" { variable | struct_dec } "}" ";" .

struct_dec       = "struct" identifier "*" identifier ";" .

struct_acc       = identifier "->" identifier .

procedure        =  struct_dec | struct_def | "(" [ variable { "," variable } ] ")"
                    ( ";" | "{" { variable ";" } { statement } "}" ) .

cstar            =  struct_dec | struct_def | type identifier "["orExpression "]" ";" |
                    { type identifier [ "=" [ cast ] [ "-" ] literal ] ";" |
                    ( "void" | type ) identifier procedure } .
```
