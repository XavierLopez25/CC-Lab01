# Lab 01 - Introducción a ANTLR

Video de YouTube: https://youtu.be/teRFn461F40

### A) Estructura de un archivo `.g4`

1. **Cabecera (opcional)**  
   - `options { … }`, `@header { … }`, etc.  
   - Acá se podrían agregar imports de Java/Python, modos de generación, etc.

2. **Reglas de parser (parser rules)**  
   - Se definen con nombre en minúscula:  
     ```antlr
     prog: stat+ ;
     stat
       : expr NEWLINE                 # printExpr
       | ID '=' expr NEWLINE          # assign
       | NEWLINE                      # blank
       ;
     ```  
   - Cada alternativa puede llevar un **label** con `#Etiqueta`, lo que genera métodos específicos en el listener/visitor (por ejemplo `visitPrintExpr`, `visitAssign`, …).

3. **Reglas de lexer (lexer rules)**  
   - Nombres en mayúscula:  
     ```antlr
     MUL     : '*' ;
     DIV     : '/' ;
     ADD     : '+' ;
     SUB     : '-' ;
     ID      : [a-zA-Z]+ ;
     INT     : [0-9]+ ;
     NEWLINE : '\r'? '\n' ;
     WS      : [ \t]+ -> skip ;
     ```  
   - El sufijo `-> skip` hace que esos tokens (espacios/tabs) no pasen al parser.  

4. **Qué hace cada parte**  
   - `prog` es la regla inicial (entry point).  
   - `stat` agrupa tres tipos de sentencias: impresión (`printExpr`), asignación (`assign`) y líneas en blanco (`blank`).  
   - `expr` es recursiva para manejar suma, resta, multiplicación, división, paréntesis, enteros e identificadores.  

### B) Desglose de `Driver.py`

```python
import sys
from antlr4 import *
from MiniLangLexer import MiniLangLexer
from MiniLangParser import MiniLangParser

def main(argv):
    # 1) Cargamos el archivo de texto
    input_stream = FileStream(argv[1])
    # 2) Creamos el lexer y generamos tokens
    lexer = MiniLangLexer(input_stream)
    stream = CommonTokenStream(lexer)
    # 3) Creamos el parser y construimos el árbol sintáctico
    parser = MiniLangParser(stream)
    tree = parser.prog()   # Llama a la regla inicial `prog`
    # Acá se podría luego hacer un listener o un visitor para 'tree'

if __name__ == '__main__':
    main(sys.argv)
```
