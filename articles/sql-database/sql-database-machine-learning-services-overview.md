---
title: Machine Learning Services met R (voorbeeld)
description: In dit artikel wordt beschreven hoe Azure SQL Database Machine Learning Services (met R) en wordt uitgelegd hoe het werkt.
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
ms.openlocfilehash: 2a2cd4bfc3d393543b41eea776f02723d94054b1
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345834"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services with R (preview)

Machine Learning Services is een functie van Azure SQL Database, die wordt gebruikt voor het uitvoeren van in-database R-scripts. De functie bevat Microsoft R-pakketten voor krachtige voorspellende analyses en machine learning. De relationele gegevens kunnen worden gebruikt in R-scripts via opgeslagen procedures, T-SQL-script met R-instructies of R-code die T-SQL bevat.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> De preview is beschikbaar voor afzonderlijke databases en elastische pools met behulp van het vCore-gebaseerde inkoopmodel in de **algemene en** **bedrijfskritieke** servicelagen. In dit eerste voorbeeld worden de **hyperscale** servicelaag en de optie **beheerde instantieimplementatie** niet ondersteund. Momenteel is R de enige ondersteunde taal. Er is op dit moment geen ondersteuning voor Python.
>
> De preview is momenteel beschikbaar in de volgende regio's: West-Europa, Noord-Europa, West-VS 2, Oost-VS, South Central US, North Central US, Canada Central, Zuidoost-Azië, India Zuid en Australië Zuidoost.

## <a name="what-you-can-do-with-r"></a>Wat u doen met R

Gebruik de kracht van de taal R om geavanceerde analyses en in-database machine learning te bieden. Deze mogelijkheid zorgt voor berekeningen en verwerking op de plaats waar de gegevens zich bevinden, u hoeft de gegevens niet op te halen via het netwerk. Ook u gebruik maken van de kracht van enterprise R-pakketten om geavanceerde analyses op schaal te leveren.

Machine Learning-services bevatten een basisdistributie van R, overlapt met zakelijke R-pakketten van Microsoft. R-functies en -algoritmen van Microsoft zijn ontworpen voor zowel schaal als bruikbaarheid en bieden voorspellende analyses, statistische modellen, gegevensvisualisaties en toonaangevende algoritmen voor machine learning.

### <a name="r-packages"></a>R-pakketten

De meest voorkomende open-source R-pakketten zijn vooraf geïnstalleerd in Machine Learning Services. De volgende R-pakketten van Microsoft zijn ook inbegrepen:

| R-pakket | Beschrijving|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open is de verbeterde distributie van R van Microsoft. Het is een compleet open-source platform voor statistische analyse en data science. Het is gebaseerd op en 100% compatibel met R, en bevat extra mogelijkheden voor betere prestaties en reproduceerbaarheid. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR is de primaire bibliotheek voor schaalbare R. Functies in deze bibliotheek behoren tot de meest gebruikte. Gegevenstransformaties en manipulatie, statistische samenvatting, visualisatie en vele vormen van modellering en analyses zijn te vinden in deze bibliotheken. Bovendien distribueren functies in deze bibliotheken workloads automatisch over beschikbare kernen voor parallelle verwerking, met de mogelijkheid om te werken aan stukjes gegevens die worden gecoördineerd en beheerd door de berekeningsengine. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML voegt machine learning-algoritmen toe om aangepaste modellen te maken voor tekstanalyse, beeldanalyse en sentimentanalyse. |

Naast de vooraf geïnstalleerde pakketten u [extra pakketten installeren.](sql-database-machine-learning-services-add-r-packages.md)

<a name="signup"></a>

## <a name="sign-up-for-the-preview-closed"></a>Aanmelden voor de preview (gesloten)

> [!IMPORTANT]
> Aanmelden voor Azure SQL Database Machine Learning Services (preview) is momenteel **gesloten.**

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [belangrijkste verschillen met SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Zie de [quickstart-handleiding](sql-database-connect-query-r.md)voor meer informatie over het gebruik van R voor het opvragen van Azure SQL Database Machine Learning Services (preview.
- Zie [Eenvoudige R-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md)om aan de slag te gaan met een aantal eenvoudige R-scripts.
