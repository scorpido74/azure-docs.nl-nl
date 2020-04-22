---
title: Foutrijen verwerken met toewijzingsgegevensstromen in Azure Data Factory
description: Meer informatie over het verwerken van SQL-truncation-fouten in Azure Data Factory met behulp van toewijzingsgegevensstromen.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 8225143bb75118620b45c2520bb62ea30501a617
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732688"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>SQL-foutrijen verwerken in gegevensstromen voor gegevensfabriekstoewijzing

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Een veelvoorkomend scenario in Data Factory bij het gebruik van toewijzingsgegevensstromen, is het schrijven van uw getransformeerde gegevens naar een Azure SQL-database. In dit scenario is een veelvoorkomende foutvoorwaarde die u moet voorkomen mogelijk kolomafgekapt. Volg deze stappen om logboekregistratie van kolommen te bieden die niet in een doeltekenreekskolom passen, zodat uw gegevensstroom in die scenario's kan worden voortgezet.

## <a name="scenario"></a>Scenario

1. We hebben een doelazure SQL-databasetabel met een ```nvarchar(5)``` kolom met de naam "naam".

2. Binnen onze gegevensstroom willen we filmtitels van onze gootsteen in kaart brengen tot die doelkolom "naam".

    ![Filmgegevensstroom 1](media/data-flow/error4.png)
    
3. Het probleem is dat de filmtitel niet allemaal past in een gootsteenkolom die slechts 5 tekens kan bevatten. Wanneer u deze gegevensstroom uitvoert, ontvangt u een fout als deze:```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

In deze video wordt een voorbeeld gegeven van het instellen van foutrijverwerkingslogica in uw gegevensstroom:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Hoe te ontwerpen rond deze voorwaarde

1. In dit scenario is de maximale lengte van de kolom 'naam' vijf tekens. Dus, laten we een voorwaardelijke gesplitste transformatie die ons in staat stelt om rijen te loggen met "titels" die langer zijn dan vijf tekens, terwijl ook de rest van de rijen die kunnen passen in die ruimte te schrijven naar de database.

    ![voorwaardelijke splitsing](media/data-flow/error1.png)

2. Deze voorwaardelijke gesplitste transformatie definieert de maximale lengte van "titel" als vijf. Elke rij die minder dan of gelijk ```GoodRows``` is aan vijf, gaat in de stream. Elke rij die groter is dan ```BadRows``` vijf gaat in de stroom.

3. Nu moeten we de rijen registreren die zijn mislukt. Voeg een sinktransformatie ```BadRows``` toe aan de stroom voor logboekregistratie. Hier zullen we alle velden "automatisch toewijzen", zodat we het volledige transactierecord kunnen registreren. Dit is een csv-bestandsuitvoer met tekstdie is beperkt tot één bestand in Blob Storage. We noemen het logbestand "badrows.csv".

    ![Slechte rijen](media/data-flow/error3.png)
    
4. De ingevulde gegevensstroom wordt hieronder weergegeven. We zijn nu in staat om foutrijen af te splitsen om de SQL-truncation-fouten te voorkomen en deze vermeldingen in een logboekbestand te plaatsen. Ondertussen kunnen succesvolle rijen blijven schrijven naar onze doeldatabase.

    ![volledige gegevensstroom](media/data-flow/error2.png)

## <a name="next-steps"></a>Volgende stappen

* Bouw de rest van uw logica voor gegevensstromen met behulp van [transformaties](concepts-data-flow-overview.md)van gegevensstromen .
