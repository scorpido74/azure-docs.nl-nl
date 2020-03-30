---
title: Machine learning en data science tools
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de machine learning-tools en frameworks die vooraf zijn geïnstalleerd op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c414087270558e21340e50114c0563ff7e50064c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282304"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Machine learning- en data science-tools op Azure Data Science Virtual Machines
Azure Data Science Virtual Machines (DSVMs) hebben een uitgebreide set hulpprogramma's en bibliotheken voor machine learning die beschikbaar zijn in populaire talen, zoals Python, R en Julia.

Hier zijn enkele van de machine-learning tools en bibliotheken op DSVMs.

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning-SDK voor Python

Bekijk de volledige referentie voor de [Azure Machine Learning SDK voor Python.](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)

|    |           |
| ------------- | ------------- |
| Wat is het?   |   Azure Machine Learning is een cloudservice die u gebruiken om machine learning-modellen te ontwikkelen en te implementeren. U uw modellen volgen terwijl u ze bouwt, traint, schaalt en beheert met behulp van de Python SDK. Implementeer modellen als containers en voer ze uit in de cloud, on-premises of op Azure IoT Edge.   |
| Ondersteunde edities     | Windows (conda-omgeving: AzureML), Linux (conda-omgeving: py36)    |
| Typische toepassingen      | Algemeen machineleerplatform      |
| Hoe is het geconfigureerd of geïnstalleerd?      |  Geïnstalleerd met GPU-ondersteuning   |
| Hoe het te gebruiken of uit te voeren      | Als Python SDK en in de Azure CLI. Activeer naar de conda-omgeving `AzureML` op Windows-editie *of* naar `py36` op Linux-editie.      |
| Koppeling naar voorbeelden      | Voorbeeld-Jupyter-notitieblokken `AzureML` zijn opgenomen in de map onder notitieblokken.  |
| Gerelateerde tools      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| Wat is het?   | Een open-source AI-platform dat in-memory, distributed, fast en scalable machine learning ondersteunt.  |
| Ondersteunde versies      | Linux   |
| Typische toepassingen      | Gedistribueerde, schaalbare machine learning voor algemene doeleinden   |
| Hoe is het geconfigureerd of geïnstalleerd?      | H2O is `/dsvm/tools/h2o`geïnstalleerd in .      |
| Hoe het te gebruiken of uit te voeren      | Maak verbinding met de VM met X2Go. Start een nieuwe terminal `java -jar /dsvm/tools/h2o/current/h2o.jar`en voer uit. Start vervolgens een webbrowser `http://localhost:54321`en maak verbinding met .      |
| Koppeling naar voorbeelden      | Voorbeelden zijn beschikbaar op de VM in `h2o` Jupyter onder de directory.      |
| Gerelateerde tools      | Apache Spark, MXNet, XGBoost, Bruisend Water, Diep water    |

Er zijn verschillende andere machine learning-bibliotheken op DSVMs, zoals het populaire `scikit-learn` pakket dat deel uitmaakt van de Anaconda Python-distributie voor DSVMs. Als u de lijst met pakketten wilt bekijken die beschikbaar zijn in Python, R en Julia, voert u de respectievelijke pakketbeheerders uit.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Wat is het?   | Een snel, gedistribueerd, high-performance gradient-boosting (GBDT, GBRT, GBM of MART) framework op basis van decision tree-algoritmen. Het wordt gebruikt voor het rangschikken, classificatie, en vele andere machine-learning taken.    |
| Ondersteunde versies      | Windows, Linux    |
| Typische toepassingen      | Kader voor het stimuleren van gradiënten voor algemene doeleinden      |
| Hoe is het geconfigureerd of geïnstalleerd?      | Op Windows is LightGBM geïnstalleerd als een Python-pakket. Op Linux is de command-line `/opt/LightGBM/lightgbm`executable in, het R-pakket is geïnstalleerd en Python-pakketten zijn geïnstalleerd.     |
| Koppeling naar voorbeelden      | [LightGBM-gids](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Gerelateerde tools      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Wat is het?   |   Een grafische gebruikersinterface voor datamining met Behulp van R.   |
| Ondersteunde edities     | Windows, Linux     |
| Typische toepassingen      | Algemene UI-tool voor gegevensmining voor R    |
| Hoe het te gebruiken of uit te voeren      | Als ui-tool. Start in Windows een opdrachtprompt, voer R uit `rattle()`en voer vervolgens in R uit. Maak op Linux verbinding met X2Go, start een terminal, `rattle()`voer R uit en voer vervolgens binnen R uit. |
| Koppeling naar voorbeelden      | [Rattle](https://togaware.com/onepager/) |
| Gerelateerde tools      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Wat is het?   |   Een snelle, open-source, out-of-core leersysteembibliotheek    |
| Ondersteunde edities     | Windows, Linux     |
| Typische toepassingen      | Algemene machinelearningbibliotheek      |
| Hoe is het geconfigureerd of geïnstalleerd?      |  Windows: msi installer<br/>Linux: apt-get |
| Hoe het te gebruiken of uit te voeren      | Als een on-path command-line tool (op`C:\Program Files\VowpalWabbit\vw.exe` Windows, `/usr/bin/vw` op Linux)    |
| Koppeling naar voorbeelden      | [VowPal Wabbit monsters](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Gerelateerde tools      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Wat is het?   |  Een verzameling van machine learning-algoritmen voor dataminingtaken. De algoritmen kunnen direct worden toegepast op een gegevensset of worden aangeroepen vanuit uw eigen Java-code. Weka bevat hulpprogramma's voor gegevens voorverwerking, classificatie, regressie, clustering, associatieregels en visualisatie. |
| Ondersteunde edities     | Windows, Linux     |
| Typische toepassingen      | Algemeen machineleerinstrument     |
| Hoe het te gebruiken of uit te voeren      | Zoek in Windows naar Weka in het **menu Start.** Meld u op Linux aan met X2Go en ga vervolgens naar **Applications** > **Development** > **Weka**. |
| Koppeling naar voorbeelden      | [Weka monsters](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Gerelateerde tools      |LightGBM, rammelaar, XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Wat is het?   |   Een snelle, draagbare en gedistribueerde bibliotheek voor het verhogen van verlooppercentages (GBDT, GBRT of GBM) voor Python, R, Java, Scala, C++en meer. Het draait op een enkele machine, en op Apache Hadoop en Spark.    |
| Ondersteunde edities     | Windows, Linux     |
| Typische toepassingen      | Algemene machinelearningbibliotheek      |
| Hoe is het geconfigureerd of geïnstalleerd?      |  Geïnstalleerd met GPU-ondersteuning   |
| Hoe het te gebruiken of uit te voeren      | Als Python-bibliotheek (2,7 en 3,5), R-pakket en on-path command-line tool (voor`C:\dsvm\tools\xgboost\bin\xgboost.exe` Windows en `/dsvm/tools/xgboost/xgboost` voor Linux)    |
| Koppelingen naar voorbeelden      | Voorbeelden zijn opgenomen op `/dsvm/tools/xgboost/demo` de VM, `C:\dsvm\tools\xgboost\demo` in op Linux, en op Windows.   |
| Gerelateerde tools      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Wat is het?   | Open-source SQL-queryengine op big data    |
| Ondersteunde DSVM-versies      | Windows 2019, Linux  |
| Hoe is het geconfigureerd en geïnstalleerd op de DSVM?      |  Alleen `/dsvm/tools/drill*` geïnstalleerd in de ingesloten modus   |
| Typische toepassingen      |  Voor gegevensverkenning op de plaats zonder extract, transformatie, belasting (ETL) te vereisen. Query verschillende gegevensbronnen en -indelingen, waaronder CSV, JSON, relationele tabellen en Hadoop.     |
| Hoe het te gebruiken en uit te voeren      | Snelkoppeling bureaublad  <br/> [Over 10 minuten aan de slag met Drill](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Gerelateerde tools op de DSVM      |   Rattle, Weka, SQL Server Management Studio      |


