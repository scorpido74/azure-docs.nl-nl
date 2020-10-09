---
title: Fout rijen verwerken met toewijzing van gegevens stromen in Azure Data Factory
description: Meer informatie over het afbreken van SQL-afbreking in Azure Data Factory met het toewijzen van gegevens stromen.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 3f8ac2d1434019548b01d8468015a543d89d0fba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85254409"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Afgebroken SQL-rijen in Data Factory gegevens stromen toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Een veelvoorkomend scenario in Data Factory bij het gebruik van toewijzings gegevens stromen is het schrijven van uw getransformeerde gegevens naar een data base in Azure SQL Database. In dit scenario wordt een veelvoorkomende fout voorwaarde die u moet voor komen tegen mogelijke kolom afkap ping. Volg deze stappen voor het vastleggen van kolommen die niet in een doel reeks kolom passen, zodat uw gegevens stroom in deze scenario's kan worden voortgezet.

## <a name="scenario"></a>Scenario

1. Er is een doel database tabel die een kolom bevat met de ```nvarchar(5)``` naam ' name '.

2. In onze gegevens stroom willen we film titels van onze Sink toewijzen aan de kolom naam.

    ![Film gegevens stroom 1](media/data-flow/error4.png)
    
3. Het probleem is dat de titel van de film niet helemaal past binnen een Sink-kolom die Maxi maal vijf tekens kan bevatten. Wanneer u deze gegevens stroom uitvoert, treedt er een fout op, zoals deze: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

In deze video wordt een voor beeld gegeven van het instellen van een logische rij voor het verwerken van rijen in uw gegevens stroom:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Het ontwerp rond deze voor waarde

1. In dit scenario is de maximum lengte van de kolom ' name ' vijf tekens. Daarom voegen we een Conditional Split-trans formatie toe waarmee we rijen kunnen registreren met ' titels ' die langer zijn dan vijf tekens, terwijl ook de rest van de rijen die in die ruimte passen, kunnen worden opgeslagen in de data base.

    ![Conditional Split](media/data-flow/error1.png)

2. Met deze Conditional Split trans formatie wordt de maximum lengte van ' title ' gedefinieerd als vijf. Een rij die kleiner dan of gelijk aan vijf is, gaat in de ```GoodRows``` stroom. Rijen die groter zijn dan vijf, worden in de ```BadRows``` Stream verzonden.

3. De rijen die zijn mislukt, moeten nu worden geregistreerd. Voeg een Sink-trans formatie toe aan de ```BadRows``` stroom voor logboek registratie. Hier worden alle velden automatisch toegewezen, zodat de registratie van de volledige transactie record wordt geregistreerd. Dit is een tekst scheidings teken van CSV-bestand naar één bestand in Blob Storage. Het logboek bestand badrows.csv wordt aangeroepen.

    ![Ongeldige rijen](media/data-flow/error3.png)
    
4. De voltooide gegevens stroom wordt hieronder weer gegeven. We kunnen nu fout rijen opsplitsen om te voor komen dat er fouten in de SQL-afkap ping ontstaan en deze vermeldingen in een logboek bestand plaatsen. Ondertussen kunnen voltooide rijen naar de doel database blijven schrijven.

    ![gegevens stroom volt ooien](media/data-flow/error2.png)

## <a name="next-steps"></a>Volgende stappen

* Bouw de rest van uw gegevensstroom logica met behulp van trans [formaties](concepts-data-flow-overview.md)voor gegevens stromen.
