# Tabla de Transiciones - Máquina de Turing Validador de Contraseñas

## Fase 1: Contar Longitud (mínimo 8 símbolos)

| Estado Actual | Símbolo Leído | Escribir | Mover | Siguiente Estado | Nota |
|---------------|---------------|----------|-------|------------------|------|
| q0 | cualquier símbolo (!B) | mismo | R | q1 | Cuenta 1 |
| q0 | B (blanco) | B | R | q_reject | Muy corta |
| q1 | !B | mismo | R | q2 | Cuenta 2 |
| q1 | B | B | R | q_reject | Muy corta |
| q2 | !B | mismo | R | q3 | Cuenta 3 |
| q2 | B | B | R | q_reject | Muy corta |
| q3 | !B | mismo | R | q4 | Cuenta 4 |
| q3 | B | B | R | q_reject | Muy corta |
| q4 | !B | mismo | R | q5 | Cuenta 5 |
| q4 | B | B | R | q_reject | Muy corta |
| q5 | !B | mismo | R | q6 | Cuenta 6 |
| q5 | B | B | R | q_reject | Muy corta |
| q6 | !B | mismo | R | q7 | Cuenta 7 |
| q6 | B | B | R | q_reject | Muy corta |
| q7 | !B | mismo | L | q_back | ≥8, inicia retroceso |
| q7 | B | B | R | q_reject | Exactamente 7 |

## Fase 2: Retroceder al Inicio

| Estado Actual | Símbolo Leído | Escribir | Mover | Siguiente Estado | Nota |
|---------------|---------------|----------|-------|------------------|------|
| q_back | !B | mismo | L | q_back | Retrocede |
| q_back | B | B | R | q_upper | Llegó al inicio |

## Fase 3: Buscar Mayúscula

| Estado Actual | Símbolo Leído | Escribir | Mover | Siguiente Estado | Nota |
|---------------|---------------|----------|-------|------------------|------|
| q_upper | A-Z | M | R | q_lower | ✓ Encontró mayúscula |
| q_upper | a-z, 0-9, símbolos | mismo | R | q_upper | Sigue buscando |
| q_upper | B | B | R | q_reject | No encontró |

**Símbolos para "sigue buscando":**
```
a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z,0,1,2,3,4,5,6,7,8,9,@,#,$,%,&,*,!,?,+,-,_,=
```

**Símbolos para "encontró":**
```
A,B,C,D,E,F,G,H,I,J,K,L,M,N,O,P,Q,R,S,T,U,V,W,X,Y,Z
```

## Fase 4: Buscar Minúscula

| Estado Actual | Símbolo Leído | Escribir | Mover | Siguiente Estado | Nota |
|---------------|---------------|----------|-------|------------------|------|
| q_lower | a-z | m | R | q_digit | ✓ Encontró minúscula |
| q_lower | A-Z, M, 0-9, símbolos | mismo | R | q_lower | Sigue buscando |
| q_lower | B | B | R | q_reject | No encontró |

**Símbolos para "encontró":**
```
a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z
```

## Fase 5: Buscar Dígito

| Estado Actual | Símbolo Leído | Escribir | Mover | Siguiente Estado | Nota |
|---------------|---------------|----------|-------|------------------|------|
| q_digit | 0-9 | D | R | q_accept | ✓ ¡VÁLIDA! |
| q_digit | A-Z, a-z, M, m, símbolos | mismo | R | q_digit | Sigue buscando |
| q_digit | B | B | R | q_reject | No encontró |

**Símbolos para "encontró":**
```
0,1,2,3,4,5,6,7,8,9
```

## Fase 6: Rechazar Espacios (desde TODOS los estados)

| Estado Actual | Símbolo Leído | Escribir | Mover | Siguiente Estado | Nota |
|---------------|---------------|----------|-------|------------------|------|
| q0, q1, q2, q3, q4, q5, q6, q7 | espacio | espacio | R | q_reject | Sin espacios |
| q_back | espacio | espacio | R | q_reject | Sin espacios |
| q_upper | espacio | espacio | R | q_reject | Sin espacios |
| q_lower | espacio | espacio | R | q_reject | Sin espacios |
| q_digit | espacio | espacio | R | q_reject | Sin espacios |

💡 **En JFLAP:** Para representar espacio, usa `~` o presiona la barra espaciadora

## Resumen de Estados

| Estado | Tipo | Descripción |
|--------|------|-------------|
| q0 | Inicial (→) | Comienza conteo |
| q1-q7 | Intermedio | Cuenta hasta 8 |
| q_back | Intermedio | Retrocede al inicio |
| q_upper | Intermedio | Busca mayúscula |
| q_lower | Intermedio | Busca minúscula |
| q_digit | Intermedio | Busca dígito |
| q_accept | Final (◎) | ✅ Contraseña válida |
| q_reject | Normal | ❌ Contraseña rechazada |

## Ejemplos de Ejecución

### ✅ Ejemplo 1: "Abc12345" (ACEPTA)

```
Cinta inicial: A b c 1 2 3 4 5 B B B...

Fase Longitud:
q0 → q1 → q2 → q3 → q4 → q5 → q6 → q7 (cuenta 8)

Fase Retroceso:
q7 ← q_back ← ... ← hasta el inicio

Fase Mayúscula:
q_upper lee 'A' → marca 'M' → q_lower

Fase Minúscula:
q_lower lee 'b' → marca 'm' → q_digit

Fase Dígito:
q_digit lee '1' → marca 'D' → q_accept ✓

Cinta final: M m c D 2 3 4 5 B B B...
```

### ❌ Ejemplo 2: "short" (RECHAZA - muy corta)

```
Cinta: s h o r t B B B...

q0 → q1 → q2 → q3 → q4 → q5 (lee B) → q_reject ✗
```

### ❌ Ejemplo 3: "NoDigit!" (RECHAZA - sin dígitos)

```
Cinta: N o D i g i t ! B B B...

Pasa longitud ✓
Encuentra mayúscula 'N' ✓
Encuentra minúscula 'o' ✓
Busca dígito... lee B → q_reject ✗
```

### ❌ Ejemplo 4: "Has Space1" (RECHAZA - tiene espacio)

```
Cinta: H a s   S p a c e 1 B B...
              ↑
En cualquier estado que lea ' ' → q_reject ✗
```

## Notas de Implementación en JFLAP

1. **Símbolo Blanco (B):** JFLAP usa el cuadrado vacío. Déjalo en blanco en las transiciones.

2. **Múltiples símbolos:** Separa con comas sin espacios:
   ```
   A,B,C,D,E,F
   ```

3. **Cualquier símbolo excepto blanco:** Usa `!B` en versiones recientes de JFLAP.

4. **Estado Final:** Click derecho en q_accept → "Final"

5. **Probar:** Input → Step (F5) o Fast Run

## Optimizaciones Posibles

- **Reducir estados de conteo:** Usar un solo estado con símbolos marcadores
- **Búsqueda paralela:** Marcar todos los tipos en un solo paso
- **Validación incremental:** Rechazar espacios sin terminar el conteo

Estas optimizaciones reducen estados pero aumentan la complejidad de transiciones.
