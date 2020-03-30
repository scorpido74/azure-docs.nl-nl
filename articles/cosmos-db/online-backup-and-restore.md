---
title: Online back-up en on-demand gegevensherstel in Azure Cosmos DB
description: In dit artikel wordt beschreven hoe automatische, online back-up en on-demand gegevensherstel werkt in Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: f5cc4339d7d6dce6d49c8d3eb744fca7fa5774d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240439"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Online back-up en on-demand gegevensherstel in Azure Cosmos DB

Azure Cosmos DB maakt automatisch regelmatig back-ups van uw gegevens. De automatische back-ups worden genomen zonder dat dit gevolgen heeft voor de prestaties of beschikbaarheid van de databasebewerkingen. Alle back-ups worden afzonderlijk opgeslagen in een opslagservice en deze back-ups worden wereldwijd gerepliceerd voor tolerantie tegen regionale rampen. De automatische back-ups zijn handig in scenario's wanneer u per ongeluk uw Azure Cosmos-account, database of container verwijdert of bijwerkt en later het gegevensherstel vereist.

## <a name="automatic-and-online-backups"></a>Automatische en online back-ups

Met Azure Cosmos DB zijn niet alleen uw gegevens, maar ook de back-ups van uw gegevens zeer redundant en bestand tegen regionale rampen. In de volgende stappen wordt weergegeven hoe Azure Cosmos DB een back-up van gegevens uitvoert:

* In Azure Cosmos DB wordt elke 4 uur automatisch een back-up van uw database gemaakt. Op elk gewenst moment worden alleen de twee meest recente back-ups opgeslagen. Als de container of database echter wordt verwijderd, blijven in Azure Cosmos DB de bestaande momentopnamen van een opgegeven container of database gedurende 30 dagen behouden.

* Azure Cosmos DB slaat deze back-ups op in Azure Blob-opslag, terwijl de werkelijke gegevens lokaal binnen Azure Cosmos DB zijn.

*  Om een lage latentie te garanderen, wordt de momentopname van uw back-up opgeslagen in Azure Blob-opslag in hetzelfde gebied als het huidige schrijfgebied (of een van de schrijfregio's, voor het geval u een multimasterconfiguratie hebt) van uw Azure Cosmos-databaseaccount. Voor tolerantie tegen regionale rampen wordt elke momentopname van de back-upgegevens in Azure Blob-opslag opnieuw gerepliceerd naar een andere regio via georedundante opslag (GRS). Het gebied waaraan de back-up wordt gerepliceerd, is gebaseerd op uw bronregio en het regionale paar dat is gekoppeld aan het brongebied. Zie de lijst [met georedundante paren van Azure-regio's](../best-practices-availability-paired-regions.md) voor meer informatie. U hebt geen toegang tot deze back-up. Azure Cosmos DB gebruikt deze back-up alleen als een back-upherstel wordt gestart.

* De back-ups worden genomen zonder dat dit gevolgen heeft voor de prestaties of beschikbaarheid van uw toepassing. Azure Cosmos DB voert gegevensback-up op de achtergrond uit zonder dat er extra ingerichte doorvoer (RU's) wordt verbruikt of dat dit gevolgen heeft voor de prestaties en beschikbaarheid van uw database.

* Als u uw gegevens per ongeluk hebt verwijderd of beschadigd, moet u binnen 8 uur contact opnemen met [Azure-ondersteuning,](https://azure.microsoft.com/support/options/) zodat het Azure Cosmos DB-team u kan helpen de gegevens uit de back-ups te herstellen.

In de volgende afbeelding ziet u hoe een Azure Cosmos-container met alle drie de primaire fysieke partities in West-VS wordt geback-upt in een extern Azure Blob Storage-account in West-VS en vervolgens wordt gerepliceerd naar Oost-VS:

![Periodieke volledige back-ups van alle Cosmos DB-entiteiten in GRS Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Opties voor het beheren van uw eigen back-ups

Met Azure Cosmos DB SQL API-accounts u ook uw eigen back-ups onderhouden met een van de volgende benaderingen:

* Gebruik [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) om gegevens periodiek te verplaatsen naar een opslag naar keuze.

* Gebruik Azure Cosmos [DB-wijzigingsfeed](change-feed.md) om gegevens periodiek te lezen voor volledige back-ups en voor incrementele wijzigingen en deze op te slaan in uw eigen opslag.

## <a name="backup-retention-period"></a>Bewaarperiode voor back-ups

Azure Cosmos DB maakt elke vier uur snapshots van uw gegevens. Op een gegeven moment blijven alleen de laatste twee momentopnamen behouden. Als de container of database echter wordt verwijderd, blijven in Azure Cosmos DB de bestaande momentopnamen van een opgegeven container of database gedurende 30 dagen behouden.

## <a name="restoring-data-from-online-backups"></a>Gegevens herstellen van online back-ups

In een van de volgende scenario's kan per ongeluk gegevens worden verwijderd of gewijzigd:  

* Het volledige Azure Cosmos-account wordt verwijderd

* Een of meer Azure Cosmos-databases worden verwijderd

* Een of meer Azure Cosmos-containers worden verwijderd

* Azure Cosmos-items (bijvoorbeeld documenten) in een container worden verwijderd of gewijzigd. Dit specifieke geval wordt meestal aangeduid als "gegevenscorruptie".

* Een gedeelde aanbiedingsdatabase of -containers in een database met gedeelde aanbiedingen worden verwijderd of beschadigd

Azure Cosmos DB kan gegevens herstellen in alle bovenstaande scenario's. Tijdens het herstelproces wordt altijd een nieuw Azure Cosmos-account gemaakt om de herstelde gegevens in te bewaren. De naam van het nieuwe account, indien `<Azure_Cosmos_account_original_name>-restored1`niet opgegeven, heeft de indeling . Het laatste cijfer wordt verhoogd als meerdere herstelfouten worden geprobeerd. U gegevens niet herstellen naar een vooraf gemaakt Azure Cosmos-account.

Wanneer een Azure Cosmos-account wordt verwijderd, kunnen we de gegevens herstellen naar een account met dezelfde naam, op voorwaarde dat de accountnaam niet in gebruik is. In dergelijke gevallen wordt aanbevolen om het account niet opnieuw te maken na verwijdering, omdat het niet alleen voorkomt dat de herstelde gegevens dezelfde naam gebruiken, maar ook het ontdekken van het juiste account om te herstellen van moeilijker maakt. 

Wanneer een Azure Cosmos-database wordt verwijderd, is het mogelijk om de hele database of een subset van de containers in die database te herstellen. Het is ook mogelijk om containers in databases te selecteren en te herstellen en alle herstelde gegevens worden in een nieuw Azure Cosmos-account geplaatst.

Wanneer een of meer items in een container per ongeluk worden verwijderd of gewijzigd (de zaak voor gegevensbeschadiging), moet u de tijd opgeven waarop u moet herstellen. Tijd is van essentieel belang voor deze zaak. Aangezien de container live is, wordt de back-up nog steeds uitgevoerd, dus als u wacht na de bewaarperiode (de standaardis acht uur) worden de back-ups overschreven. In het geval van verwijderingen worden uw gegevens niet meer opgeslagen omdat ze niet worden overschreven door de back-upcyclus. Back-ups voor verwijderde databases of containers worden gedurende 30 dagen opgeslagen.

Als u doorvoer indient op databaseniveau (dat wil zeggen wanneer een set containers de ingerichte doorvoer deelt), gebeurt het back-up- en herstelproces in dit geval op het gehele databaseniveau en niet op het niveau van de afzonderlijke containers. In dergelijke gevallen is het selecteren van een subset van containers om te herstellen geen optie.

## <a name="migrating-data-to-the-original-account"></a>Gegevens migreren naar het oorspronkelijke account

Het primaire doel van het herstellen van de gegevens is om een manier te bieden om alle gegevens die u per ongeluk verwijdert of wijzigt, te herstellen. Daarom raden we u aan eerst de inhoud van de herstelde gegevens te inspecteren om ervoor te zorgen dat deze informatie bevatten wat u verwacht. Werk vervolgens aan het migreren van de gegevens naar het primaire account. Hoewel het mogelijk is om het herstelde account te gebruiken als het live-account, is het geen aanbevolen optie als u productieworkloads hebt.  

Hieronder volgen verschillende manieren om gegevens terug te migreren naar het oorspronkelijke Azure Cosmos-account:

* Hulpprogramma [voor gegevensmigratie van Cosmos DB gebruiken](import-data.md)
* [Azure-gegevensfabriek gebruiken]( ../data-factory/connector-azure-cosmos-db.md)
* [Wijzigingsfeed gebruiken](change-feed.md) in Azure Cosmos DB 
* Aangepaste code schrijven

Verwijder de herstelde accounts zodra u klaar bent met migreren, omdat er lopende kosten in rekening worden gebracht.

## <a name="next-steps"></a>Volgende stappen

Vervolgens u meer informatie krijgen over het herstellen van gegevens uit een Azure Cosmos-account of hoe u gegevens migreren naar een Azure Cosmos-account

* Als u een herstelverzoek wilt indienen, neemt u contact op met Azure Support [en bestandst u een ticket vanaf de Azure-portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Gegevens herstellen van een Azure Cosmos-account](how-to-backup-and-restore.md)
* [Gebruik Cosmos DB change feed](change-feed.md) om gegevens naar Azure Cosmos DB te verplaatsen.
* [Gebruik Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) om gegevens naar Azure Cosmos DB te verplaatsen.

