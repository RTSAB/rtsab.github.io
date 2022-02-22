---
layout: default
title: CUDA
nav_order: 4
parent: Machine Learning och AI
permalink: /docs/cuda
has_toc: true
---

# CUDA

CUDA är en samling av bibliotek, SDK och verktyg för optimering av parallel computing byggt på NVIDIA CUDA. Med hjälp av dessa kan utvecklare enkelt dra nytta av GPU och optimera för sina syften, oavsett om det är deep learning eller massiva parallella beräkningar.

Det finns många appar och frameworks som använder CUDA, t ex [PyTorch](https://pytorch.org/), [TensorFlow](https://www.tensorflow.org/) och [Autodesk](https://www.autodesk.se/). Många programmeringsspråk har också implementerat CUDA där kanske Python och C++ är de mest populära.

## CUDA Toolkit

För att utveckla CUDA-baserade applikationer krävs en CUDA-kapabel GPU och en drivrutin som är kompatibel med den version av CUDA Toolkit man använder. Vilka versioner som stöds kan man hitta på [Nvidia Developer](https://developer.nvidia.com/cuda-gpus). Drivrutinen för CUDA är bakåtkompatibel, dvs applikationer som är kompilerade mot en viss version av CUDA fungerar även mot senare versioner.

CUDA Toolkit består av bibliotek med speciell inriktning mot olika användningsområden, t ex:

- cuBLAS för linjär algebra (Basic Linear Algebra Subprograms)
- cuFFT för Fourier Transformationer
- cuRAND för slumptalsberäkningar (randomiseringar)
- cuSOLVER för exempelvis linjära ekvationer
- cuSPARSE för numerisk analys med glesa (sparse) matriser
- NVIDIA Performance Primitives för att processa bilder och signaler

Med hjälp av en kompilator skapas därefter koden för den drivrutin man använder.

