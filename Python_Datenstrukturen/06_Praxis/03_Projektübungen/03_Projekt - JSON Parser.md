---
tags: [python, datenstrukturen, praxis, projekt, parsing]
created: 2025-10-02
---

← [[06_Praxis/03_Projektübungen/02_Projekt - Cache Implementation|Projekt - Cache Implementation]] | [[INDEX|📑 Index]] | [[06_Praxis/03_Projektübungen/04_Projekt - Datenbankmodell|Projekt - Datenbankmodell]] →

# Projekt: JSON Parser

> [!tip] Projektziel
> Baue einen vollständigen JSON-Parser von Grund auf, der JSON-Strings in Python-Datenstrukturen umwandelt und validiert

## Projektübersicht

In diesem Projekt entwickelst du einen **JSON Parser und Serializer**, der:
- JSON-Strings parst (ohne `json` Modul!)
- Python-Objekte in JSON serialisiert
- Syntax-Validierung durchführt
- Detaillierte Fehlermeldungen liefert
- Performance-optimiert ist

**Geschätzte Bearbeitungszeit:** 3-4 Stunden

## Anforderungen

### Funktionale Anforderungen

1. **Parsing**
   - Alle JSON-Typen: null, bool, number, string, array, object
   - Escaped Characters in Strings
   - Whitespace-Handling
   - Unicode-Support

2. **Serialisierung**
   - Python → JSON Konvertierung
   - Pretty-Print Option
   - Custom Encoder für eigene Klassen

3. **Validierung**
   - Syntax-Checks
   - Detaillierte Fehler mit Position
   - Trailing Comma Detection

### Technische Anforderungen

- **KEIN** `json` Modul verwenden!
- Verwende Stack für verschachtelte Strukturen
- Type Hints und Docstrings
- Umfassende Fehlerbehandlung

## JSON Syntax-Übersicht

```
JSON        ::= value
value       ::= object | array | string | number | "true" | "false" | "null"
object      ::= '{' [ pair (',' pair)* ] '}'
pair        ::= string ':' value
array       ::= '[' [ value (',' value)* ] ']'
string      ::= '"' chars '"'
number      ::= ['-'] int ['.' digits] [('e'|'E') ['+' | '-'] digits]
```

## Aufgabe 1: Tokenizer (Mittel)

Erstelle einen Tokenizer der JSON in Tokens zerlegt.

```python
from enum import Enum, auto
from dataclasses import dataclass
from typing import List, Optional, Any

class TokenType(Enum):
    """JSON Token-Typen"""
    # Literale
    STRING = auto()
    NUMBER = auto()
    TRUE = auto()
    FALSE = auto()
    NULL = auto()
    
    # Strukturen
    LEFT_BRACE = auto()      # {
    RIGHT_BRACE = auto()     # }
    LEFT_BRACKET = auto()    # [
    RIGHT_BRACKET = auto()   # ]
    COLON = auto()           # :
    COMMA = auto()           # ,
    
    # Meta
    EOF = auto()             # End of file

@dataclass
class Token:
    """Repräsentiert ein Token"""
    type: TokenType
    value: Any
    position: int  # Position im Input-String
    line: int
    column: int
    
    def __repr__(self) -> str:
        return f"Token({self.type.name}, {self.value!r}, L{self.line}:C{self.column})"

class JSONLexerError(Exception):
    """Fehler beim Tokenisieren"""
    pass

class JSONLexer:
    """Tokenisiert JSON-Strings"""
    
    def __init__(self, text: str):
        self.text = text
        self.pos = 0
        self.line = 1
        self.column = 1
        self.tokens: List[Token] = []
    
    def current_char(self) -> Optional[str]:
        """Gibt aktuelles Zeichen zurück"""
        if self.pos >= len(self.text):
            return None
        return self.text[self.pos]
    
    def peek_char(self, offset: int = 1) -> Optional[str]:
        """Schaut voraus ohne Position zu ändern"""
        pos = self.pos + offset
        if pos >= len(self.text):
            return None
        return self.text[pos]
    
    def advance(self) -> None:
        """Bewegt Position vorwärts"""
        if self.pos < len(self.text):
            if self.text[self.pos] == '\n':
                self.line += 1
                self.column = 1
            else:
                self.column += 1
            self.pos += 1
    
    def skip_whitespace(self) -> None:
        """Überspringt Whitespace"""
        while self.current_char() in ' \t\n\r':
            self.advance()
    
    def read_string(self) -> str:
        """
        Liest einen String-Token
        
        Muss Escape-Sequenzen behandeln:
        - \" \\ \/ \b \f \n \r \t
        - \uXXXX (Unicode)
        """
        result = []
        self.advance()  # Überspringe öffnendes "
        
        while self.current_char() is not None and self.current_char() != '"':
            char = self.current_char()
            
            if char == '\\':
                # Escape-Sequenz
                self.advance()
                escaped = self.current_char()
                
                if escaped is None:
                    raise JSONLexerError(f"Unterminated string at L{self.line}:C{self.column}")
                
                # TODO: Implementiere Escape-Sequenzen
                # Standard escapes: " \ / b f n r t
                # Unicode: \uXXXX
                
                escape_map = {
                    '"': '"',
                    '\\': '\\',
                    '/': '/',
                    'b': '\b',
                    'f': '\f',
                    'n': '\n',
                    'r': '\r',
                    't': '\t',
                }
                
                if escaped in escape_map:
                    result.append(escape_map[escaped])
                elif escaped == 'u':
                    # TODO: Unicode escape \uXXXX
                    # Lese 4 Hex-Ziffern und konvertiere zu char
                    pass
                else:
                    raise JSONLexerError(
                        f"Invalid escape sequence '\\{escaped}' at L{self.line}:C{self.column}"
                    )
            else:
                result.append(char)
            
            self.advance()
        
        if self.current_char() != '"':
            raise JSONLexerError(f"Unterminated string at L{self.line}:C{self.column}")
        
        self.advance()  # Überspringe schließendes "
        return ''.join(result)
    
    def read_number(self) -> float:
        """
        Liest eine Zahl
        
        Format: ['-'] int ['.' digits] [('e'|'E') ['+' | '-'] digits]
        """
        start_pos = self.pos
        
        # Optional: Minus
        if self.current_char() == '-':
            self.advance()
        
        # Integer-Teil
        if self.current_char() == '0':
            self.advance()
            # Nach '0' darf keine weitere Ziffer folgen (außer '.')
            if self.current_char() and self.current_char().isdigit():
                raise JSONLexerError(
                    f"Leading zeros not allowed at L{self.line}:C{self.column}"
                )
        elif self.current_char() and self.current_char().isdigit():
            while self.current_char() and self.current_char().isdigit():
                self.advance()
        else:
            raise JSONLexerError(f"Invalid number at L{self.line}:C{self.column}")
        
        # Optional: Dezimalteil
        if self.current_char() == '.':
            self.advance()
            if not (self.current_char() and self.current_char().isdigit()):
                raise JSONLexerError(
                    f"Digit expected after decimal point at L{self.line}:C{self.column}"
                )
            while self.current_char() and self.current_char().isdigit():
                self.advance()
        
        # Optional: Exponent
        if self.current_char() in 'eE':
            self.advance()
            if self.current_char() in '+-':
                self.advance()
            if not (self.current_char() and self.current_char().isdigit()):
                raise JSONLexerError(
                    f"Digit expected in exponent at L{self.line}:C{self.column}"
                )
            while self.current_char() and self.current_char().isdigit():
                self.advance()
        
        number_str = self.text[start_pos:self.pos]
        
        # Konvertiere zu int oder float
        if '.' in number_str or 'e' in number_str or 'E' in number_str:
            return float(number_str)
        else:
            return int(number_str)
    
    def read_keyword(self, keyword: str) -> bool:
        """Liest und validiert ein Keyword (true, false, null)"""
        # TODO: Implementieren
        # Prüfe ob text[pos:pos+len(keyword)] == keyword
        pass
    
    def tokenize(self) -> List[Token]:
        """
        Tokenisiert den kompletten Input
        
        Returns:
            Liste von Tokens inkl. EOF-Token
        """
        tokens = []
        
        while self.current_char() is not None:
            self.skip_whitespace()
            
            if self.current_char() is None:
                break
            
            # Speichere Position für Token
            token_line = self.line
            token_col = self.column
            token_pos = self.pos
            
            char = self.current_char()
            
            # Strukturzeichen
            if char == '{':
                tokens.append(Token(TokenType.LEFT_BRACE, '{', token_pos, token_line, token_col))
                self.advance()
            elif char == '}':
                tokens.append(Token(TokenType.RIGHT_BRACE, '}', token_pos, token_line, token_col))
                self.advance()
            elif char == '[':
                tokens.append(Token(TokenType.LEFT_BRACKET, '[', token_pos, token_line, token_col))
                self.advance()
            elif char == ']':
                tokens.append(Token(TokenType.RIGHT_BRACKET, ']', token_pos, token_line, token_col))
                self.advance()
            elif char == ':':
                tokens.append(Token(TokenType.COLON, ':', token_pos, token_line, token_col))
                self.advance()
            elif char == ',':
                tokens.append(Token(TokenType.COMMA, ',', token_pos, token_line, token_col))
                self.advance()
            
            # String
            elif char == '"':
                value = self.read_string()
                tokens.append(Token(TokenType.STRING, value, token_pos, token_line, token_col))
            
            # Number
            elif char == '-' or char.isdigit():
                value = self.read_number()
                tokens.append(Token(TokenType.NUMBER, value, token_pos, token_line, token_col))
            
            # Keywords
            elif char == 't':
                if self.read_keyword('true'):
                    tokens.append(Token(TokenType.TRUE, True, token_pos, token_line, token_col))
                else:
                    raise JSONLexerError(f"Invalid keyword at L{token_line}:C{token_col}")
            elif char == 'f':
                if self.read_keyword('false'):
                    tokens.append(Token(TokenType.FALSE, False, token_pos, token_line, token_col))
                else:
                    raise JSONLexerError(f"Invalid keyword at L{token_line}:C{token_col}")
            elif char == 'n':
                if self.read_keyword('null'):
                    tokens.append(Token(TokenType.NULL, None, token_pos, token_line, token_col))
                else:
                    raise JSONLexerError(f"Invalid keyword at L{token_line}:C{token_col}")
            
            else:
                raise JSONLexerError(
                    f"Unexpected character '{char}' at L{token_line}:C{token_col}"
                )
        
        # EOF-Token hinzufügen
        tokens.append(Token(TokenType.EOF, None, self.pos, self.line, self.column))
        return tokens
```

**Erwartetes Verhalten:**
```python
lexer = JSONLexer('{"name": "Alice", "age": 30}')
tokens = lexer.tokenize()

for token in tokens:
    print(token)

# Output:
# Token(LEFT_BRACE, '{', L1:C1)
# Token(STRING, 'name', L1:C2)
# Token(COLON, ':', L1:C8)
# Token(STRING, 'Alice', L1:C10)
# Token(COMMA, ',', L1:C17)
# Token(STRING, 'age', L1:C19)
# Token(COLON, ':', L1:C24)
# Token(NUMBER, 30, L1:C26)
# Token(RIGHT_BRACE, '}', L1:C28)
# Token(EOF, None, L1:C29)
```

## Aufgabe 2: Parser (Schwer)

Implementiere den rekursiven Parser.

```python
from typing import Any, List, Dict, Union

JSONValue = Union[None, bool, int, float, str, List[Any], Dict[str, Any]]

class JSONParseError(Exception):
    """Fehler beim Parsen"""
    pass

class JSONParser:
    """Parst Token-Stream zu Python-Objekten"""
    
    def __init__(self, tokens: List[Token]):
        self.tokens = tokens
        self.pos = 0
    
    def current_token(self) -> Token:
        """Gibt aktuelles Token zurück"""
        if self.pos >= len(self.tokens):
            return self.tokens[-1]  # EOF
        return self.tokens[self.pos]
    
    def advance(self) -> Token:
        """Bewegt Position vorwärts und gibt altes Token zurück"""
        token = self.current_token()
        if self.pos < len(self.tokens) - 1:
            self.pos += 1
        return token
    
    def expect(self, token_type: TokenType) -> Token:
        """
        Erwartet bestimmten Token-Typ
        
        Raises:
            JSONParseError wenn Token-Typ nicht passt
        """
        token = self.current_token()
        if token.type != token_type:
            raise JSONParseError(
                f"Expected {token_type.name}, got {token.type.name} "
                f"at L{token.line}:C{token.column}"
            )
        return self.advance()
    
    def parse(self) -> JSONValue:
        """
        Parst kompletten Token-Stream
        
        Returns:
            Python-Objekt (dict, list, str, int, float, bool, None)
        """
        value = self.parse_value()
        
        # Prüfe auf trailing content
        if self.current_token().type != TokenType.EOF:
            token = self.current_token()
            raise JSONParseError(
                f"Unexpected content after JSON at L{token.line}:C{token.column}"
            )
        
        return value
    
    def parse_value(self) -> JSONValue:
        """Parst einen JSON-Wert"""
        token = self.current_token()
        
        if token.type == TokenType.STRING:
            self.advance()
            return token.value
        
        elif token.type == TokenType.NUMBER:
            self.advance()
            return token.value
        
        elif token.type == TokenType.TRUE:
            self.advance()
            return True
        
        elif token.type == TokenType.FALSE:
            self.advance()
            return False
        
        elif token.type == TokenType.NULL:
            self.advance()
            return None
        
        elif token.type == TokenType.LEFT_BRACKET:
            return self.parse_array()
        
        elif token.type == TokenType.LEFT_BRACE:
            return self.parse_object()
        
        else:
            raise JSONParseError(
                f"Unexpected token {token.type.name} at L{token.line}:C{token.column}"
            )
    
    def parse_array(self) -> List[JSONValue]:
        """
        Parst ein JSON-Array
        
        Format: '[' [ value (',' value)* ] ']'
        """
        self.expect(TokenType.LEFT_BRACKET)
        
        values = []
        
        # Leeres Array
        if self.current_token().type == TokenType.RIGHT_BRACKET:
            self.advance()
            return values
        
        # Erstes Element
        values.append(self.parse_value())
        
        # Weitere Elemente
        while self.current_token().type == TokenType.COMMA:
            self.advance()  # Überspringe Comma
            
            # Prüfe auf trailing comma
            if self.current_token().type == TokenType.RIGHT_BRACKET:
                token = self.current_token()
                raise JSONParseError(
                    f"Trailing comma not allowed at L{token.line}:C{token.column}"
                )
            
            values.append(self.parse_value())
        
        self.expect(TokenType.RIGHT_BRACKET)
        return values
    
    def parse_object(self) -> Dict[str, JSONValue]:
        """
        Parst ein JSON-Objekt
        
        Format: '{' [ pair (',' pair)* ] '}'
        pair   : string ':' value
        """
        # TODO: Implementieren
        # Ähnlich zu parse_array, aber mit Key:Value-Paaren
        pass
```

**Erwartetes Verhalten:**
```python
json_str = '''
{
    "name": "Alice",
    "age": 30,
    "active": true,
    "score": null,
    "tags": ["python", "json"],
    "address": {
        "city": "Berlin",
        "zip": 10115
    }
}
'''

lexer = JSONLexer(json_str)
tokens = lexer.tokenize()
parser = JSONParser(tokens)
result = parser.parse()

print(result)
# {'name': 'Alice', 'age': 30, 'active': True, 'score': None, 
#  'tags': ['python', 'json'], 'address': {'city': 'Berlin', 'zip': 10115}}
```

## Aufgabe 3: JSON Serializer (Mittel)

Implementiere Python → JSON Konvertierung.

```python
from typing import Any, TextIO
from decimal import Decimal

class JSONEncoder:
    """Konvertiert Python-Objekte zu JSON-Strings"""
    
    def __init__(self, indent: Optional[int] = None, 
                 ensure_ascii: bool = True):
        """
        Args:
            indent: Anzahl Spaces für Pretty-Print (None = kompakt)
            ensure_ascii: Wenn True, escape non-ASCII characters
        """
        self.indent = indent
        self.ensure_ascii = ensure_ascii
        self._current_indent = 0
    
    def encode(self, obj: Any) -> str:
        """Konvertiert Python-Objekt zu JSON-String"""
        return self._encode_value(obj)
    
    def _encode_value(self, obj: Any) -> str:
        """Konvertiert Wert rekursiv"""
        
        if obj is None:
            return 'null'
        
        elif isinstance(obj, bool):
            return 'true' if obj else 'false'
        
        elif isinstance(obj, (int, float)):
            # Spezialfall: Infinity und NaN sind nicht erlaubt in JSON
            if isinstance(obj, float):
                if obj != obj:  # NaN check
                    raise ValueError("NaN is not allowed in JSON")
                if obj == float('inf') or obj == float('-inf'):
                    raise ValueError("Infinity is not allowed in JSON")
            return str(obj)
        
        elif isinstance(obj, Decimal):
            return str(obj)
        
        elif isinstance(obj, str):
            return self._encode_string(obj)
        
        elif isinstance(obj, (list, tuple)):
            return self._encode_array(obj)
        
        elif isinstance(obj, dict):
            return self._encode_object(obj)
        
        else:
            # Versuche __dict__ für Custom Objects
            if hasattr(obj, '__dict__'):
                return self._encode_object(obj.__dict__)
            raise TypeError(f"Object of type {type(obj).__name__} is not JSON serializable")
    
    def _encode_string(self, s: str) -> str:
        """
        Konvertiert String mit Escape-Sequenzen
        
        Muss escapen: " \ / \b \f \n \r \t
        Optional: Non-ASCII → \uXXXX
        """
        result = ['"']
        
        for char in s:
            if char == '"':
                result.append('\\"')
            elif char == '\\':
                result.append('\\\\')
            elif char == '/':
                result.append('\\/')
            elif char == '\b':
                result.append('\\b')
            elif char == '\f':
                result.append('\\f')
            elif char == '\n':
                result.append('\\n')
            elif char == '\r':
                result.append('\\r')
            elif char == '\t':
                result.append('\\t')
            elif self.ensure_ascii and ord(char) > 127:
                # Unicode escape
                result.append(f'\\u{ord(char):04x}')
            else:
                result.append(char)
        
        result.append('"')
        return ''.join(result)
    
    def _encode_array(self, arr: List[Any]) -> str:
        """Konvertiert Liste/Tuple zu JSON-Array"""
        if not arr:
            return '[]'
        
        if self.indent is None:
            # Kompakte Ausgabe
            items = [self._encode_value(item) for item in arr]
            return '[' + ', '.join(items) + ']'
        else:
            # Pretty-Print
            self._current_indent += self.indent
            items = []
            for item in arr:
                indent_str = ' ' * self._current_indent
                items.append(f"{indent_str}{self._encode_value(item)}")
            
            self._current_indent -= self.indent
            
            return '[\n' + ',\n'.join(items) + '\n' + ' ' * self._current_indent + ']'
    
    def _encode_object(self, obj: Dict[str, Any]) -> str:
        """Konvertiert Dict zu JSON-Objekt"""
        # TODO: Implementieren
        # Ähnlich zu _encode_array
        # Keys müssen Strings sein!
        pass
```

**Erwartete Verwendung:**
```python
data = {
    'name': 'Alice',
    'age': 30,
    'tags': ['python', 'json'],
    'address': {'city': 'Berlin'}
}

# Kompakt
encoder = JSONEncoder()
print(encoder.encode(data))
# {"name": "Alice", "age": 30, "tags": ["python", "json"], "address": {"city": "Berlin"}}

# Pretty-Print
encoder = JSONEncoder(indent=2)
print(encoder.encode(data))
# {
#   "name": "Alice",
#   "age": 30,
#   "tags": [
#     "python",
#     "json"
#   ],
#   "address": {
#     "city": "Berlin"
#   }
# }
```

## Aufgabe 4: Hauptfunktionen (Leicht)

Erstelle die High-Level API.

```python
def loads(s: str) -> JSONValue:
    """
    Parst JSON-String zu Python-Objekt
    
    Args:
        s: JSON-String
        
    Returns:
        Python-Objekt
        
    Raises:
        JSONLexerError: Bei Tokenisierungs-Fehlern
        JSONParseError: Bei Syntax-Fehlern
    """
    lexer = JSONLexer(s)
    tokens = lexer.tokenize()
    parser = JSONParser(tokens)
    return parser.parse()

def dumps(obj: Any, indent: Optional[int] = None, 
          ensure_ascii: bool = True) -> str:
    """
    Konvertiert Python-Objekt zu JSON-String
    
    Args:
        obj: Python-Objekt
        indent: Anzahl Spaces für Pretty-Print
        ensure_ascii: Escape non-ASCII characters
        
    Returns:
        JSON-String
    """
    encoder = JSONEncoder(indent=indent, ensure_ascii=ensure_ascii)
    return encoder.encode(obj)

def load(fp: TextIO) -> JSONValue:
    """Liest JSON von Datei"""
    return loads(fp.read())

def dump(obj: Any, fp: TextIO, indent: Optional[int] = None) -> None:
    """Schreibt JSON in Datei"""
    fp.write(dumps(obj, indent=indent))
```

## Aufgabe 5: Validator (Mittel)

Erstelle einen JSON-Validator mit detaillierten Fehlern.

```python
from typing import List, Tuple

@dataclass
class ValidationError:
    """Repräsentiert einen Validierungs-Fehler"""
    message: str
    line: int
    column: int
    position: int
    
    def __str__(self) -> str:
        return f"Error at L{self.line}:C{self.column}: {self.message}"

class JSONValidator:
    """Validiert JSON mit detaillierten Fehlerberichten"""
    
    def __init__(self, strict: bool = True):
        """
        Args:
            strict: Wenn True, erlaube keine Extensions (z.B. trailing commas)
        """
        self.strict = strict
        self.errors: List[ValidationError] = []
    
    def validate(self, text: str) -> Tuple[bool, List[ValidationError]]:
        """
        Validiert JSON-String
        
        Returns:
            (ist_valide, liste_von_fehlern)
        """
        self.errors = []
        
        try:
            # Versuch zu parsen
            lexer = JSONLexer(text)
            tokens = lexer.tokenize()
            parser = JSONParser(tokens)
            parser.parse()
            return True, []
        
        except (JSONLexerError, JSONParseError) as e:
            # Extrahiere Position aus Fehlermeldung wenn möglich
            # TODO: Verbessern mit Token-Info
            self.errors.append(ValidationError(
                message=str(e),
                line=0,
                column=0,
                position=0
            ))
            return False, self.errors
    
    def validate_with_suggestions(self, text: str) -> Dict[str, Any]:
        """
        Validiert und gibt Verbesserungsvorschläge
        
        Returns:
            Dict mit 'valid', 'errors', 'suggestions'
        """
        # TODO: Implementieren
        # Erkenne häufige Fehler:
        # - Trailing commas
        # - Single quotes statt double quotes
        # - Fehlende Klammern
        # - etc.
        pass
```

**Erwartete Verwendung:**
```python
validator = JSONValidator()

# Valides JSON
valid, errors = validator.validate('{"name": "Alice"}')
print(valid)  # True

# Invalides JSON
valid, errors = validator.validate('{"name": "Alice",}')  # trailing comma
print(valid)  # False
for error in errors:
    print(error)
```

## Aufgabe 6: Performance-Optimierungen (Schwer)

Optimiere den Parser für große Dateien.

```python
class StreamingJSONParser:
    """Parser für sehr große JSON-Dateien"""
    
    def __init__(self, file_path: Path, chunk_size: int = 8192):
        """
        Args:
            file_path: Pfad zur JSON-Datei
            chunk_size: Bytes pro Lese-Chunk
        """
        self.file_path = file_path
        self.chunk_size = chunk_size
    
    def parse_array_items(self):
        """
        Generator der Array-Items einzeln liefert
        
        Nützlich für große Arrays: Nicht alles auf einmal in Memory
        
        Yields:
            Einzelne Array-Elemente
        """
        # TODO: Implementieren
        # Öffne Datei, lese chunk-weise
        # Parse Items on-the-fly
        pass
    
    def parse_object_items(self):
        """Generator für Objekt Key-Value-Paare"""
        # TODO: Implementieren
        pass
```

## Aufgabe 7: Vollständiges System (Schwer)

Kombiniere alle Komponenten.

```python
from pathlib import Path
from typing import Optional
import time

class JSONProcessor:
    """High-Level JSON-Verarbeitungs-System"""
    
    @staticmethod
    def parse_file(filepath: Path, 
                   validate: bool = True,
                   verbose: bool = False) -> Optional[JSONValue]:
        """
        Parst JSON-Datei mit Fehlerbehandlung
        
        Args:
            filepath: Pfad zur JSON-Datei
            validate: Führe Validierung durch
            verbose: Zeige Parsing-Informationen
            
        Returns:
            Geparste Daten oder None bei Fehler
        """
        if verbose:
            print(f"Parsing {filepath}...")
            start_time = time.time()
        
        try:
            text = filepath.read_text(encoding='utf-8')
            
            if validate:
                validator = JSONValidator()
                valid, errors = validator.validate(text)
                if not valid:
                    print("Validation errors:")
                    for error in errors:
                        print(f"  - {error}")
                    return None
            
            result = loads(text)
            
            if verbose:
                duration = time.time() - start_time
                print(f"✓ Parsed in {duration:.4f}s")
                print(f"  Type: {type(result).__name__}")
                if isinstance(result, (list, dict)):
                    print(f"  Size: {len(result)} items")
            
            return result
            
        except Exception as e:
            print(f"Error parsing {filepath}: {e}")
            return None
    
    @staticmethod
    def format_file(input_path: Path, 
                   output_path: Optional[Path] = None,
                   indent: int = 2) -> bool:
        """
        Formatiert JSON-Datei (Pretty-Print)
        
        Args:
            input_path: Input-Datei
            output_path: Output-Datei (None = überschreibe Input)
            indent: Anzahl Spaces
            
        Returns:
            True bei Erfolg
        """
        try:
            data = loads(input_path.read_text())
            formatted = dumps(data, indent=indent)
            
            out_path = output_path or input_path
            out_path.write_text(formatted)
            
            return True
        except Exception as e:
            print(f"Error formatting: {e}")
            return False
    
    @staticmethod
    def compare_files(path1: Path, path2: Path) -> bool:
        """
        Vergleicht zwei JSON-Dateien strukturell
        
        Returns:
            True wenn inhaltlich gleich (ignoriert Whitespace)
        """
        try:
            data1 = loads(path1.read_text())
            data2 = loads(path2.read_text())
            return data1 == data2
        except Exception:
            return False
    
    @staticmethod
    def minify_file(input_path: Path, 
                   output_path: Optional[Path] = None) -> bool:
        """Entfernt alle Whitespaces (minimiert Dateigröße)"""
        try:
            data = loads(input_path.read_text())
            minified = dumps(data)  # Keine indentation
            
            out_path = output_path or input_path
            out_path.write_text(minified)
            
            return True
        except Exception as e:
            print(f"Error minifying: {e}")
            return False
    
    @staticmethod
    def merge_files(paths: List[Path]) -> Optional[Dict[str, Any]]:
        """
        Merged mehrere JSON-Objekte
        
        Args:
            paths: Liste von JSON-Dateien
            
        Returns:
            Merged Dict oder None bei Fehler
        """
        result = {}
        
        for path in paths:
            try:
                data = loads(path.read_text())
                if not isinstance(data, dict):
                    print(f"Warning: {path} is not an object, skipping")
                    continue
                result.update(data)
            except Exception as e:
                print(f"Error reading {path}: {e}")
        
        return result if result else None
```

**Vollständiges Demo-Programm:**
```python
def main():
    """Demo der JSON-Parser Funktionalität"""
    
    print("="*70)
    print("JSON PARSER DEMO")
    print("="*70)
    
    # Test 1: Einfaches Parsing
    print("\n1. Einfaches Parsing")
    print("-" * 70)
    
    simple_json = '{"name": "Alice", "age": 30, "active": true}'
    result = loads(simple_json)
    print(f"Input:  {simple_json}")
    print(f"Result: {result}")
    print(f"Type:   {type(result)}")
    
    # Test 2: Komplexe Struktur
    print("\n2. Komplexe verschachtelte Struktur")
    print("-" * 70)
    
    complex_json = '''
    {
        "users": [
            {
                "id": 1,
                "name": "Alice",
                "roles": ["admin", "user"],
                "metadata": {
                    "created": "2024-01-01",
                    "active": true
                }
            },
            {
                "id": 2,
                "name": "Bob",
                "roles": ["user"],
                "metadata": {
                    "created": "2024-01-02",
                    "active": false
                }
            }
        ],
        "total": 2
    }
    '''
    
    result = loads(complex_json)
    print(f"Users gefunden: {len(result['users'])}")
    print(f"Erster User: {result['users'][0]['name']}")
    print(f"Total: {result['total']}")
    
    # Test 3: Serialisierung
    print("\n3. Python → JSON Serialisierung")
    print("-" * 70)
    
    data = {
        'string': 'Hello World',
        'number': 42,
        'float': 3.14159,
        'bool': True,
        'null': None,
        'array': [1, 2, 3],
        'nested': {
            'a': 1,
            'b': 2
        }
    }
    
    # Kompakt
    compact = dumps(data)
    print("Kompakt:")
    print(compact)
    
    # Pretty
    pretty = dumps(data, indent=2)
    print("\nPretty-Print:")
    print(pretty)
    
    # Test 4: Escape-Sequenzen
    print("\n4. String Escape-Sequenzen")
    print("-" * 70)
    
    test_strings = [
        '{"text": "Line 1\\nLine 2"}',
        '{"text": "Tab\\there"}',
        '{"text": "Quote: \\"Hello\\""}',
        '{"text": "Path: C:\\\\Users\\\\file.txt"}',
    ]
    
    for json_str in test_strings:
        result = loads(json_str)
        print(f"JSON:   {json_str}")
        print(f"Python: {result['text']!r}")
        print()
    
    # Test 5: Fehlerbehandlung
    print("\n5. Fehlerbehandlung")
    print("-" * 70)
    
    invalid_jsons = [
        ('{"name": "Alice",}', 'Trailing comma'),
        ('{"name": Alice}', 'Unquoted string'),
        ('[1, 2, 3,]', 'Trailing comma in array'),
        ('{"name": "Alice"', 'Unclosed object'),
        ("{'name': 'Alice'}", 'Single quotes'),
    ]
    
    for json_str, description in invalid_jsons:
        print(f"\nTest: {description}")
        print(f"JSON: {json_str}")
        try:
            loads(json_str)
            print("✗ Should have failed!")
        except (JSONLexerError, JSONParseError) as e:
            print(f"✓ Caught error: {e}")
    
    # Test 6: Datei-Operationen
    print("\n6. Datei-Operationen")
    print("-" * 70)
    
    # Erstelle Test-Datei
    test_data = {
        'project': 'JSON Parser',
        'version': '1.0',
        'features': ['parsing', 'serialization', 'validation']
    }
    
    test_file = Path('test.json')
    dump(test_data, test_file.open('w'), indent=2)
    print(f"✓ Datei erstellt: {test_file}")
    
    # Lese zurück
    loaded = load(test_file.open('r'))
    print(f"✓ Datei gelesen: {loaded}")
    
    # Minifiziere
    JSONProcessor.minify_file(test_file, Path('test.min.json'))
    print(f"✓ Minified: {Path('test.min.json').read_text()}")
    
    # Vergleiche
    same = JSONProcessor.compare_files(test_file, Path('test.min.json'))
    print(f"✓ Strukturell gleich: {same}")
    
    # Test 7: Performance
    print("\n7. Performance-Test")
    print("-" * 70)
    
    # Erstelle großes JSON
    large_data = {
        'items': [
            {'id': i, 'name': f'Item {i}', 'value': i * 1.5}
            for i in range(1000)
        ]
    }
    
    # Serialisierung
    start = time.time()
    json_str = dumps(large_data)
    ser_time = time.time() - start
    print(f"Serialisierung (1000 items): {ser_time:.4f}s")
    print(f"Größe: {len(json_str):,} bytes")
    
    # Parsing
    start = time.time()
    parsed = loads(json_str)
    parse_time = time.time() - start
    print(f"Parsing (1000 items): {parse_time:.4f}s")
    print(f"Items: {len(parsed['items'])}")
    
    # Test 8: Custom Objects
    print("\n8. Custom Objects")
    print("-" * 70)
    
    from dataclasses import dataclass
    
    @dataclass
    class Person:
        name: str
        age: int
        active: bool
    
    person = Person("Alice", 30, True)
    
    # Serialisiere Dataclass
    json_str = dumps(person)
    print(f"Person → JSON: {json_str}")
    
    # Cleanup
    test_file.unlink(missing_ok=True)
    Path('test.min.json').unlink(missing_ok=True)
    
    print("\n" + "="*70)
    print("ALLE TESTS ABGESCHLOSSEN!")
    print("="*70)

if __name__ == '__main__':
    main()
```

**Erwarteter Output:**
```
======================================================================
JSON PARSER DEMO
======================================================================

1. Einfaches Parsing
----------------------------------------------------------------------
Input:  {"name": "Alice", "age": 30, "active": true}
Result: {'name': 'Alice', 'age': 30, 'active': True}
Type:   <class 'dict'>

2. Komplexe verschachtelte Struktur
----------------------------------------------------------------------
Users gefunden: 2
Erster User: Alice
Total: 2

3. Python → JSON Serialisierung
----------------------------------------------------------------------
Kompakt:
{"string": "Hello World", "number": 42, "float": 3.14159, ...}

Pretty-Print:
{
  "string": "Hello World",
  "number": 42,
  "float": 3.14159,
  "bool": true,
  "null": null,
  "array": [
    1,
    2,
    3
  ],
  "nested": {
    "a": 1,
    "b": 2
  }
}

...
```

## Bonus-Aufgaben (Optional)

### Bonus 1: JSON Schema Validation
Implementiere JSON Schema Validierung:
```python
schema = {
    "type": "object",
    "properties": {
        "name": {"type": "string"},
        "age": {"type": "number", "minimum": 0}
    },
    "required": ["name"]
}
```

### Bonus 2: JSON Patch
Implementiere JSON Patch (RFC 6902):
```python
patch = [
    {"op": "add", "path": "/age", "value": 30},
    {"op": "remove", "path": "/temporary"}
]
```

### Bonus 3: JSON Pointer
Implementiere JSON Pointer (RFC 6901):
```python
get_value(data, "/users/0/name")  # "Alice"
```

### Bonus 4: Streaming Parser
Implementiere SAX-style Event Parser für sehr große Dateien.

## Lernziele & verwendete Konzepte

**Datenstrukturen:**
- ✅ List für Token-Stream und Arrays
- ✅ Dict für Objekte und Mappings
- ✅ Stack (implizit durch Rekursion)
- ✅ Enum für Token-Types
- ✅ Dataclass für strukturierte Daten

**Konzepte:**
- Lexical Analysis (Tokenizing)
- Recursive Descent Parsing
- State Machines
- Error Handling mit Custom Exceptions
- Unicode und Encoding
- File I/O

**Performance:**
- O(n) Parsing (linearer Durchlauf)
- O(n) Serialisierung
- Streaming für große Dateien
- Memory-effiziente Implementierung

**Komplexität:**
- Zeit: O(n) für Parsing und Serialisierung
- Speicher: O(d) für verschachtelte Tiefe d

## Bewertungskriterien

### Funktionalität (40%)
- [ ] Alle JSON-Typen werden geparst
- [ ] Escape-Sequenzen funktionieren
- [ ] Serialisierung korrekt
- [ ] Fehlerbehandlung vorhanden

### Robustheit (30%)
- [ ] Detaillierte Fehlermeldungen
- [ ] Edge Cases behandelt
- [ ] Unicode-Support
- [ ] Keine Crashes

### Code-Qualität (20%)
- [ ] Klare Struktur (Lexer/Parser getrennt)
- [ ] Type Hints
- [ ] Docstrings
- [ ] PEP 8 konform

### Performance (10%)
- [ ] Effiziente Implementierung
- [ ] Keine unnötigen Kopien
- [ ] String-Building optimiert

## Testing

Teste mit diesen Edge Cases:

```python
# Test Cases
test_cases = [
    # Leer
    ('{}', {}),
    ('[]', []),
    
    # Verschachtelt
    ('[[[]]]', [[[]]]),
    ('{"a":{"b":{"c":1}}}', {'a': {'b': {'c': 1}}}),
    
    # Numbers
    ('{"n": 0}', {'n': 0}),
    ('{"n": -123}', {'n': -123}),
    ('{"n": 3.14}', {'n': 3.14}),
    ('{"n": 1.23e10}', {'n': 1.23e10}),
    ('{"n": 1.23e-10}', {'n': 1.23e-10}),
    
    # Strings
    ('{"s": ""}', {'s': ''}),
    ('{"s": "\\n"}', {'s': '\n'}),
    ('{"s": "\\""}', {'s': '"'}),
    ('{"s": "\\u0041"}', {'s': 'A'}),
    
    # Mixed
    ('[1, "two", true, null, {"five": 5}]', 
     [1, "two", True, None, {"five": 5}]),
]

for json_str, expected in test_cases:
    result = loads(json_str)
    assert result == expected, f"Failed: {json_str}"
    print(f"✓ {json_str}")
```

## Python-Dokumentation

📚 **Offizielle Ressourcen:**
- [json module](https://docs.python.org/3/library/json.html) - Zum Vergleich
- [String Methods](https://docs.python.org/3/library/stdtypes.html#string-methods)
- [File I/O](https://docs.python.org/3/tutorial/inputoutput.html#reading-and-writing-files)
- [Exceptions](https://docs.python.org/3/tutorial/errors.html)
- [Unicode](https://docs.python.org/3/howto/unicode.html)

📖 **Spezifikationen:**
- [JSON RFC 8259](https://datatracker.ietf.org/doc/html/rfc8259) - Offizielle JSON-Spezifikation

## Verwandte Themen

- [[02_Sequenzen/04_Comprehensions/01_List Comprehensions|List Comprehensions]]
- [[03_Mappings_und_Sets/01_Dictionaries/01_Dict Basics|Dictionary Basics]]
- [[01_Grundlagen/04_Strings/04_Encoding und Unicode|Unicode]]
- [[04_Fortgeschritten/04_Dataclasses/01_Dataclass Basics|Dataclasses]]

---
← [[06_Praxis/03_Projektübungen/02_Projekt - Cache Implementation|Projekt - Cache Implementation]] | [[INDEX|📑 Index]] | [[06_Praxis/03_Projektübungen/04_Projekt - Datenbankmodell|Projekt - Datenbankmodell]] →