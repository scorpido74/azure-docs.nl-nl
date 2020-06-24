---
title: Azure Cosmos DB gegevens herstellen vanuit een back-up
description: In dit artikel wordt beschreven hoe u Azure Cosmos DB gegevens terugzet vanuit een back-up, hoe u contact opneemt met de ondersteuning van Azure om gegevens te herstellen, stappen die u moet uitvoeren nadat de gegevens zijn hersteld.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1a0075f9b4fc3ff919d4db4bd440a5435d711c83
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261729"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Gegevens herstellen vanuit een back-up in Azure Cosmos DB 

Als u per ongeluk uw data base of container verwijdert, kunt u [een ondersteunings ticket indienen]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) of [ondersteuning voor Azure aanroepen]( https://azure.microsoft.com/support/options/) om de gegevens van automatische online back-ups te herstellen. Ondersteuning voor Azure is alleen beschikbaar voor geselecteerde abonnementen, zoals **Standard**, **Developer**en abonnementen hoger. Ondersteuning voor Azure is niet beschikbaar voor het **Basic** -abonnement. Zie de pagina [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/) voor meer informatie over verschillende ondersteunings abonnementen. 

Als u een specifieke moment opname van de back-up wilt herstellen, moet Azure Cosmos DB de gegevens beschikbaar hebben voor de duur van de back-upcyclus voor die moment opname.

## <a name="request-a-restore"></a>Een terugzet bewerking aanvragen

U moet de volgende gegevens hebben voordat u een herstel bewerking kunt aanvragen:

* Uw abonnement-ID gereed is.

* Op basis van de manier waarop uw gegevens per ongeluk zijn verwijderd of gewijzigd, moet u voor bereidingen treffen voor aanvullende informatie. Het is raadzaam dat u de informatie die u voor het eerst beschikbaar hebt, hebt om de back-and-Case zo klein mogelijk te houden.

* Als het volledige Azure Cosmos DB account wordt verwijderd, moet u de naam van het verwijderde account opgeven. Als u een ander account met dezelfde naam als het verwijderde account maakt, deelt u dit met het ondersteunings team, omdat het u helpt om het juiste account te bepalen. Het is raadzaam om verschillende ondersteunings tickets voor elk verwijderd account te bestand, omdat het de Verwar ring van de herstel bewerking minimaliseert.

* Als een of meer data bases worden verwijderd, moet u het Azure Cosmos-account opgeven, evenals de namen van de Azure Cosmos-data bases en opgeven of er een nieuwe Data Base met dezelfde naam bestaat.

* Als een of meer containers worden verwijderd, moet u de naam van het Azure Cosmos-account, de database namen en de container namen opgeven. En geef op of er een container met dezelfde naam bestaat.

* Als u uw gegevens per ongeluk hebt verwijderd of beschadigd, neemt u binnen 8 uur contact op met [Azure-ondersteuning](https://azure.microsoft.com/support/options/) , zodat het Azure Cosmos DB team u kan helpen bij het herstellen van de gegevens van de back-ups.
  
  * Als u per ongeluk uw data base of container hebt verwijderd, opent u een Ernst B of ernst C Azure-ondersteunings aanvraag. 
  * Als u per ongeluk een aantal documenten in de container hebt verwijderd of beschadigd, opent u een Ernst-ondersteunings aanvraag. 

Als de gegevens zijn beschadigd en als de documenten in een container worden gewijzigd of verwijderd, moet u **de container zo snel mogelijk verwijderen**. Als u de container verwijdert, kunt u voor komen dat Azure Cosmos DB de back-ups overschrijft. Als het verwijderen om de een of andere reden niet mogelijk is, moet u zo snel mogelijk een ticket indienen. Naast de naam van het Azure Cosmos-account, database namen, container namen, moet u het tijdstip opgeven waarop de gegevens kunnen worden teruggezet. Het is belang rijk om zo nauw keurig mogelijk te zijn om ons te helpen bij het bepalen van de beste beschik bare back-ups op dat moment. Het is ook belang rijk om de tijd op te geven in UTC. 

In de volgende scherm afbeelding ziet u hoe u een ondersteunings aanvraag voor een container (verzameling/grafiek/tabel) kunt maken om gegevens te herstellen met behulp van Azure Portal. Geef aanvullende informatie op, zoals het type gegevens, het doel van de herstel tijd, het tijdstip waarop de gegevens zijn verwijderd om ons te helpen bij het bepalen van de prioriteit van de aanvraag.

:::image type="content" source="./media/how-to-backup-and-restore/backup-support-request-portal.png" alt-text="Een verzoek tot ondersteuning voor back-ups maken met behulp van Azure Portal":::

## <a name="post-restore-actions"></a>Acties na herstel

Nadat u de gegevens hebt hersteld, ontvangt u een melding over de naam van het nieuwe account (meestal in de indeling `<original-name>-restored1` ) en het tijdstip waarop het account is hersteld. Het herstelde account heeft dezelfde ingerichte door Voer, het indexerings beleid en het bevindt zich in dezelfde regio als het oorspronkelijke account. Een gebruiker die de abonnements beheerder of een cobeheerder is, kan het herstelde account zien.

Nadat de gegevens zijn hersteld, moet u de gegevens in het herstelde account controleren en valideren en ervoor zorgen dat deze de versie bevat die u verwacht. Als alles goed lijkt, migreert u de gegevens terug naar uw oorspronkelijke account met [Azure Cosmos DB wijzigings feed](change-feed.md) of [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

U wordt aangeraden de container of data base onmiddellijk na het migreren van de gegevens te verwijderen. Als u de herstelde data bases of containers niet verwijdert, worden er kosten in rekening gebracht voor aanvraag eenheden, opslag en uitgaand verkeer.

## <a name="next-steps"></a>Volgende stappen

Vervolgens leert u hoe u de gegevens opnieuw naar uw oorspronkelijke account kunt migreren met behulp van de volgende artikelen:

* Neem contact op met de ondersteuning van Azure, maak [een ticket van de Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om een herstel aanvraag te doen.
* [Gebruik Cosmos DB Change feed](change-feed.md) om gegevens naar Azure Cosmos DB te verplaatsen.

* [Gebruik Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) om gegevens te verplaatsen naar Azure Cosmos db.
