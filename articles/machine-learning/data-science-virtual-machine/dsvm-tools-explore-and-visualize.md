---
title: Hulpprogramma's voor exploratie en visualisatie van gegevens
titleSuffix: Azure Data Science Virtual Machine
description: Hulp middelen voor het verkennen en visualisatie van gegevens voor de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: 14c34a13440d50fcf42c2207f9933dc1de014a14
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191974"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Hulp middelen voor het verkennen en visualisatie van gegevens op de Azure-Data Science Virtual Machine

In data wetenschappen is de sleutel het begrijpen van de gegevens. Met hulpprogram ma's voor visualisatie en gegevens exploratie kunt u de gegevens beter leren gebruiken. De volgende hulpprogram ma's, die zijn opgenomen in de Data Science Virtual Machine (DSVM), maken deze sleutel stap eenvoudiger.

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Wat is het?   | Open-Source SQL-query-engine op big data    |
| Ondersteunde DSVM-versies      | Windows, Linux  |
| Hoe wordt het geconfigureerd en geïnstalleerd op de DSVM?      |  Alleen geïnstalleerd `/dsvm/tools/drill*` in de Inge sloten modus   |
| Wordt doorgaans gebruikt      |  Voor in-place gegevens onderzoek zonder extra heren, transformeren, laden (ETL). Query's uitvoeren op verschillende gegevens bronnen en indelingen, waaronder CSV, JSON, relationele tabellen en Hadoop.     |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad  <br/> [Aan de slag met boren in 10 minuten](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Gerelateerde hulpprogram ma's op de DSVM      |   Rattle, Fridge, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Wat is het?   |  Een verzameling machine learning-algoritmen voor gegevens analyse taken. Deze algoritmen kunnen rechtstreeks worden toegepast op een gegevensset of worden aangeroepen vanuit uw eigen Java-code. Fridge bevat hulpprogram ma's voor het voorverwerken van gegevens, classificatie, regressie, Clustering, koppelings regels en visualisatie. |
| Ondersteunde DSVM-edities     | Windows, Linux     |
| Wordt doorgaans gebruikt      | Algemeen hulp programma voor machine learning     |
| Hoe gebruiken en uitvoeren      | Zoeken op Windows, Weka in het menu Start. Meld u aan bij Linux met X2Go en ga vervolgens naar toepassingen > Development > Fridge. |
| Koppelingen naar voorbeelden      | [Voorbeelden van weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Gerelateerde hulpprogram ma's op de DSVM      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rammelaar
|    |           |
| ------------- | ------------- |
| Wat is het?   |   Een Graphical User Interface (GUI) voor gegevens analyse met R   |
| Ondersteunde DSVM-edities     | Windows, Linux     |
| Wordt doorgaans gebruikt      | Algemeen hulp programma voor UI-gegevens analyse voor R    |
| Hoe gebruiken en uitvoeren      | UI-hulpprogramma. Open in Windows een opdracht prompt, voer R uit en voer `rattle()`vervolgens in r uit. Op Linux maakt u verbinding met X2Go, start u een Terminal, voert u R uit en voert u `rattle()`vervolgens in r uit. |
| Koppelingen naar voorbeelden      | [Rammelaar](https://togaware.com/onepager/) |
| Gerelateerde hulpprogram ma's op de DSVM      |LightGBM, Fridge, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Wat is het?   | Interactieve gegevens visualisatie en BI-hulp programma    |
| Ondersteunde DSVM-versies      | Windows  |
| Wordt doorgaans gebruikt      |  Gegevens visualisatie en het maken van Dash boards   |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`()      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio 2019, Visual Studio code, Juno      |

