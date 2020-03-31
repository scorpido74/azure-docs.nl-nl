---
title: Ondersteunde talen
titleSuffix: Azure Data Science Virtual Machine
description: De ondersteunde programmatalen en gerelateerde tools die vooraf zijn geïnstalleerd op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283652"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Talen ondersteund op de Data Science Virtual Machine 

De Data Science Virtual Machine (DSVM) wordt geleverd met verschillende vooraf gebouwde talen en ontwikkeltools voor het bouwen van uw kunstmatige intelligentie (AI) toepassingen. Hier zijn enkele van de opmerkelijke.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016-editie)

|    |           |
| ------------- | ------------- |
| Ondersteunde taalversies | Python 2.7 en 3.7 |
| Ondersteunde DSVM-edities      | Windows Server 2016     |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | Er `conda` worden twee globale omgevingen gemaakt: <br /> * `root` De omgeving `/anaconda/` op python 3.7. <br/> * `python2` De omgeving `/anaconda/envs/python2` op python 2.7.       |
| Koppelingen naar voorbeelden      | Voorbeeld van Jupyter-notitieblokken voor Python zijn inbegrepen.     |
| Gerelateerde tools op de DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> Builds van Windows Server 2016 die vóór maart 2018 zijn gemaakt, bevatten Python 3.5 en Python 2.7. Python 2.7 is de **conda-wortelomgeving** en **py37** is de Python 3.7-omgeving.

### <a name="how-to-use-and-run-it"></a>Hoe het te gebruiken en uit te voeren    

* Uitvoeren met een opdrachtprompt:

  Open een opdrachtprompt en gebruik een van de volgende methoden, afhankelijk van de versie van Python die u wilt uitvoeren:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Gebruik in een IDE:

  Gebruik Python Tools for Visual Studio (PTVS), geïnstalleerd in de Visual Studio Community-editie. Standaard is python 3.6 de enige omgeving die automatisch is ingesteld in PTVS. 

    > [!NOTE]
    > Als u PTVS wilt aanwijzen op Python 2.7, moet u een aangepaste omgeving in PTVS maken. Als u dit omgevingspad wilt instellen in de Visual Studio **+ Custom**Community Edition, gaat u naar**Python-omgevingen** voor -> **hulpprogramma's** -> en selecteert u + Aangepast . **Tools** Stel vervolgens de locatie in op **c:\anaconda\envs\python2** en selecteer **Automatisch detecteren**.

* Gebruik in Jupyter:

  Open Jupyter en selecteer **Nieuw** om een nieuw notitieblok te maken. U het kerneltype instellen als _Python [Conda Root]_ voor Python 3.7 en _Python [Conda env:python2]_ voor Python 2.7.

* Python-pakketten installeren:

  De standaard Python-omgevingen op de DSVM zijn globale omgevingen die leesbaar zijn voor alle gebruikers. Maar alleen beheerders kunnen globale pakketten schrijven en installeren. Als u pakketten wilt installeren in de globale omgeving, `activate` activeert u de root- of python2-omgeving met behulp van de opdracht als beheerder. Vervolgens u gebruik maken `conda` `pip` van een package manager like of om pakketten te installeren of bij te werken.

## <a name="python-linux-edition"></a>Python (Linux editie)

|    |           |
| ------------- | ------------- |
| Ondersteunde taalversies | Python 2.7 en 3.5 |
| Ondersteunde DSVM-edities      | Linux   |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | Er `conda` worden twee globale omgevingen gemaakt: <br /> * `root`omgeving gelegen `/anaconda/` op python 2.7. <br/> * `py35`omgeving gelegen `/anaconda/envs/py35`op python 3.5.       |
| Koppelingen naar voorbeelden      | Voorbeeld van Jupyter-notitieblokken voor Python zijn inbegrepen.     |
| Gerelateerde tools op de DSVM      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Hoe het te gebruiken en uit te voeren    

* Run in een terminal:

  Open de terminal en voer een van de volgende handelingen uit, afhankelijk van de versie van Python die u wilt uitvoeren:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Gebruik in een IDE:

  Gebruik PyCharm, geïnstalleerd in de Visual Studio Community-editie. 

* Gebruik in Jupyter:

  Open Jupyter en selecteer **Nieuw** om een nieuw notitieblok te maken. U het kerneltype instellen als **Python [Conda Root]** voor Python 2.7 en **Python [Conda env:py35]** voor de Python 3.5-omgeving. 

* Python-pakketten installeren:

  De standaard Python-omgevingen op de DSVM zijn globale omgevingen die door alle gebruikers kunnen worden gelezen. Maar alleen beheerders kunnen globale pakketten schrijven en installeren. Als u pakketten wilt installeren in de globale omgeving, activeert u de root- of py35-omgeving met behulp van de `source activate` opdracht als beheerder of als gebruiker met sudo-machtigingen. Vervolgens u gebruik maken `conda` `pip` van een package manager like of om pakketten te installeren of bij te werken.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Ondersteunde taalversies | Microsoft R Open 3.x (100% compatibel met CRAN-R)<br /> Microsoft R Server 9.x Developer edition (een schaalbaar R-platform dat klaar is voor een onderneming)|
| Ondersteunde DSVM-edities      | Linux, Windows     |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | Windows:`C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux:`/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Koppelingen naar voorbeelden      | Voorbeeld Jupyter-notitieblokken voor R zijn inbegrepen.     |
| Gerelateerde tools op de DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Hoe het te gebruiken en uit te voeren    

**Windows**:

* Uitvoeren met een opdrachtprompt:

  Open een opdrachtprompt en typ `R`.

* Gebruik in een IDE:

  Gebruik RTools voor Visual Studio (RTVS) geïnstalleerd in de Visual Studio Community-editie of RStudio. Deze zijn beschikbaar in het menu Start of als bureaubladpictogram. 

* Gebruik in Jupyter

  Open Jupyter en selecteer **Nieuw** om een nieuw notitieblok te maken. U het kerneltype instellen als **R** om de Jupyter R-kernel (IRKernel) te gebruiken.

* Installeer R-pakketten:

  R is geïnstalleerd op de DSVM in een wereldwijde omgeving die leesbaar is voor alle gebruikers. Maar alleen beheerders kunnen globale pakketten schrijven en installeren. Als u pakketten wilt installeren in de globale omgeving, voert u R uit met een van de voorgaande methoden. Vervolgens u de R-pakketbeheerder `install.packages()` uitvoeren om pakketten te installeren of bij te werken.

**Linux**:

* Run in terminal:

  Open een terminal `R`en ren.  

* Gebruik in een IDE:

  Gebruik RStudio, geïnstalleerd op de Linux DSVM.  

* Gebruik in Jupyter:

  Open Jupyter en selecteer **Nieuw** om een nieuw notitieblok te maken. U het kerneltype instellen als **R** om de Jupyter R-kernel (IRKernel) te gebruiken. 

* Installeer R-pakketten:

  R is geïnstalleerd op de DSVM in een wereldwijde omgeving die leesbaar is voor alle gebruikers. Maar alleen beheerders kunnen globale pakketten schrijven en installeren. Als u pakketten wilt installeren in de globale omgeving, voert u R uit met een van de voorgaande methoden. Vervolgens u de R-pakketbeheerder `install.packages()` uitvoeren om pakketten te installeren of bij te werken.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Ondersteunde taalversies | 0,6 |
| Ondersteunde DSVM-edities      | Linux, Windows     |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | Windows: geïnstalleerd op`C:\JuliaPro-VERSION`<br /> Linux: Geïnstalleerd op`/opt/JuliaPro-VERSION`    |
| Koppelingen naar voorbeelden      | Voorbeeld Jupyter notebooks voor Julia zijn inbegrepen.     |
| Gerelateerde tools op de DSVM      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Hoe het te gebruiken en uit te voeren    

**Windows**:

* Uitvoeren op een opdrachtprompt

  Open een opdrachtprompt `julia`en voer uit.
* Gebruik in een IDE:

  Gebruik `Juno` met de Julia IDE geïnstalleerd op de DSVM en beschikbaar als een desktop snelkoppeling.

* Gebruik in Jupyter:

  Open Jupyter en selecteer **Nieuw** om een nieuw notitieblok te maken. U het kerneltype instellen als **Julia VERSION**.

* Installeer Julia-pakketten:

  De standaard Locatie Julia is een globale omgeving die leesbaar is voor alle gebruikers. Maar alleen beheerders kunnen globale pakketten schrijven en installeren. Als u pakketten wilt installeren in de wereldwijde omgeving, voert u Julia uit met een van de voorgaande methoden. Vervolgens u Julia package manager-opdrachten uitvoeren, zoals `Pkg.add()` het installeren of bijwerken van pakketten.


**Linux**:
* Run in een terminal:

  Open een terminal `julia`en ren.
* Gebruik in een IDE:

  Gebruik `Juno`, met de Julia IDE geïnstalleerd op de DSVM en beschikbaar als een **applicatie** menu snelkoppeling.

* Gebruik in Jupyter:

  Open Jupyter en selecteer **Nieuw** om een nieuw notitieblok te maken. U het kerneltype instellen als **Julia VERSION**.

* Installeer Julia-pakketten:

  De standaard Locatie Julia is een globale omgeving die leesbaar is voor alle gebruikers. Maar alleen beheerders kunnen globale pakketten schrijven en installeren. Als u pakketten wilt installeren in de wereldwijde omgeving, voert u Julia uit met een van de voorgaande methoden. Vervolgens u Julia package manager-opdrachten uitvoeren, zoals `Pkg.add()` het installeren of bijwerken van pakketten.

## <a name="other-languages"></a>Andere talen

**C#**: Beschikbaar op Windows en toegankelijk via `Developer Command Prompt for Visual Studio`de Visual Studio `csc` Community-editie of op de, waar u de opdracht uitvoeren.

**Java**: OpenJDK is beschikbaar op zowel de Linux- als Windows-edities van de DSVM en is ingesteld op het pad. Als u Java `javac` wilt `java` gebruiken, typt u de opdracht of opdracht bij een opdrachtprompt in Windows of op de bash-shell in Linux.

**Node.js**: Node.js is beschikbaar op zowel de Linux- als Windows-edities van de DSVM en is ingesteld op het pad. Als u toegang wilt krijgen `node` `npm` tot Node.js, typt u de opdracht of opdracht bij een opdrachtprompt in Windows of op de bashshell in Linux. Op Windows is de Visual Studio-extensie voor de tools Node.js geïnstalleerd om een grafische IDE te bieden om uw Node.js-toepassing te ontwikkelen.

**F#**: Beschikbaar op Windows en toegankelijk via `Developer Command Prompt for Visual Studio`de Visual Studio `fsc` Community-editie of op een, waar u de opdracht uitvoeren.
