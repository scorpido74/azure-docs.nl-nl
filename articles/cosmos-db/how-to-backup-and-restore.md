---
title: Azure Cosmos DB-gegevens herstellen vanuit een back-up
description: In dit artikel wordt beschreven hoe u Azure Cosmos DB-gegevens herstellen vanaf een back-up, hoe u contact opnemen met Azure-ondersteuning om gegevens te herstellen, stappen die moeten worden genomen nadat de gegevens zijn hersteld.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 19ca835ca8211202cd358ac2ec3695675183a372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70240762"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Gegevens herstellen van een back-up in Azure Cosmos DB 

Als u per ongeluk uw database of container verwijdert, u [een ondersteuningsticket indienen]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) of [Azure-ondersteuning bellen]( https://azure.microsoft.com/support/options/) om de gegevens te herstellen van automatische online back-ups. Azure-ondersteuning is alleen beschikbaar voor geselecteerde abonnementen, zoals **Standard,** **Developer**en plannen die hoger zijn dan deze. Azure-ondersteuning is niet beschikbaar met **het Basic-abonnement.** Zie de pagina [Azure-ondersteuningsplannen](https://azure.microsoft.com/support/plans/) voor meer informatie over verschillende ondersteuningsplannen. 

Als u een specifieke momentopname van de back-up wilt herstellen, vereist Azure Cosmos DB dat de gegevens beschikbaar zijn voor de duur van de back-upcyclus voor die momentopname.

## <a name="request-a-restore"></a>Een herstel aanvragen

U moet de volgende gegevens hebben voordat u een herstel aanvraag:

* Maak uw abonnement-id gereed.

* Op basis van de manier waarop uw gegevens per ongeluk zijn verwijderd of gewijzigd, moet u zich voorbereiden op aanvullende informatie. Het wordt aangeraden dat u de informatie beschikbaar voor u om de heen-en-weer die schadelijk kunnen zijn in sommige gevallen gevoelige gevallen te minimaliseren.

* Als het volledige Azure Cosmos DB-account wordt verwijderd, moet u de naam van het verwijderde account opgeven. Als u een ander account maakt met dezelfde naam als het verwijderde account, deelt u dat met het ondersteuningsteam, omdat het helpt om het juiste account te bepalen dat u wilt kiezen. Het wordt aanbevolen om verschillende ondersteuningstickets voor elk verwijderd account in te dienen, omdat het de verwarring van de status van het herstel minimaliseert.

* Als een of meer databases worden verwijderd, moet u het Azure Cosmos-account en de azure cosmos-databasenamen opgeven en opgeven of er een nieuwe database met dezelfde naam bestaat.

* Als een of meer containers worden verwijderd, moet u de naam van het Azure Cosmos-account, de databasenamen en de containernamen opgeven. En geef op of er een container met dezelfde naam bestaat.

* Als u uw gegevens per ongeluk hebt verwijderd of beschadigd, moet u binnen 8 uur contact opnemen met [Azure-ondersteuning,](https://azure.microsoft.com/support/options/) zodat het Azure Cosmos DB-team u kan helpen de gegevens uit de back-ups te herstellen.
  
  * Als u per ongeluk uw database of container hebt verwijderd, opent u een Sev B- of Sev C Azure-ondersteuningsaanvraag. 
  * Als u per ongeluk bepaalde documenten in de container hebt verwijderd of beschadigd, opent u een Sev A-ondersteuningsaanvraag. 

Wanneer er sprake is van gegevensbeschadiging en als de documenten in een container worden gewijzigd of verwijderd, **verwijdert u de container zo snel mogelijk**. Door de container te verwijderen, u voorkomen dat Azure Cosmos DB de back-ups overschrijft. Als het verwijderen om de een of andere reden niet mogelijk is, dient u zo snel mogelijk een ticket in te dienen. Naast azure cosmos-accountnaam, databasenamen, containernamen, moet u opgeven op welk tijdstip de gegevens kunnen worden hersteld. Het is belangrijk om zo nauwkeurig mogelijk te zijn om ons te helpen de beste beschikbare back-ups op dat moment te bepalen. Het is ook belangrijk om de tijd in UTC op te geven. 

In de volgende schermafbeelding ziet u hoe u een ondersteuningsaanvraag maakt voor een container (verzameling/grafiek/tabel) om gegevens te herstellen met behulp van Azure-portal. Geef aanvullende gegevens, zoals het type gegevens, het doel van het herstel, het tijdstip waarop de gegevens zijn verwijderd om ons te helpen de aanvraag te prioriteren.

![Een back-upondersteuningsaanvraag maken met Azure-portal](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Acties na herstel

Nadat u de gegevens hebt hersteld, ontvangt u een melding over de naam `<original-name>-restored1`van het nieuwe account (deze is meestal in de indeling) en het tijdstip waarop het account is hersteld. De herstelde account heeft dezelfde ingerichte doorvoer, indexeringsbeleid en bevindt zich in dezelfde regio als het oorspronkelijke account. Een gebruiker die de abonnementsbeheerder of een medebeheerder is, kan het herstelde account zien.

Nadat de gegevens zijn hersteld, moet u de gegevens in het herstelde account inspecteren en valideren en ervoor zorgen dat de versie bevat die u verwacht. Als alles er goed uitziet, moet u de gegevens migreren naar uw oorspronkelijke account met [Azure Cosmos DB change feed](change-feed.md) of Azure Data [Factory.](../data-factory/connector-azure-cosmos-db.md)

Het wordt aangeraden de container of database onmiddellijk na het migreren van de gegevens te verwijderen. Als u de herstelde databases of containers niet verwijdert, worden er kosten verbonden aan aanvraageenheden, opslag en uitgang.

## <a name="next-steps"></a>Volgende stappen

Vervolgens u meer informatie krijgen over het migreren van de gegevens naar uw oorspronkelijke account met behulp van de volgende artikelen:

* Als u een herstelverzoek wilt indienen, neemt u contact op met Azure Support [en bestandst u een ticket vanaf de Azure-portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Gebruik Cosmos DB change feed](change-feed.md) om gegevens naar Azure Cosmos DB te verplaatsen.

* [Gebruik Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) om gegevens naar Azure Cosmos DB te verplaatsen.
