# Implementación final: ablación del entrelazamiento en una QCNN de 6 qubits

Benjamín Cárdenas · INF-465 Introducción a la Computación Cuántica · UTFSM · noviembre de 2026

El informe es el notebook `Implementacion_Final_QCNN.ipynb`. Ya está ejecutado, así que se puede leer con sus resultados sin correrlo.

[![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Devilman33/qcnn-ablacion-entrelazamiento/blob/main/Implementacion_Final_QCNN.ipynb)

En Google Colab basta con *Entorno de ejecución → Ejecutar todas*: la primera celda de código descarga este repositorio e instala lo que Colab no trae (`requirements-colab.txt`).

## Contenido

| Archivo o carpeta | Qué es |
|---|---|
| `Implementacion_Final_QCNN.ipynb` | Notebook con el código, las figuras y la discusión |
| `requirements.txt` | Versiones exactas (PennyLane 0.45.1, JAX 0.10.2, Qiskit 2.6.2, Qiskit Aer 0.18.4, Qiskit IBM Runtime 0.52.1) |
| `requirements-colab.txt` | Solo las librerías que Colab no trae; las usa la primera celda del notebook |
| `resultados/` | Entrenamientos, simulaciones ruidosas y conteos de los procesadores ibm_fez e ibm_kingston |
| `datos_anthony_zenodo/` | Resultados publicados por Anthony et al. (2026) en Zenodo (CC BY 4.0), usados en la replicación |
| `referencia_pennylane/` | `demo.py` original del tutorial de PennyLane (Apache 2.0), ejecutado sin modificar como referencia |

## Cómo ejecutarlo

```
pip install -r requirements.txt
jupyter notebook Implementacion_Final_QCNN.ipynb
```

Hay que abrirlo desde esta carpeta, porque lee `resultados/`, `datos_anthony_zenodo/` y `referencia_pennylane/` con rutas relativas. Con `RECALCULAR = False` (sección 3) y `EJECUTAR_IBM = False` (sección 11), el notebook lee los resultados guardados: corre completo en unos 2 minutos y no se conecta a IBM Quantum. Recalcular todo toma alrededor de una hora; volver a ejecutar en hardware requiere una cuenta de IBM Quantum guardada con `QiskitRuntimeService.save_account(...)`.
