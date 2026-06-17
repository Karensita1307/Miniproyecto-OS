# Laboratorio de Estrés y Monitoreo de Sistemas con Docker, Next.js y PostgreSQL

Este repositorio contiene la implementación de un laboratorio diseñado para analizar, estresar y monitorear el comportamiento de un sistema operativo Linux frente a condiciones severas de alta carga utilizando contenedores.

## 👥 Autores
* **Juan Esteban Aguirre Castañeda** - Código: 202459676
* **Karen Sofía López Botero** - Código: 202459519
* *Ingeniería de Sistemas - Sistemas Operativos*

---

## 📝 Resumen del Proyecto
El objetivo principal es construir una plataforma capaz de generar carga controlada sobre diferentes recursos del hardware (CPU, Memoria e I/O) y observar la respuesta del kernel de Linux, el planificador (*scheduler*) y los procesos involucrados.

La solución utiliza **Docker Compose** para desplegar los servicios, estresa dinámicamente el sistema mediante endpoints expuestos en una interfaz web y extrae métricas leyendo directamente el sistema de archivos virtual `/proc`.

---

## 🏗️ Arquitectura del Sistema
El entorno está compuesto por tres contenedores principales interconectados a través de una red de tipo **bridge**:

1.  **Next.js (Frontend & API de Carga):** Dashboard interactivo que permite configurar la concurrencia y duración de las pruebas, visualizar telemetría y lanzar los escenarios de estrés a `/api/stress-cpu` y `/api/stress-db`.
2.  **PostgreSQL (Almacenamiento):** Motor encargado de soportar el estrés enfocado a base de datos y operaciones intensivas de Entrada/Salida (I/O).
3.  **Jupyter Notebook (Entrenamiento IA):** Contenedor destinado a ejecutar cargas mediante el entrenamiento de un modelo de Inteligencia Artificial.

---

## 🛠️ Metodología de Pruebas y Estrés

El laboratorio evalúa de manera estricta **5 escenarios experimentales**:
* **Estado Idle:** Línea base del sistema sin interacciones ni procesos activos.
* **Estrés de CPU:** Algoritmo de cálculo de números primos que simula un escenario puramente *CPU-Bound*.
* **Estrés de Base de Datos:** Creación de tablas, inserciones masivas, consultas pesadas y limpieza forzada vía `TRUNCATE`.
* **Entrenamiento de IA:** Procesamiento computacional continuo para el entrenamiento de un modelo.
* **Escenario Simultáneo:** Ejecución concurrente de todas las cargas anteriores para forzar la competencia crítica por recursos.

> 📊 **Herramientas de Monitoreo Utilizadas:** `htop`, `vmstat`, `iostat`, `docker stats`, `pg_stat_activity` y lectura directa sobre `/proc/stat` y `/proc/meminfo`.

---

## 📈 Resultados Clave de las Pruebas

### 1. Estado Idle (Línea Base)
El host se mantiene completamente estable, demostrando que las colas de ejecución de los contenedores están vacías.
* **CPU Global:** ~1.10%
* **RAM Global:** 1.14 GB / 7.64 GB

### 2. Estrés de CPU Sostenido
Al invocar el endpoint de números primos, un núcleo lógico del procesador es asignado casi por completo al contenedor de Next.js. El incremento en memoria RAM es marginal debido a que es procesamiento matemático puro sin almacenamiento intermedio.
* **CPU Global:** ~99.00% (Contenedor Next.js absorbe el 98.26%)

### 3. Estrés en PostgreSQL
PostgreSQL implementa una arquitectura basada en multiprocesamiento simétrico (SMP), delegando las tareas a múltiples procesos hijos independientes, lo que eleva la lectura de CPU acumulada por encima del 100%. Las métricas de Block I/O demuestran un flujo crítico de escritura en almacenamiento secundario.
* **CPU Contenedor Postgres:** 259.20%
* **Block I/O:** 48.2 MB (In) / 112 MB (Out)

### 4. Entrenamiento de IA
Evidencia una naturaleza fuertemente mixta. Mientras que los núcleos del procesador realizan multiplicaciones matriciales continuas de alta densidad, Jupyter consume de manera masiva la memoria RAM física para albergar las matrices del dataset.
* **RAM Contenedor Jupyter:** 3.62 GB consumidos

### 5. Escenario Simultáneo (Saturación Crítica)
El hardware ingresa en un cuello de botella absoluto. Los recursos globales del procesador se agotan por completo y la memoria RAM roza su capacidad máxima operativa, degradando drásticamente el rendimiento general.
* **CPU Global:** 100.00%
* **RAM Global:** 7.38 GB / 7.64 GB

---

## 🧠 Conclusiones Clave del Laboratorio
* **Orquestación y Aislamiento:** `Docker Compose` facilitó enormemente el despliegue y el aislamiento de los servicios para analizar la competencia por recursos.
* **Comportamiento de la RAM en Recuperación:** Tras detener el estrés, la CPU cae inmediatamente a un estado ocioso, pero la memoria RAM disminuye de forma paulatina. Esto se debe a que el kernel conserva datos en el caché de páginas virtuales para acelerar futuras lecturas de disco, liberándolos solo si otro proceso los solicita.
* **Monitoreo de Bajo Nivel:** El uso de archivos del sistema como `/proc` demostró ser un mecanismo altamente efectivo para obtener métricas del host en tiempo real sin añadir sobrecarga masiva.

---

## 📖 Referencias (Norma IEEE)
* [1] Docker Inc., "Docker Documentation."
* [2] PostgreSQL Global Development Group, "PostgreSQL Documentation."
* [3] Vercel, "Next.js Documentation."
* [4] The Linux Kernel Documentation, "/proc filesystem."
* [5] A. Silberschatz, P. B. Galvin, y G. Gagne, "Operating System Concepts."
