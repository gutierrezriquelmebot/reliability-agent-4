# Diseño del Sistema de Prompts — Principal Reliability Engineer

## Filosofía de diseño

El sistema de prompts de este proyecto sigue la arquitectura **Rol + Restricciones + Protocolo**,
que produce respuestas consistentes, defendibles y orientadas a decisiones.

La premisa central: **un LLM genérico describe datos. Un LLM bien promoteado toma decisiones.**

---

## Arquitectura de dos capas

### Capa 1 — System Prompt (Rol fijo)

```
Identidad     → Principal Reliability Engineer, 25+ años
Especialidades → PdM · ISO 10816/20816 · Tribología · RCM/FMEA
Mandato       → EMITIR diagnóstico defendible + DECISIÓN operacional
Restricciones → Sin teoría, sin ambigüedad, sin suavizar conclusiones
Criterio de validez → Soporta 3 usos simultáneos:
                      1. Gerente detiene planta
                      2. Mantenimiento interviene activo
                      3. Finanzas justifica presupuesto
```

### Capa 2 — User Prompt (Datos + Protocolo dinámico)

```
Contexto del activo   → Fijo por diseño del sistema
Telemetría actual     → Inyectada en tiempo real desde sensores
Umbrales              → Configurados por el operador en el dashboard
Tendencia P-F         → Últimos 7 puntos con análisis pre-calculado
Datos financieros     → Exposición bruta + colateral pre-computada
Protocolo             → 8 secciones con estructura exacta obligatoria
```

---

## Protocolo de 8 secciones

| Sección | Propósito | Anti-patrón que elimina |
|---|---|---|
| 1. Diagnóstico físico | Forzar hipótesis única de fallo | "Podría ser X, Y o Z" |
| 2. Estado en curva P-F | Ubicación precisa en la curva | "Se encuentra en zona de riesgo" |
| 3. Tendencia y velocidad | Análisis de serie temporal | "La tendencia es creciente" |
| 4. Riesgo operacional | Probabilidades por ventana temporal | "Existe riesgo significativo" |
| 5. Decisión | Una sola acción, sin ambigüedad | "Se recomienda considerar..." |
| 6. Análisis financiero | Cálculo real A/B con ROI | "El costo podría ser alto" |
| 7. Plan de acción | 3 acciones con [QUÉ/CUÁNDO/MÉTRICA] | "Revisar el equipo" |
| 8. Formato de salida | Estructura exacta reproducible | Salidas inconsistentes |

---

## Técnicas de prompt engineering aplicadas

### 1. Role Anchoring con credenciales específicas
En vez de "actúa como experto", se especifican:
- Años de experiencia (25+)
- Normativas conocidas (ISO 10816/20816)
- Metodologías dominadas (RCM, FMEA, P-F)
- Tipo de industria (activos rotativos, rodamientos)

**Por qué funciona**: el modelo calibra su vocabulario técnico, asunciones y nivel de
detalle hacia ese perfil específico, no hacia un "experto genérico".

### 2. Mandato negativo explícito
```
Tu rol NO es describir datos.
Tu rol ES emitir un diagnóstico técnico defendible y una decisión operacional.
```
**Por qué funciona**: los LLMs tienden a resumir/describir por defecto. El mandato negativo
corta ese comportamiento antes de que empiece.

### 3. Criterio de validez multi-uso
```
Tu respuesta debe soportar simultáneamente:
1. Un gerente que detiene una planta
2. Un equipo que interviene un activo crítico
3. Un área financiera que justifica presupuesto
Si no soporta los tres, no es válida.
```
**Por qué funciona**: en vez de describir qué quiero en el output, describo quién lo usa
y para qué. El modelo razona desde el caso de uso real, no desde instrucciones de formato.

### 4. Restricciones lingüísticas específicas
```
Prohibido: "podría", "considerar", "evaluar", "se recomienda"
Obligatorio: una decisión única, justificada con criterio técnico + económico
```
**Por qué funciona**: elimina el lenguaje de cobertura que los modelos usan cuando
tienen incertidumbre. Los fuerza a comprometerse con una posición.

### 5. Pre-cómputo de contexto
El prompt no deja que el modelo haga la aritmética básica:
```javascript
// Pre-calculado antes de enviar al LLM
const exposureDirect = (snap.rul * 12500).toLocaleString();
const accelLabel = deltaHalves > 4 ? 'ACELERADA ↑↑' : ...;
const pfZone = snap.pf >= 85 ? 'CERCANO A F...' : ...;
```
**Por qué funciona**: el modelo gasta tokens en razonamiento, no en multiplicaciones.
También reduce alucinaciones numéricas al darle los números ya calculados.

### 6. Formato de acción estructurado
```
ACCIÓN N — [QUÉ hacer] / [CUÁNDO: plazo máximo] / [MÉTRICA de validación]
```
**Por qué funciona**: el triple formato [QUÉ/CUÁNDO/MÉTRICA] es imposible de responder
con vaguedad. Cada slot requiere un dato concreto o el formato falla visualmente.

---

## Iteraciones del sistema

| Versión | Cambio principal | Problema que resolvía |
|---|---|---|
| v1 | Prompt único con contexto + instrucción | LLM daba respuestas genéricas |
| v2 | System prompt separado del user prompt | Rol no se mantenía entre análisis |
| v3 | 4 prompts por tipo de análisis | Consistencia entre tipos, duplicación |
| **v4 (actual)** | **Protocolo único de 8 secciones** | **Todos los tipos ejecutan el mismo rigor** |

La evolución más importante fue pasar de "instrucciones por tipo" a "protocolo único con
metadatos variables". Esto garantiza que el análisis financiero tiene el mismo rigor que
el diagnóstico técnico, porque ambos ejecutan las mismas 8 secciones.

---

## Lecciones aprendidas

1. **La especificidad del rol importa más que la longitud del prompt.** Un PRE con 6 especialidades listadas produce mejor output que 500 tokens de instrucciones genéricas.

2. **Los criterios de validez son más poderosos que las instrucciones de formato.** Decir "debe soportar una decisión de detención de planta" produce más rigor que decir "sé detallado".

3. **El pre-cómputo de contexto es prompt engineering.** Calcular la tendencia y la zona P-F antes de enviar reduce alucinaciones y mejora la coherencia del análisis.

4. **El streaming cambia la experiencia más que el contenido.** Ver el análisis aparecer token a token genera más confianza en el sistema que una respuesta instantánea, porque el usuario puede leer y validar en paralelo.
