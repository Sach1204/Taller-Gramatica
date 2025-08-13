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
#include <string.h>
#include <stdbool.h>
#include <regex.h>

bool check_regex(const char *pattern, const char *input) {
    regex_t regex;
    int ret = regcomp(&regex, pattern, REG_EXTENDED);
    if (ret) return false;
    ret = regexec(&regex, input, 0, NULL, 0);
    regfree(&regex);
    return ret == 0;
}

int main(int argc, char *argv[]) {
    if (argc != 3) {
        printf("Uso: %s <G1|G2|G3|G4|G5> <archivo.txt>\n", argv[0]);
        return 1;
    }

    const char *patterns[] = {
        "^(0|1)((0|1)*\\1)?$",  // G1
        "^a*b+$",               // G2
        "^abb$",                // G3
        "^(ab|abb)$",           // G4
        "^a(ab)*b$"             // G5
    };

    int index = argv[1][1] - '1';  // Convierte "G1" -> 0, "G2" -> 1, etc.
    if (index < 0 || index > 4) {
        printf("Gramática no válida.\n");
        return 1;
    }

    FILE *file = fopen(argv[2], "r");
    if (!file) {
        perror("Error al abrir el archivo");
        return 1;
    }

    char line[256];
    while (fgets(line, sizeof(line), file)) {
        // Eliminar salto de línea
        line[strcspn(line, "\n")] = '\0';

        // Quitar comentarios (# ...)
        char *comment = strchr(line, '#');
        if (comment) *comment = '\0';

        // Eliminar espacios al inicio y fin
        char *start = line;
        while (*start == ' ' || *start == '\t') start++;
        char *end = start + strlen(start) - 1;
        while (end >= start && (*end == ' ' || *end == '\t')) *end-- = '\0';

        if (strlen(start) == 0) continue; // Línea vacía después de limpiar

        printf("%s -> %s\n", start, check_regex(patterns[index], start) ? "acepta" : "NO acepta");
    }

    fclose(file);
    return 0;
}
```
