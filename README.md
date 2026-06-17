# Laboratorio de Estrés y Monitoreo de Sistemas con Docker, Next.js y PostgreSQL

[cite_start]Este repositorio contiene la implementación de un laboratorio diseñado para analizar, estresar y monitorear el comportamiento de un sistema operativo Linux (bajo arquitecturas de contenedores) frente a condiciones severas de alta carga[cite: 106].

## 👥 Autores
* [cite_start]**Juan Esteban Aguirre Castañeda** - Código: 202459676 [cite: 103]
* [cite_start]**Karen Sofía López Botero** - Código: 202459519 [cite: 103]
* [cite_start]*Ingeniería de Sistemas - Sistemas Operativos* [cite: 104, 115]

---

## 📝 Resumen del Proyecto
[cite_start]El objetivo principal es construir una plataforma capaz de generar carga controlada sobre diferentes recursos del hardware (CPU, Memoria e I/O) y observar en tiempo real la respuesta del kernel de Linux, el planificador (*scheduler*) y los procesos involucrados[cite: 109, 117].

[cite_start]La solución automatiza el despliegue de microservicios mediante **Docker Compose**, estresa dinámicamente el sistema mediante endpoints expuestos en una interfaz web y extrae métricas de bajo nivel leyendo directamente el sistema de archivos virtual `/proc`[cite: 107, 119, 135].

---

## 🏗️ Arquitectura del Sistema
[cite_start]El entorno está compuesto por tres contenedores principales interconectados de forma aislada a través de una red de tipo **bridge**[cite: 123, 124]:

1.  [cite_start]**Next.js (Frontend & API Carga):** Dashboard interactivo que permite configurar la concurrencia/duración de las pruebas, visualizar telemetría y lanzar los ataques simulados a `/api/stress-cpu` y `/api/stress-db`[cite: 119, 129, 131, 133].
2.  [cite_start]**PostgreSQL (Almacenamiento):** Motor encargado de soportar el estrés enfocado a base de datos y operaciones intensivas de I/O[cite: 123, 133].
3.  [cite_start]**Jupyter Notebook (Entrenamiento IA):** Contenedor destinado a ejecutar cargas mixtas a través del cálculo computacional masivo y entrenamiento de un modelo de Inteligencia Artificial[cite: 107, 123].

---

## 🛠️ Metodología de Pruebas y Estrés

[cite_start]El laboratorio evalúa de manera estricta **5 escenarios experimentales**[cite: 126]:
* [cite_start]**Estado Idle:** Línea base del sistema sin interacciones ni procesos activos[cite: 126].
* [cite_start]**Estrés de CPU:** Algoritmo pesado de cálculo de números primos que simula un escenario puramente *CPU-Bound*[cite: 131].
* [cite_start]**Estrés de Base de Datos:** Creación de tablas temporales, inserciones masivas de datos, ejecuciones de consultas (*queries*) pesadas y limpieza forzada vía `TRUNCATE` (*I/O-Bound*)[cite: 133].
* [cite_start]**Entrenamiento de IA:** Ejecución de multiplicaciones matriciales continuas de alta densidad[cite: 144].
* [cite_start]**Escenario Simultáneo:** Ejecución concurrente de todas las cargas anteriores para forzar la competencia crítica por recursos[cite: 126, 151].

> [cite_start]📊 **Herramientas de Monitoreo Utilizadas:** `htop`, `vmstat`, `iostat`, `docker stats`, `pg_stat_activity` y lectura directa sobre `/proc/stat` y `/proc/meminfo`[cite: 127, 135].

---

## 📈 Resultados Clave de las Pruebas

### 1. Estado Idle (Línea Base)
[cite_start]El host se mantiene completamente estable, demostrando que las colas de ejecución de los contenedores están vacías[cite: 138].
* [cite_start]**CPU Global:** ~1.10% [cite: 138]
* [cite_start]**RAM Global:** 1.14 GB [cite: 138]

### 2. Estrés de CPU Sostenido
[cite_start]Al invocar el endpoint de números primos, un núcleo lógico del procesador es asignado al 100% para suplir al contenedor de Next.js[cite: 140]. [cite_start]El incremento en memoria RAM es marginal debido a que es procesamiento matemático puro sin persistencia[cite: 140].
* [cite_start]**CPU Global:** ~99.00% (Contenedor Next.js absorbe el 98.26%) [cite: 140]

### 3. Estrés en PostgreSQL
[cite_start]PostgreSQL implementa una arquitectura basada en multiprocesamiento simétrico (SMP), delegando las tareas a múltiples procesos hijos independientes, lo que dispara las métricas de CPU acumulada por encima del 100%[cite: 142]. [cite_start]Además, se registra un flujo masivo de escritura y lectura en almacenamiento secundario (*Block I/O Elevado*)[cite: 142].
* [cite_start]**CPU Contenedor Postgres:** 259.20% [cite: 142]
* [cite_start]**Block I/O:** 48.2 MB (In) / 112 MB (Out) [cite: 142]

### 4. Entrenamiento de IA
Evidencia una carga fuertemente mixta. [cite_start]Mientras que los núcleos realizan operaciones de alta densidad, Jupyter asigna de forma masiva memoria RAM física para alojar las matrices del dataset[cite: 144].
* [cite_start]**RAM Contenedor Jupyter:** 3.62 GB consumidos [cite: 144]

### 5. Escenario Simultáneo (Colapso / Cuello de Botella)
[cite_start]El hardware ingresa en una fase de saturación crítica[cite: 146]. [cite_start]Los recursos del procesador se agotan al límite y la RAM roza el umbral operativo máximo, lo que degrada la respuesta del sistema[cite: 146].
* [cite_start]**CPU Global:** 100.00% [cite: 146]
* [cite_start]**RAM Global:** 7.38 GB / 7.64 GB [cite: 146]

---

## 🧠 Conclusiones Clave del Laboratorio
* [cite_start]**Orquestación Efectiva:** `Docker Compose` proporcionó un entorno aislado óptimo para limitar y medir con precisión el alcance de los recursos asignados[cite: 153].
* [cite_start]**Comportamiento de la RAM:** Tras los picos de estrés, la memoria RAM global baja de manera paulatina[cite: 148]. [cite_start]Esto se debe a que el kernel de Linux conserva datos de manera preventiva en el caché de páginas virtuales para optimizar lecturas futuras, liberándola solo si existe una demanda externa real[cite: 148].
* [cite_start]**Competencia de Recursos:** Se constató el impacto real en el planificador de Linux ante procesos que pelean simultáneamente por ciclos de reloj de procesamiento matemático en paralelo a accesos concurrentes a disco[cite: 157].

---

## 📖 Referencias (Norma IEEE)
* [cite_start][1] Docker Inc., "Docker Documentation." [cite: 159]
* [cite_start][2] PostgreSQL Global Development Group, "PostgreSQL Documentation." [cite: 160]
* [cite_start][3] Vercel, "Next.js Documentation." [cite: 161]
* [cite_start][4] The Linux Kernel Documentation, "/proc filesystem." [cite: 162]
* [cite_start][5] A. Silberschatz, P. B. Galvin, y G. Gagne, "Operating System Concepts." [cite: 163]
