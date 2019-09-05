---
title: Machine learning en data science-hulpprogramma's - Azure | Microsoft Docs
description: Meer informatie over de machine learning-hulpprogramma's en frameworks die vooraf zijn geïnstalleerd op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: 3b05d076a61593e8c34ce642f2fe2fd137e07109
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565243"
---
# <a name="machine-learning-and-data-science-tools"></a>Machine learning en data science-hulpprogramma 's
Azure Data Science Virtual Machines is een uitgebreide set hulpprogramma's en bibliotheken voor machine learning (ML) beschikbaar in populaire talen, zoals Python, R en Julia. 

Hier volgen enkele van de hulpprogramma's voor ML en -bibliotheken op virtuele Machines voor Datatechnologie. 

## <a name="azure-machine-learninghttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>[Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml) SDK
|    |           |
| ------------- | ------------- |
| Wat is het?   |   Azure Machine Learning is een cloudservice die u gebruiken kunt om te ontwikkelen en implementeren van ML-modellen. Als u bouwen, trainen, schalen en beheren met behulp van de Python-SDK, kunt u uw modellen bijhouden. Modellen als containers implementeren en deze in de cloud, on-premises of in Azure IoT Edge uitvoeren.   |
| Ondersteunde versies     | Windows (Conda-omgeving: AzureML), Linux (Conda-omgeving: py36)    |
| Wordt doorgaans gebruikt      | Algemene ML-platform      |
| Hoe wordt deze geconfigureerd of geïnstalleerd?      |  Geïnstalleerd met GPU-ondersteuning   |
| Over het gebruik of uit te voeren      | Als Python SDK en Azure CLI. Activeren naar het conda-omgeving `AzureML` op Windows-editie *of* naar `py36` op Linux-editie.      |
| Koppeling naar voorbeelden      | Voorbeeld van Jupyter notebooks zijn opgenomen in de `AzureML` map onder notitieblokken.  |
| Meer hulpprogramma 's      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Wat is het?   |    XGBoost is een snelle, draagbare en gedistribueerde verloop versterking (GBDT, GBRT of GBM)-bibliotheek voor Python, R, Java, Scala, C++ en meer. Deze wordt uitgevoerd op een enkele machine, Hadoop en Spark.    |
| Ondersteunde versies     | Windows, Linux     |
| Wordt doorgaans gebruikt      | Algemene ML-bibliotheek      |
| Hoe wordt deze geconfigureerd of geïnstalleerd?      |  Geïnstalleerd met GPU-ondersteuning   |
| Over het gebruik of uit te voeren      | Als Python-bibliotheek (2.7 en 3.5), R-pakket en de pad-opdrachtregelprogramma (`C:\dsvm\tools\xgboost\bin\xgboost.exe` voor Windows, `/dsvm/tools/xgboost/xgboost` voor Linux)    |
| Koppelingen naar voorbeelden      | Voorbeelden zijn opgenomen op de virtuele machine, in `/dsvm/tools/xgboost/demo` op Linux, en `C:\dsvm\tools\xgboost\demo` op Windows.   |
| Meer hulpprogramma 's      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Wat is het?   |   Vowpal Wabbit (ook wel bekend als ' VW") is een open-source, snelle, out-van-core learning-bibliotheek.    |
| Ondersteunde versies     | Windows, Linux     |
| Wordt doorgaans gebruikt      | Algemene ML-bibliotheek      |
| Hoe wordt deze geconfigureerd of geïnstalleerd?      |  Windows - msi-installatieprogramma, Linux--apt-get |
| Over het gebruik of uit te voeren      | Als een opdrachtregelprogramma voor het pad (`C:\Program Files\VowpalWabbit\vw.exe` op Windows, `/usr/bin/vw` op Linux)    |
| Koppeling naar voorbeelden      | [VowPal Wabbit-voorbeelden](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Meer hulpprogramma 's      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Wat is het?   |  Weka is een verzameling van ML-algoritmes voor datamining taken. De algoritmen kunnen worden toegepast op een set gegevens rechtstreeks of met de naam van uw eigen Java-code. Weka bevat hulpprogramma's voor het vooraf verwerken van gegevens, classificatie, regressie, clustering, koppelingsregels voor en visualisatie. |
| Ondersteunde versies     | Windows, Linux     |
| Wordt doorgaans gebruikt      | Algemene ML-hulpprogramma     |
| Over het gebruik of uit te voeren      | Zoeken op Windows, Weka in het menu Start. Meld u aan met X2Go op Linux, en ga vervolgens naar **toepassingen** > **ontwikkeling** > **Weka**. |
| Koppeling naar voorbeelden      | [Voorbeelden van weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Meer hulpprogramma 's      |LightGBM, ratel, XGBoost   |

## <a name="rattle"></a>Rammelaar
|    |           |
| ------------- | ------------- |
| Wat is het?   |   Rammelaar is een grafische gebruikersinterface voor data-gegevensanalyse met behulp van R.   |
| Ondersteunde versies     | Windows, Linux     |
| Wordt doorgaans gebruikt      | Hulpprogramma voor algemene UI datamining voor R    |
| Over het gebruik of uit te voeren      | UI-hulpprogramma. Op Windows, start u een opdrachtprompt, R uitvoeren en voer vervolgens in R `rattle()`. Op Linux, verbinding maken met X2Go, start een terminal, R uitvoeren en voer vervolgens in R `rattle()`. |
| Koppeling naar voorbeelden      | [Rammelaar](https://togaware.com/onepager/) |
| Meer hulpprogramma 's      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Wat is het?   | LightGBM is een snelle, gedistribueerde, hoogwaardige verloop versterking (GBDT, GBRT, GBM of DATAMART) framework op basis van algoritmen voor een beslissingsstructuur. Het wordt gebruikt voor classificatie, classificatie en vele andere ML-taken uitvoeren.    |
| Ondersteunde versies      | Windows, Linux    |
| Wordt doorgaans gebruikt      | Voor algemene doeleinden kleurovergang Gradient boosting-framework      |
| Hoe wordt deze geconfigureerd of geïnstalleerd?      | LightGBM is geïnstalleerd op Windows, als een Python-pakket. Op Linux, uitvoerbare bestand van de opdrachtregel wordt `/opt/LightGBM/lightgbm`, het R-pakket is geïnstalleerd en Python-pakketten zijn geïnstalleerd.     |
| Koppeling naar voorbeelden      | [Handleiding voor LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Meer hulpprogramma 's      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Wat is het?   | H2O is een open-source AI-platform die ondersteuning biedt voor in het geheugen, gedistribueerde, snelle en schaalbare ML.  |
| Ondersteunde versies      | Linux   |
| Wordt doorgaans gebruikt      | Voor algemeen gebruik gedistribueerde, schaalbare ML   |
| Hoe wordt deze geconfigureerd of geïnstalleerd?      | H2O is geïnstalleerd in `/dsvm/tools/h2o`.      |
| Over het gebruik of uit te voeren      | Verbinding maken met de virtuele machine met behulp van X2Go. Start een nieuwe terminal en voer `java -jar /dsvm/tools/h2o/current/h2o.jar`. Vervolgens start u een webbrowser en maak verbinding met `http://localhost:54321`.      |
| Koppeling naar voorbeelden      | Voorbeelden zijn beschikbaar op de virtuele machine in Jupyter onder de `h2o` directory.      |
| Meer hulpprogramma 's      | Apache Spark, MXNet, XGBoost, mousserende Water, Deep Water    |

Er zijn verschillende andere ML-bibliotheken op virtuele Machines voor Datatechnologie, zoals het populaire `scikit-learn` pakket dat wordt geleverd als onderdeel van de Anaconda Python-distributie die geïnstalleerd op virtuele Machines voor Datatechnologie. Om te zien wat de lijst met pakketten die beschikbaar zijn in Python, voert R en Julia, u de respectieve Pakketbeheer.
