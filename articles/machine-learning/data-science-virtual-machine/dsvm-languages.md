---
title: Ondersteunde talen
titleSuffix: Azure Data Science Virtual Machine
description: De ondersteunde programma talen en gerelateerde hulpprogram ma's die vooraf zijn geïnstalleerd op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 573b6b75b4534789e4e40fa3d6cc1694c5ede2ea
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192161"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Talen die worden ondersteund op de Data Science Virtual Machine 

De Data Science Virtual Machine (DSVM) wordt geleverd met diverse vooraf gemaakte talen en ontwikkelhulpprogramma's voor het bouwen van uw AI-toepassingen. Hier volgen enkele van de meest kenmerkende labels. 

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016-editie)

|    |           |
| ------------- | ------------- |
| Taalversies ondersteund | 2.7 en 3.6 |
| Ondersteunde DSVM-edities      | Windows Server 2016     |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | Twee algemene `conda` omgevingen worden gemaakt. <br /> * `root` omgeving zich bevindt in `/anaconda/` Python 3.6 is. <br/> * `python2` omgeving zich bevindt in `/anaconda/envs/python2`wordt Python 2.7       |
| Koppelingen naar voorbeelden      | Voorbeeld Jupyter-notebooks voor Python zijn opgenomen     |
| Meer hulpprogramma's op de DSVM      | PySpark, R, Julia      |

> [!NOTE]
> Windows Server 2016 die zijn gemaakt vóór maart 2018 bevat Python 3.5 en Python 2.7. Python 2.7 is ook het conda **hoofdmap** omgeving en **py35** is de omgeving voor Python 3.5. 

### <a name="how-to-use--run-it"></a>Hoe kan ik gebruik / uitvoeren?    

* Uitgevoerd in de opdrachtprompt

Opdrachtprompt openen en doe het volgende, afhankelijk van de versie van Python die u wilt uitvoeren. 

```
# To run Python 2.7
activate python2
python --version

# To run Python 3.6
activate 
python --version

```
* Met behulp van een IDE

Gebruik Python Tools voor Visual Studio (PTVS) in de Visual Studio Community-editie geïnstalleerd. De installatie van de enige omgeving automatisch in PTVS standaard is Python 3.6. 

> [!NOTE]
> Zodat ze de PTVS naar Python 2.7, moet u een aangepaste omgeving maken in PTVS. Deze omgeving paden instellen in de Community Edition van Visual Studio, gaat u naar **extra** -> **Python Tools** -> **Python-omgevingen** en klik vervolgens op **+ aangepast**. Stel de locatie op `c:\anaconda\envs\python2` en klik vervolgens op _automatisch detecteren_. 

* Gebruik in Jupyter

Jupyter Open en klik op de `New` om een nieuwe notebook maken. Op dit moment kunt u het type kernel als _Python [Conda Root]_ voor Python 3.6 en _Python [Conda env:python2]_ voor Python 2.7-omgeving. 

* Python-pakketten installeren

De standaard Python-omgevingen op de DSVM zijn globale omgeving kan worden gelezen door alle gebruikers. Maar alleen beheerders kunnen schrijven / algemene pakketten installeren. Activeren om het pakket naar de globale omgeving installeert, naar de hoofdmap of python2-omgeving met de `activate` opdracht als Administrator. Vervolgens kunt u de package manager, zoals `conda` of `pip` installeren of bijwerken van pakketten. 

## <a name="python-linux-and-windows-server-2012-edition"></a>Python (Linux en Windows Server 2012-editie)

|    |           |
| ------------- | ------------- |
| Taalversies ondersteund | 2.7 en 3.5 |
| Ondersteunde DSVM-edities      | Linux, Windows Server 2012    |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | Twee algemene `conda` omgevingen worden gemaakt. <br /> * `root` omgeving zich bevindt in `/anaconda/` Python 2.7 is. <br/> * `py35` omgeving zich bevindt in `/anaconda/envs/py35`wordt Python 3.5       |
| Koppelingen naar voorbeelden      | Voorbeeld Jupyter-notebooks voor Python zijn opgenomen     |
| Meer hulpprogramma's op de DSVM      | PySpark, R, Julia      |
### <a name="how-to-use--run-it"></a>Hoe kan ik gebruik / uitvoeren?    

**Linux**
* Uitgevoerd in de terminal

Open de terminal en doe het volgende, afhankelijk van de versie van Python die u wilt uitvoeren. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* Met behulp van een IDE

Gebruik PyCharm geïnstalleerd in de Visual Studio Community-editie. 

* Gebruik in Jupyter

Jupyter Open en klik op de `New` om een nieuwe notebook maken. Op dit moment kunt u het type kernel als _Python [Conda Root]_ voor Python 2.7 en _Python [Conda env:py35]_ voor Python 3.5-omgeving. 

* Python-pakketten installeren

De standaard Python-omgevingen op de DSVM zijn algemene omgevingen kan worden gelezen door alle gebruikers. Maar alleen beheerders kunnen schrijven / algemene pakketten installeren. Activeren om het pakket naar de globale omgeving installeert, naar de hoofdmap of py35 omgeving met de `source activate` opdracht als Administrator of een gebruiker met sudo-machtigingen. Vervolgens kunt u een pakketbeheerder zoals `conda` of `pip` installeren of bijwerken van pakketten. 

**Windows 2012**
* Uitgevoerd in de opdrachtprompt

Opdrachtprompt openen en doe het volgende, afhankelijk van de versie van Python die u wilt uitvoeren. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* Met behulp van een IDE

Gebruik Python Tools voor Visual Studio (PTVS) in de Visual Studio Community-editie geïnstalleerd. De instellingen van de enige omgeving automatisch in PTVS in Python 2.7. 
> [!NOTE]
> Zodat ze de PTVS naar Python 3.5, moet u een aangepaste omgeving maken in PTVS. Deze omgeving paden instellen in de Community Edition van Visual Studio, gaat u naar **extra** -> **Python Tools** -> **Python-omgevingen** en klik vervolgens op **+ aangepast**. Stel de locatie op `c:\anaconda\envs\py35` en klik vervolgens op _automatisch detecteren_. 

* Gebruik in Jupyter

Jupyter Open en klik op de `New` om een nieuwe notebook maken. Op dit moment kunt u het type kernel als _Python [Conda Root]_ voor Python 2.7 en _Python [Conda env:py35]_ voor Python 3.5-omgeving. 

* Python-pakketten installeren

De standaard Python-omgevingen op de DSVM zijn globale omgeving kan worden gelezen door alle gebruikers. Maar alleen beheerders kunnen schrijven / algemene pakketten installeren. Activeren om het pakket naar de globale omgeving installeert, naar de hoofdmap of py35 omgeving met de `activate` opdracht als Administrator. Vervolgens kunt u de package manager, zoals `conda` of `pip` installeren of bijwerken van pakketten. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Taalversies ondersteund | Microsoft R Open 3.x (100% compatibel is met CRAN R<br /> Microsoft R Server 9.x Developer edition (een schaalbare gereed R platform)|
| Ondersteunde DSVM-edities      | Linux, Windows     |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Koppelingen naar voorbeelden      | Voorbeeld Jupyter-notebooks voor R zijn opgenomen     |
| Meer hulpprogramma's op de DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use--run-it"></a>Hoe kan ik gebruik / uitvoeren?    

**Windows**:

* Uitgevoerd in de opdrachtprompt

Open de opdrachtprompt en typ `R`.

* Met behulp van een IDE

Gebruik RTools voor Visual Studio (RTVS) geïnstalleerd in de Visual Studio Community edition of RStudio. Deze zijn beschikbaar in het startmenu of als een pictogram op het bureaublad. 

* Gebruik in Jupyter

Jupyter Open en klik op de `New` om een nieuwe notebook maken. Op dit moment kunt u het type kernel als _R_ Jupyter R kernel (IRKernel) gebruiken. 

* R-pakketten installeren

R is geïnstalleerd op de DSVM in een globale omgeving kan worden gelezen door alle gebruikers. Maar alleen beheerders kunnen schrijven / algemene pakketten installeren. Om het pakket naar de globale omgeving installeert, R, met behulp van een van de methoden die hierboven worden uitgevoerd. Vervolgens kunt u het R-Pakketbeheer uitvoeren `install.packages()` installeren of bijwerken van pakketten. 

**Linux**:

* Uitgevoerd in de terminal

Open terminal en alleen uitvoeren `R`.  

* Met behulp van een IDE

RStudio geïnstalleerd op de Linux-DSVM gebruiken.  

* Gebruik in Jupyter

Jupyter Open en klik op de `New` om een nieuwe notebook maken. Op dit moment kunt u het type kernel als _R_ Jupyter R kernel (IRKernel) gebruiken. 

* R-pakketten installeren

R is geïnstalleerd op de DSVM in een globale omgeving kan worden gelezen door alle gebruikers. Maar alleen beheerders kunnen schrijven / algemene pakketten installeren. Om het pakket naar de globale omgeving installeert, R, met behulp van een van de methoden die hierboven worden uitgevoerd. Vervolgens kunt u het R-Pakketbeheer uitvoeren `install.packages()` installeren of bijwerken van pakketten. 


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Taalversies ondersteund | 0,6 |
| Ondersteunde DSVM-edities      | Linux, Windows     |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | Windows: Geïnstalleerd op`C:\JuliaPro-VERSION`<br /> Linux: Geïnstalleerd op`/opt/JuliaPro-VERSION`    |
| Koppelingen naar voorbeelden      | Voorbeeld Jupyter-notebooks voor Julia zijn opgenomen     |
| Meer hulpprogramma's op de DSVM      | Python, R      |
### <a name="how-to-use--run-it"></a>Hoe kan ik gebruik / uitvoeren?    

**Windows**:

* Uitgevoerd in de opdrachtprompt

Open de opdrachtprompt en voert `julia`. 
* Met behulp van een IDE

Gebruik `Juno` de IDE Julia geïnstalleerd op de DSVM en is beschikbaar als een snelkoppeling op het bureaublad.

* Gebruik in Jupyter

Jupyter Open en klik op de `New` om een nieuwe notebook maken. Op dit moment kunt u het type kernel als `Julia VERSION` 

* Julia pakketten installeren

De standaardwaarde van Julia locatie is een globale omgeving kan worden gelezen door alle gebruikers. Maar alleen beheerders kunnen schrijven / algemene pakketten installeren. Als u wilt installeren pakket aan de globale omgeving, Julia met behulp van een van de methoden die hierboven worden uitgevoerd. Vervolgens kunt u het pakket Julia manager opdrachten, zoals uitvoeren `Pkg.add()` installeren of bijwerken van pakketten. 


**Linux**:
* Uitgevoerd in de terminal.

Open terminal en alleen uitvoeren `julia`. 
* Met behulp van een IDE

Gebruik `Juno` de Julia IDE die is geïnstalleerd op de DSVM en die beschikbaar is als snelkoppeling voor een toepassings menu.

* Gebruik in Jupyter

Jupyter Open en klik op de `New` om een nieuwe notebook maken. Op dit moment kunt u het type kernel als `Julia VERSION` 

* Julia pakketten installeren

De standaardwaarde van Julia locatie is een globale omgeving kan worden gelezen door alle gebruikers. Maar alleen beheerders kunnen schrijven / algemene pakketten installeren. Als u wilt installeren pakket aan de globale omgeving, Julia met behulp van een van de methoden die hierboven worden uitgevoerd. Vervolgens kunt u het pakket Julia manager opdrachten, zoals uitvoeren `Pkg.add()` installeren of bijwerken van pakketten. 

## <a name="other-languages"></a>Andere talen

**C#** : Beschikbaar in Windows en toegankelijk via de Visual Studio Community-editie of op `Developer Command Prompt for Visual Studio` een locatie waarop u de `csc` opdracht gewoon kunt uitvoeren. 

**Java**: OpenJDK is beschikbaar in zowel de Linux-als de Windows-versie van de DSVM en is ingesteld op het pad. U kunt typen `javac` of `java` opdracht op de opdrachtprompt in Windows- of bash-shell in Linux Java gebruiken. 

**node.js**: node.js is beschikbaar op Linux- en Windows-versie van de DSVM en instellen op het pad. U kunt typen `node` of `npm` opdracht op de opdrachtprompt in de Windows- of bash-shell in Linux voor toegang tot node.js. De Node.js-hulpprogramma's voor Visual Studio-extensie is geïnstalleerd op Windows, voor een grafische IDE voor het ontwikkelen van uw node.js-toepassing. 

**F#** : Beschikbaar in Windows en toegankelijk via de Visual Studio Community-editie of op `Developer Command Prompt for Visual Studio` een locatie waarop u de `fsc` opdracht gewoon kunt uitvoeren. 


