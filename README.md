# Taller-Gramatica
### Codigo en Python ###
```python
import re

def check_grammar(grammar_id, input_string):
    patterns = {
        'G1': r'^(0|1)((0|1)*\1)?$',  # Capicuas binarios (ej: 010, 11011)
        'G2': r'^a*b+$',              # a^n b^{n+1} (ej: ab, aabbb)
        'G3': r'^abb$',               # Solo "abb"
        'G4': r'^(ab|abb)$',          # "ab" o "abb"
        'G5': r'^a(ab)*b$'            # a(ab)^n b (ej: ab, aababb)
    }
    
    if grammar_id not in patterns:
        return False
    
    return bool(re.fullmatch(patterns[grammar_id], input_string))

if __name__ == "__main__":
    grammar_id = "G1"  # Fijamos la gramática para este caso
    file_path = "g1.txt"  # Archivo a leer
    
    with open(file_path, "r", encoding="utf-8") as file:
        for line in file:
            # Quitar comentarios y espacios
            line = line.split("#")[0].strip()
            if not line:
                continue
            result = "acepta" if check_grammar(grammar_id, line) else "NO acepta"
            print(f"{line} -> {result}")
```
### Codigo en C ###
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <regex.h>
#include <stdbool.h>

#define MAX_LINE 256

// Función para validar usando regex
bool check_grammar(const char *grammar_id, const char *input_string) {
    const char *patterns[][2] = {
        {"G1", "^(0|1)((0|1)*\\1)?$"},   // Capicuas binarios
        {"G2", "^a*b+$"},                // a^n b^{n+1}
        {"G3", "^abb$"},                 // Solo "abb"
        {"G4", "^(ab|abb)$"},            // "ab" o "abb"
        {"G5", "^a(ab)*b$"}              // a(ab)^n b
    };

    regex_t regex;
    bool match = false;
    int num_patterns = sizeof(patterns) / sizeof(patterns[0]);

    for (int i = 0; i < num_patterns; i++) {
        if (strcmp(grammar_id, patterns[i][0]) == 0) {
            if (regcomp(&regex, patterns[i][1], REG_EXTENDED) != 0) {
                fprintf(stderr, "Error al compilar la expresión regular.\n");
                return false;
            }

            if (regexec(&regex, input_string, 0, NULL, 0) == 0) {
                match = true;
            }

            regfree(&regex);
            break;
        }
    }
    return match;
}

int main() {
    const char *grammar_id = "G1";     // Seleccionamos la gramática
    const char *file_path = "g1.txt"; // Archivo a leer
    FILE *file = fopen(file_path, "r");

    if (!file) {
        perror("Error al abrir el archivo");
        return 1;
    }

    char line[MAX_LINE];
    while (fgets(line, sizeof(line), file)) {
        // Quitar salto de línea
        line[strcspn(line, "\n")] = 0;

        // Quitar comentarios y espacios
        char *comment = strchr(line, '#');
        if (comment) *comment = '\0';
        for (int i = strlen(line) - 1; i >= 0 && (line[i] == ' ' || line[i] == '\t'); i--)
            line[i] = '\0';

        if (strlen(line) == 0) continue;

        bool result = check_grammar(grammar_id, line);
        printf("%s -> %s\n", line, result ? "acepta" : "NO acepta");
    }

    fclose(file);
    return 0;
}

```
