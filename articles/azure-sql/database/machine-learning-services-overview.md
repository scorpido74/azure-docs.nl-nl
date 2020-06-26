---
title: Machine Learning Services met R (preview-versie)
description: In dit artikel wordt Azure SQL Database Machine Learning Services (met R) beschreven en wordt uitgelegd hoe het werkt.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0a11e2ba820797bac8ce93517841fd37a8256d2c
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85413056"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services met R (preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Machine Learning Services is een functie van Azure SQL Database die wordt gebruikt voor het uitvoeren van een Data Base-R-script. De functie bevat micro soft R-pakketten voor predictive analytics met hoge prestaties en machine learning. De relationele gegevens kunnen worden gebruikt in R-scripts via opgeslagen procedures, T-SQL-script met R-instructies of R-code met T-SQL.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="what-you-can-do-with-r"></a>Wat u kunt doen met R

Gebruik de kracht van de R-taal voor het leveren van geavanceerde analyses en machine learning in de data base. Deze mogelijkheid zorgt voor berekeningen en verwerking op de plaats waar de gegevens zich bevinden, u hoeft de gegevens niet op te halen via het netwerk. Daarnaast kunt u gebruikmaken van de kracht van ENTER prise R-pakketten voor het leveren van geavanceerde analyses op schaal.

Machine Learning-services bevatten een basisdistributie van R, overlapt met zakelijke R-pakketten van Microsoft. R-functies en -algoritmen van Microsoft zijn ontworpen voor zowel schaal als bruikbaarheid en bieden voorspellende analyses, statistische modellen, gegevensvisualisaties en toonaangevende algoritmen voor machine learning.

### <a name="r-packages"></a>R-pakketten

De meest voorkomende open source R-pakketten zijn vooraf geïnstalleerd in Machine Learning Services. De volgende R-pakketten van micro soft zijn ook opgenomen:

| R-pakket | Beschrijving|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Micro soft R open is de uitgebreide distributie van R van micro soft. Het is een volledig open-source platform voor statistische analyse en gegevens wetenschap. Het is gebaseerd op en 100% compatibel met R, en bevat aanvullende mogelijkheden voor verbeterde prestaties en reproduceer baarheid. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR is de primaire bibliotheek voor schaal bare R. functions in deze bibliotheek zijn een van de meest gebruikte functies. Gegevens transformaties en manipulatie, statistische samen vatting, visualisatie en vele vormen van modellen en analyses vindt u in deze bibliotheken. Daarnaast distribueren functies in deze bibliotheken werk belastingen automatisch over beschik bare kernen voor parallelle verwerking, met de mogelijkheid om te werken aan gegevens segmenten die worden gecoördineerd en beheerd door de berekenings engine. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML voegt machine learning-algoritmen toe om aangepaste modellen te maken voor tekst analyse, afbeeldings analyse en analyse van sentiment. |

Naast de vooraf geïnstalleerde pakketten kunt u [aanvullende pakketten installeren](machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview-closed"></a>Meld u aan voor de preview-versie (gesloten)

> [!IMPORTANT]
> Registreren voor Azure SQL Database Machine Learning Services (preview) is momenteel **gesloten**.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de belangrijkste verschillen ten opzichte [van SQL Server machine learning Services](machine-learning-services-differences.md).
- Zie de [Snelstartgids](connect-query-r.md)voor meer informatie over het gebruik van R voor het opvragen van Azure SQL database machine learning Services (preview).
- Zie [eenvoudige r-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview)](r-script-create-quickstart.md)om aan de slag te gaan met een aantal eenvoudige r-scripts.
 