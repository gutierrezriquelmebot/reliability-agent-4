# 🔩 Reliability Agent 4.0
### Sistema de Detección P-F con IA para Activos Industriales Críticos

<div align="center">

![Status](https://img.shields.io/badge/status-active-brightgreen)
![HTML](https://img.shields.io/badge/frontend-HTML%2FJS%2FChart.js-blue)
![LLM](https://img.shields.io/badge/LLM-Claude%20Sonnet%204.5-purple)
![License](https://img.shields.io/badge/license-MIT-orange)
![Año](https://img.shields.io/badge/año-2025-lightgrey)

**[→ Demo en vivo](https://gutierrezriquelmebot.github.io/reliability-agent-4/)** · **[Ver código fuente](./reliability_agent_4.html)**

</div>

---

## ¿Qué es este proyecto?

Sistema de monitoreo predictivo de rodamientos industriales que combina **simulación de sensores en tiempo real**, **visualización interactiva** y un **agente LLM** que actúa como Principal Reliability Engineer con 25+ años de experiencia.

Construido como herramienta de toma de decisiones ejecutivas en planta, orientada a eliminar fallas no programadas que cuestan **USD $12,500/hora**.

---

## El problema real que resuelve

Las plantas industriales pierden millones anuales en fallas de rodamientos que pudieron detectarse semanas antes. El desafío no es la medición — es **la interpretación y la decisión a tiempo**.

Este sistema transforma telemetría cruda (temperatura + vibración) en **memorándums técnicos ejecutivos** que un jefe de mantenimiento puede usar para detener una planta o programar una intervención con justificación financiera.

---

## Arquitectura del Sistema

```
┌─────────────────────────────────────────────────────────────┐
│                   RELIABILITY AGENT 4.0                      │
├──────────────────────┬──────────────────────────────────────┤
│   CAPA DE DATOS      │         CAPA DE INTELIGENCIA         │
│                      │                                       │
│  Generador sintético │  System Prompt: PRE 25+ años         │
│  ├─ Ruido gaussiano  │  ├─ ISO 10816 / 20816                │
│  ├─ Anomalías        │  ├─ Física del fallo (tribología)    │
│  └─ Degradación P-F  │  ├─ Protocolo 8 secciones            │
│                      │  └─ Streaming SSE en tiempo real     │
│  4 modos:            │                                       │
│  Normal → Degradando │  4 tipos de análisis:                │
│  Crítico → Falla     │  ├─ Diagnóstico de estado            │
│                      │  ├─ Tendencia P-F                    │
│  Umbrales ajustables │  ├─ Orden de detención (OD)          │
│  en tiempo real      │  └─ Análisis financiero A/B          │
└──────────────────────┴──────────────────────────────────────┘
```

---

## Features

| Módulo | Detalle |
|---|---|
| **Simulación de sensores** | Dataset sintético con ruido gaussiano, inyección de anomalías, 4 modos de degradación |
| **Curva P-F en tiempo real** | Índice de degradación acumulada con modelo exponencial inverso para RUL |
| **Umbrales configurables** | 5 sliders: temp alerta/crítico, vibración alerta/crítico, índice P-F |
| **Gráficos dinámicos** | Chart.js con líneas de umbral animadas, actualización cada 1.5s |
| **Log de eventos** | Detección automática de anomalías y cruce de umbrales con timestamp |
| **Motor LLM** | Claude Sonnet 4.5 vía Anthropic API con system prompt de PRE |
| **Streaming SSE** | Respuesta token-por-token con cursor parpadeante |
| **4 tipos de memorándums** | Diagnóstico · Tendencia · Orden OD · Financiero A/B |
| **Zero infraestructura** | HTML puro, sin servidor, sin dependencias externas |

---

## Stack Tecnológico

```
Frontend     →  HTML5 · Vanilla JS · Chart.js 4.4
LLM          →  Anthropic Claude Sonnet 4.5 (API directa)
Protocolo    →  SSE (Server-Sent Events) para streaming
Tipografía   →  IBM Plex Mono · Inter (Google Fonts)
Deploy       →  GitHub Pages (estático, sin backend)
```

---

## Metodología aplicada

### Curva P-F (Potential–Functional Failure)
El corazón del sistema. La curva P-F es un modelo de degradación que define:
- **Punto P**: momento en que la falla potencial es detectable con instrumentos
- **Punto F**: momento de la falla funcional (pérdida de función)
- **Ventana P-F**: tiempo disponible para intervenir antes de la falla

El sistema monitorea en qué parte de esta curva se encuentra el activo y genera alertas cuando la degradación acelera.

### Generación de datos sintéticos
```javascript
// Ruido gaussiano (Box-Muller)
function randn() {
  let u = 0, v = 0;
  while(u === 0) u = Math.random();
  while(v === 0) v = Math.random();
  return Math.sqrt(-2 * Math.log(u)) * Math.cos(2 * Math.PI * v);
}

// Temperatura con tendencia + ruido + anomalías
let temp = base + trend * tick + randn() * noise;
if (Math.random() < anomalyProb) temp += noise * (2 + Math.random() * 3);
```

### Sistema de Prompting (Principal Reliability Engineer)
El agente LLM opera con arquitectura de dos capas:

```
system  →  Rol + capacidades + reglas inviolables (fijo en cada llamada)
user    →  Contexto del activo + telemetría actual + protocolo de 8 secciones
```

El protocolo exige: diagnóstico físico del fallo → zona P-F → tendencia → riesgo → **decisión única** → financiero A/B → plan de acción. Sin ambigüedad, sin opciones múltiples.

---

## Cómo usar

### Opción 1 — Directo en el navegador
```bash
# Descarga el archivo y ábrelo
open reliability_agent_4.html   # macOS
start reliability_agent_4.html  # Windows
```

### Opción 2 — GitHub Pages
El proyecto está desplegado en:
```
https://gutierrezriquelmebot.github.io/reliability-agent-4/
```

### Activar el motor de IA
1. Obtén una API Key en [console.anthropic.com](https://console.anthropic.com)
2. Pégala en el campo "API Key de Anthropic" del panel derecho
3. Selecciona un modo de simulación (Normal → Falla Inminente)
4. Haz clic en cualquier tipo de análisis

> La API Key no se almacena. Se usa solo en memoria durante la sesión del navegador.

---

## Estructura del repositorio

```
reliability-agent-4/
│
├── reliability_agent_4.html    # Aplicación completa (single-file)
├── README.md                   # Este archivo
├── LICENSE                     # MIT
│
├── docs/                       # Documentación técnica
│   ├── arquitectura.md         # Diagrama de arquitectura detallado
│   ├── pf_methodology.md       # Metodología P-F aplicada
│   └── prompt_engineering.md   # Diseño del sistema de prompts
│
└── assets/                     # Screenshots y recursos visuales
    └── dashboard_preview.png
```

---

## Contexto académico

Desarrollado como proyecto de portafolio en el marco de la formación en **Ingeniería Civil Industrial** en la **Universidad Católica de la Santísima Concepción (UCSC)**, con aplicación directa a:

- Gestión de activos y mantenimiento industrial (asignatura relacionada: Gestión de Operaciones)
- Inteligencia artificial aplicada a procesos productivos
- Toma de decisiones basada en datos en entornos industriales críticos

El proyecto integra conceptos de **confiabilidad industrial** (metodología P-F, RCM) con **ingeniería de software** (arquitectura cliente, LLM, streaming) y **ciencia de datos** (generación de datos sintéticos, modelado de degradación).

---

## Próximos pasos del portafolio

- [ ] **Notebook de análisis**: Jupyter con dataset real de rodamientos (CWRU Bearing Dataset), feature engineering vibracional, modelos de clasificación de fallo
- [ ] **Dashboard Energía**: monitoreo de consumo eléctrico industrial con detección de anomalías
- [ ] **API REST**: backend FastAPI que expone el motor de diagnóstico como servicio

---

## Autor

**Emilio Gutiérrez Riquelme**
Estudiante de último año — Ingeniería Civil Industrial
Universidad Católica de la Santísima Concepción (UCSC)

[![GitHub](https://img.shields.io/badge/GitHub-gutierrezriquelmebot-black?logo=github)](https://github.com/gutierrezriquelmebot)

---

## Licencia

MIT — libre para usar, modificar y distribuir con atribución.
