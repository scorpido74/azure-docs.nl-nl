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
ms.date: 03/01/2019
ms.openlocfilehash: a2af1fdd1ee461e3b3db613ff4a575649da2dfdc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827435"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services met R (preview-versie)

Machine Learning Services is een functie van Azure SQL Database die wordt gebruikt voor het uitvoeren van een Data Base-R-script. De functie bevat micro soft R-pakketten voor predictive analytics met hoge prestaties en machine learning. De relationele gegevens kunnen worden gebruikt in R-scripts via opgeslagen procedures, T-SQL-script met R-instructies of R-code met T-SQL.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services (met R) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
>
> De open bare preview is beschikbaar voor afzonderlijke data bases en elastische Pools met behulp van het op vCore gebaseerde aankoop model in de laag **Algemeen** en **bedrijfskritische** service. In deze eerste open bare preview worden de **grootschalige** en de implementatie optie **Managed instance** niet ondersteund. Momenteel is R de enige ondersteunde taal. Er is op dit moment geen ondersteuning voor Python.
>
> De preview is momenteel beschikbaar in de volgende regio's: Europa-west, Europa-noord, VS-West 2, VS-Oost, Zuid-Centraal VS, Noord-Centraal VS, Canada-centraal, Zuidoost-Azië, India-Zuid en Australië-zuidoost.
>
> [Meld u aan voor de preview-versie](#signup) hieronder.

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

Voer de volgende stappen uit om u aan te melden voor de open bare Preview:

1. Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/) voordat u begint.

2. Stuur een e-mailbericht naar Microsoft op [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) om u te registreren voor de openbare preview. De openbare preview van Machine Learning Services (met R) in SQL Database is niet standaard ingeschakeld.

Zodra u bent Inge schreven in het programma, wordt u door micro soft naar de open bare preview gewerkt en R in te scha kelen voor uw bestaande of nieuwe data base.

Machine Learning Services met R wordt niet aanbevolen voor de werk belasting van de productie tijdens de open bare preview.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de belangrijkste verschillen ten opzichte [van SQL Server machine learning Services](sql-database-machine-learning-services-differences.md).
- Zie de [Snelstartgids](sql-database-connect-query-r.md)voor meer informatie over het gebruik van R voor het opvragen van Azure SQL database machine learning Services (preview).
- Zie [eenvoudige r-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md)om aan de slag te gaan met een aantal eenvoudige r-scripts.
