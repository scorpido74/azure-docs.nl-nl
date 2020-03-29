---
title: HPC-toepassingen schalen - Virtuele Azure-machines | Microsoft Documenten
description: Meer informatie over het schalen van HPC-toepassingen op Azure VM's.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707843"
---
# <a name="scaling-hpc-applications"></a>HPC-toepassingen schalen

Optimale scale-up en scale-out prestaties van HPC-toepassingen op Azure vereisen prestatieafstemming en optimalisatie-experimenten voor de specifieke workload. Deze sectie en de VM-serie-specifieke pagina's bieden algemene richtlijnen voor het schalen van uw toepassingen.

## <a name="compiling-applications"></a>Toepassingen samenstellen

Hoewel niet nodig, biedt het samenstellen van toepassingen met de juiste optimalisatievlaggen de beste scale-upprestaties op HB- en HC-serie VM's.

### <a name="amd-optimizing-cc-compiler"></a>AMD Optimaliseren C/C++ Compiler

Het AMD Optimizing C/C++ Compiler-compilersysteem (AOCC) biedt een hoog niveau van geavanceerde optimalisaties, multi-threading en processorondersteuning, waaronder wereldwijde optimalisatie, vectorisatie, interprocedurele analyses, lustransformaties en codegeneratie. AOCC compiler binaries zijn geschikt voor Linux systemen met GNU C Library (glibc) versie 2.17 en hoger. De compiler suite bestaat uit een C/C++ compiler (clang), een Fortran compiler (FLANG) en een Fortran front-end naar Clang (Dragon Egg).

### <a name="clang"></a>Clang

Clang is een C,C++, en Objective-C compiler die voorbewerking, parsing, optimalisatie, code generatie, assemblage en koppeling. Clang ondersteunt `-march=znver1` de vlag om de beste codegeneratie en tuning mogelijk te maken voor AMD's Op Zen gebaseerde x86-architectuur.

### <a name="flang"></a>FLANG (FLANG)

De FLANG compiler is een recente toevoeging aan de AOCC suite (toegevoegd april 2018) en is momenteel in pre-release voor ontwikkelaars om te downloaden en te testen. Op basis van Fortran 2008 breidt AMD dehttps://github.com/flangcompiler/flang)GitHub-versie van FLANG uit ( . De FLANG compiler ondersteunt alle Clang compiler opties en een extra aantal FLANG-specifieke compiler opties.

### <a name="dragonegg"></a>Dragonegg Dragonegg

DragonEgg is een gcc plugin die GCC's optimizers en code generators vervangt met die van het LLVM project. DragonEgg die wordt geleverd met AOCC werkt met gcc-4.8.x, is getest voor x86-32/x86-64 doelen en is met succes gebruikt op verschillende Linux-platforms.

GFortran is de werkelijke frontend voor Fortran-programma's die verantwoordelijk zijn voor voorbewerking, parsing en semantische analyse die de GCC GIMPLE intermediate representation (IR) genereert. DragonEgg is een GNU plugin, aansluiten op GFortran compilatie stroom. Het implementeert de GNU plugin API. Met de plugin-architectuur wordt DragonEgg de compilerdriver, die de verschillende fasen van de compilatie aandrijft.  Na het volgen van de download- en installatieinstructies kan Dragon Egg worden aangeroepen met: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>BGA-compiler
PGI Community Edition ver. 17 is bevestigd om te werken met AMD EPYC. Een bga-gecompileerde versie van STREAM levert wel volledige geheugenbandbreedte van het platform. De nieuwere Community Edition 18.10 (november 2018) zou eveneens goed moeten werken. Hieronder is voorbeeld CLI om compiler optimaal met de Intel Compiler:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel Compiler
Intel Compiler ver. 18 is bevestigd om te werken met AMD EPYC. Hieronder is voorbeeld CLI om compiler optimaal met de Intel Compiler.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC-compiler 
Voor HPC raadt AMD GCC compiler 7.3 of nieuwer aan. Oudere versies, zoals 4.8.5 bij RHEL/CentOS 7.4, worden niet aanbevolen. GCC 7.3, en nieuwer, zal leveren aanzienlijk hogere prestaties op HPL, HPCG, en DGEMM tests.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Toepassingen vergroten/verkleinen 

De volgende suggesties zijn van toepassing op optimale efficiëntie, prestaties en consistentie van het schalen van toepassingen:

* Maak processen vast aan kernen 0-59 met behulp van een sequentiële pinning-benadering (in tegenstelling tot een automatische balansbenadering). 
* Binding door Numa/Core/HwThread is beter dan standaard binding.
* Voor hybride parallelle toepassingen (OpenMP+MPI) gebruikt u 4 threads en 1 MPI-rang per CCX.
* Voor pure MPI-toepassingen, experimenteren met 1-4 MPI rangen per CCX voor optimale prestaties.
* Sommige toepassingen met een extreme gevoeligheid voor geheugenbandbreedte kunnen baat hebben bij het gebruik van een verminderd aantal cores per CCX. Voor deze toepassingen kan het gebruik van 3 of 2 cores per CCX de discussie over de geheugenbandbreedte verminderen en hogere reële prestaties of consistentere schaalbaarheid opleveren. Met name MPI Allreduce kan hiervan profiteren.
* Voor aanzienlijk grotere schaalruns is het raadzaam om UD- of hybride RC+UD-transporten te gebruiken. Veel MPI-bibliotheken/runtime-bibliotheken doen dit intern (zoals UCX of MVAPICH2). Controleer uw transportconfiguraties op grootschalige ritten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) op Azure.
