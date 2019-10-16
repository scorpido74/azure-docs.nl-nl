---
title: Hulpprogramma's voor exploratie en visualisatie van gegevens
titleSuffix: Azure Data Science Virtual Machine
description: Hulp middelen voor het verkennen en visualisatie van gegevens voor de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: a18c374df0cad7994af855089f1096d52fe30ce2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330705"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Hulp middelen voor het verkennen en visualisatie van gegevens op de Azure-Data Science Virtual Machine

In data wetenschappen is de sleutel het begrijpen van de gegevens. Met hulpprogram ma's voor visualisatie en gegevens exploratie kunt u de gegevens beter leren gebruiken. De volgende hulpprogram ma's, die zijn opgenomen in de Data Science Virtual Machine (DSVM), maken deze sleutel stap eenvoudiger.

## <a name="apache-drill"></a>Apache-detail weergave
|    |           |
| ------------- | ------------- |
| Wat is het?   | Open-Source SQL-query-engine op big data    |
| Ondersteunde DSVM-versies      | Windows, Linux  |
| Hoe wordt het geconfigureerd en geïnstalleerd op de DSVM?      |  Alleen in `/dsvm/tools/drill*` in de Inge sloten modus geïnstalleerd   |
| Typische toepassingen      |  Voor in-place gegevens onderzoek zonder extra heren, transformeren, laden (ETL). Query's uitvoeren op verschillende gegevens bronnen en indelingen, waaronder CSV, JSON, relationele tabellen en Hadoop.     |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad  <br/> [Aan de slag met boren in 10 minuten](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Gerelateerde hulpprogram ma's op de DSVM      |   Rattle, Fridge, SQL Server Management Studio      |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Wat is het?   | Interactieve gegevens visualisatie en BI-hulp programma    |
| Ondersteunde DSVM-versies      | Windows  |
| Typische toepassingen      |  Gegevens visualisatie en het maken van Dash boards   |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`) of gewoon uitvoeren vanuit het menu **Start** .      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio 2019, Visual Studio code, Juno      |

> [!NOTE]
> U hebt een Microsoft Office 365-account nodig om toegang te krijgen tot Power BI.


## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Wat is het?   |   Een Graphical User Interface (GUI) voor gegevens analyse met R   |
| Ondersteunde DSVM-edities     | Windows, Linux     |
| Typische toepassingen      | Algemeen hulp programma voor UI-gegevens analyse voor R    |
| Hoe gebruiken en uitvoeren      | Hulp programma gebruikers interface. Open in Windows een opdracht prompt, voer R uit en voer vervolgens in R `rattle()` uit. Op Linux maakt u verbinding met X2Go, start u een Terminal, voert u R uit en voert u in R de `rattle()` uit. |
| Koppelingen naar voor beelden      | [Rattle](https://togaware.com/onepager/) |
| Gerelateerde hulpprogram ma's op de DSVM      |LightGBM, Fridge, Xgboost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Wat is het?   |  Een verzameling machine learning-algoritmen voor gegevens analyse taken. Deze algoritmen kunnen rechtstreeks worden toegepast op een gegevensset of worden aangeroepen vanuit uw eigen Java-code. Fridge bevat hulpprogram ma's voor het voorverwerken van gegevens, classificatie, regressie, Clustering, koppelings regels en visualisatie. |
| Ondersteunde DSVM-edities     | Windows, Linux     |
| Typische toepassingen      | Algemeen hulp programma voor machine learning     |
| Hoe gebruiken en uitvoeren      | Zoek in Windows naar Fridge in het menu Start. Meld u aan bij Linux met X2Go en ga vervolgens naar toepassingen > Development > Fridge. |
| Koppelingen naar voor beelden      | [Fridge-voor beelden](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Gerelateerde hulpprogram ma's op de DSVM      |LightGBM, Rattle, Xgboost   |




