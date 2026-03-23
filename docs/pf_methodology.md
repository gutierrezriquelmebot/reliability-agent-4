# Metodología P-F aplicada al sistema

## ¿Qué es la curva P-F?

La curva P-F (Potential–Functional Failure) es un modelo de degradación de activos
que establece que toda falla sigue una trayectoria detectable antes de ocurrir.

```
Condición
del activo
    │
100%├─────────────────────────────────────────
    │                          ╲ P (punto detectable)
    │                           ╲
    │                            ╲ ← Zona P-F disponible
    │                             ╲
  0%│──────────────────────────────╲───────── F (falla funcional)
    │
    └──────────────────────────────────────── Tiempo
                                   ←ventana→
```

**Punto P**: momento en que la falla potencial es detectable con instrumentos de medición
(vibración, temperatura, ultrasonido, análisis de aceite).

**Punto F**: momento en que el activo pierde su función requerida (falla funcional).

**Ventana P-F**: tiempo disponible entre P y F para planificar y ejecutar una intervención.

---

## Cómo este sistema implementa el modelo P-F

### Índice P-F sintético

El sistema genera un índice continuo [0–100%] que representa el avance desde el
estado basal (P) hacia la falla funcional (F):

```javascript
// Degradación acumulada no lineal — simula física real de rodamientos
degradation = Math.min(0.99, degradation + rate * (1 + randn() * 0.1));

// Estimación de RUL con modelo exponencial inverso
function estimateRUL(pf) {
  const remaining = 1 - pf;
  return Math.max(0, Math.round(remaining * 380 * Math.exp(-pf)));
}
```

El modelo exponencial replica el comportamiento real: cuando el índice P-F está bajo,
el RUL disminuye lentamente. Cuando supera ~60%, la pérdida de vida se acelera
no-linealmente — exactamente como ocurre en rodamientos reales con fatiga de pistas.

### Cuatro zonas de la curva

| Zona | Índice P-F | Color del sistema | Acción |
|---|---|---|---|
| Basal | 0–35% | Verde | Monitoreo de rutina |
| Zona P (detección) | 35–60% | Verde/Amarillo | Aumentar frecuencia de medición |
| Zona P-F activa | 60–85% | Amarillo/Rojo | Programar intervención |
| Cercano a F | 85–100% | Rojo | Detención inmediata |

### Modos de simulación y parámetros

| Modo | Temp base | Ruido térmico | Vibración base | Tasa de degradación |
|---|---|---|---|---|
| Normal | 72°C | ±3°C | 2.1 mm/s | 0.02%/lectura |
| Degradación | 85°C | ±5°C | 5.5 mm/s | 0.3%/lectura |
| Crítico | 105°C | ±8°C | 10.2 mm/s | 0.8%/lectura |
| Falla inminente | 128°C | ±15°C | 18.5 mm/s | 2.0%/lectura |

---

## Correlación temperatura–vibración

La física de fallo en rodamientos produce patrones específicos:

### Lubricación deficiente
- Temperatura lidera el aumento (fricción → calor antes de vibración detectable)
- Vibración escalonada posterior (film hidro-dinámico comprometido → contacto metal-metal)
- Ratio T/V alto en etapas tempranas

### Fatiga de pistas (spalling)
- Vibración impulsiva lidera (defecto superficial genera impactos a frecuencia de pista)
- Temperatura secundaria (calor por impacto, no por fricción base)
- Ratio T/V bajo, vibración con componentes de alta frecuencia

### Desalineación
- Vibración a 1× y 2× RPM dominante
- Temperatura moderada y proporcional a la vibración
- El índice P-F crece linealmente, no exponencialmente

---

## Limitaciones del modelo

1. **El índice P-F es sintético**: en una implementación real se derivaría de
   análisis espectral de vibración (FFT), energía de emisión acústica,
   y análisis de aceite (partículas metálicas, viscosidad).

2. **RUL con incertidumbre alta**: el modelo exponencial es una aproximación.
   En activos reales se usarían modelos de degradación estocásticos
   (Weibull, Gamma process) calibrados con datos históricos del activo específico.

3. **Sin temperatura ambiente**: el sistema no compensa variaciones de temperatura
   ambiente, lo que en planta real produce falsos positivos en días de verano.

---

## Referencias

- Moubray, J. (1997). *Reliability-Centered Maintenance*. Industrial Press.
- ISO 13379-1:2012 — Condition monitoring — Data interpretation.
- ISO 10816-3:2009 — Mechanical vibration — Evaluation of machine vibration.
- CWRU Bearing Data Center — Case Western Reserve University (dataset real para trabajo futuro).
