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

> 📊 **Herramientas de Monitoreo Utilizadas:** `ht
