---
title: Machine Learning Services met R (preview-versie)
description: In dit artikel wordt Azure SQL Database Machine Learning Services (met R) beschreven en wordt uitgelegd hoe het werkt.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ms.openlocfilehash: ca223de2bc0b26e4968d400ea418761a399dacae
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364595"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services met R (preview-versie)

Machine Learning Services is een functie van Azure SQL Database die wordt gebruikt voor het uitvoeren van een Data Base-R-script. De functie bevat micro soft R-pakketten voor predictive analytics met hoge prestaties en machine learning. De relationele gegevens kunnen worden gebruikt in R-scripts via opgeslagen procedures, T-SQL-script met R-instructies of R-code met T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> Het voor beeld is beschikbaar voor afzonderlijke data bases en elastische Pools met behulp van het op vCore gebaseerde aankoop model in de laag **Algemeen** en **bedrijfskritische** service. In deze eerste preview worden de service tier **grootschalige** en de implementatie optie **Managed instance** niet ondersteund. Momenteel is R de enige ondersteunde taal. Er is op dit moment geen ondersteuning voor Python.
>
> De preview is momenteel beschikbaar in de volgende regio's: Europa-west, Europa-noord, VS-West 2, VS-Oost, Zuid-Centraal VS, Noord-Centraal VS, Canada-centraal, Zuidoost-Azië, India-Zuid en Australië-zuidoost.

## <a name="what-you-can-do-with-r"></a>Wat u kunt doen met R

Gebruik de kracht van de taal R om geavanceerde analyses en in-database machine learning te bieden. Deze mogelijkheid zorgt voor berekeningen en verwerking op de plaats waar de gegevens zich bevinden, u hoeft de gegevens niet op te halen via het netwerk. Daarnaast kunt u gebruikmaken van de kracht van ENTER prise R-pakketten voor het leveren van geavanceerde analyses op schaal.

Machine Learning-services bevatten een basisdistributie van R, overlapt met zakelijke R-pakketten van Microsoft. R-functies en -algoritmen van Microsoft zijn ontworpen voor zowel schaal als bruikbaarheid en bieden voorspellende analyses, statistische modellen, gegevensvisualisaties en toonaangevende algoritmen voor machine learning.

### <a name="r-packages"></a>R-pakketten

De meest voorkomende open source R-pakketten zijn vooraf geïnstalleerd in Machine Learning Services. De volgende R-pakketten van micro soft zijn ook opgenomen:

| R-pakket | Beschrijving|
|-|-|
| [Micro soft R open](https://mran.microsoft.com/rro) | Micro soft R open is de uitgebreide distributie van R van micro soft. Het is een volledig open-source platform voor statistische analyse en gegevens wetenschap. Het is gebaseerd op en 100% compatibel met R, en bevat aanvullende mogelijkheden voor verbeterde prestaties en reproduceer baarheid. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR is de primaire bibliotheek voor schaal bare R. functions in deze bibliotheek zijn een van de meest gebruikte functies. Gegevens transformaties en manipulatie, statistische samen vatting, visualisatie en vele vormen van modellen en analyses vindt u in deze bibliotheken. Daarnaast distribueren functies in deze bibliotheken werk belastingen automatisch over beschik bare kernen voor parallelle verwerking, met de mogelijkheid om te werken aan gegevens segmenten die worden gecoördineerd en beheerd door de berekenings engine. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML voegt machine learning-algoritmen toe om aangepaste modellen te maken voor tekst analyse, afbeeldings analyse en analyse van sentiment. |

Naast de vooraf geïnstalleerde pakketten kunt u [aanvullende pakketten installeren](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Registreren voor de preview-versie

> [!IMPORTANT]
> Registreren voor Azure SQL Database Machine Learning Services (preview) is momenteel gesloten.

Machine Learning Services met R wordt niet aanbevolen voor de werk belasting van de productie tijdens de preview-periode.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de belangrijkste verschillen ten opzichte [van SQL Server machine learning Services](sql-database-machine-learning-services-differences.md).
- Zie de [Snelstartgids](sql-database-connect-query-r.md)voor meer informatie over het gebruik van R voor het opvragen van Azure SQL database machine learning Services (preview).
- Zie [eenvoudige r-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md)om aan de slag te gaan met een aantal eenvoudige r-scripts.
