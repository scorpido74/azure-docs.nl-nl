---
title: Herstel van online back-ups en gegevens op aanvraag in Azure Cosmos DB
description: In dit artikel wordt beschreven hoe automatische, online back-ups en gegevens herstel op aanvraag in Azure Cosmos DB werken.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: f5cc4339d7d6dce6d49c8d3eb744fca7fa5774d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240439"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Herstel van online back-ups en gegevens op aanvraag in Azure Cosmos DB

Azure Cosmos DB maakt met regel matige tussen pozen automatisch back-ups van uw gegevens. De automatische back-ups worden gemaakt zonder dat dit van invloed is op de prestaties of Beschik baarheid van de database bewerkingen. Alle back-ups worden afzonderlijk in een opslag service opgeslagen en deze back-ups worden wereld wijd gerepliceerd voor flexibiliteit tegen regionale rampen. De automatische back-ups zijn handig in scenario's wanneer u per ongeluk uw Azure Cosmos-account,-data base of-container verwijdert of bijwerkt en later het gegevens herstel vereist.

## <a name="automatic-and-online-backups"></a>Automatische en online back-ups

Met Azure Cosmos DB, niet alleen uw gegevens, maar ook de back-ups van uw gegevens zijn zeer redundant en robuust voor regionale rampen. De volgende stappen laten zien hoe Azure Cosmos DB gegevens back-up uitvoert:

* In Azure Cosmos DB wordt elke 4 uur automatisch een back-up van uw database gemaakt. Op elk gewenst moment worden alleen de twee meest recente back-ups opgeslagen. Als de container of database echter wordt verwijderd, blijven in Azure Cosmos DB de bestaande momentopnamen van een opgegeven container of database gedurende 30 dagen behouden.

* Azure Cosmos DB slaat deze back-ups op in Azure Blob-opslag terwijl de daad werkelijke gegevens lokaal zijn opgeslagen in Azure Cosmos DB.

*  Om een lage latentie te garanderen, wordt de moment opname van uw back-up opgeslagen in Azure Blob Storage in dezelfde regio als de huidige schrijf regio (of een van de schrijf regio's, voor het geval u een configuratie met meerdere masters hebt) van uw Azure Cosmos-database account. Voor tolerantie tegen regionale nood gevallen wordt elke moment opname van de back-upgegevens in Azure Blob-opslag opnieuw gerepliceerd naar een andere regio via Geo-redundante opslag (GRS). De regio waarnaar de back-up wordt gerepliceerd, is gebaseerd op de bron regio en het regionale paar dat is gekoppeld aan de bron regio. Zie de [lijst met geo-redundante paren van Azure-regio's](../best-practices-availability-paired-regions.md) voor meer informatie. U hebt geen rechtstreekse toegang tot deze back-up. Azure Cosmos DB maakt alleen gebruik van deze back-up als er een back-up wordt gemaakt.

* De back-ups worden gemaakt zonder dat dit van invloed is op de prestaties of Beschik baarheid van uw toepassing. Azure Cosmos DB voert gegevens back-ups op de achtergrond uit zonder enige extra ingerichte door Voer (RUs) te gebruiken of de prestaties en beschik baarheid van uw data base te beïnvloeden.

* Als u uw gegevens per ongeluk hebt verwijderd of beschadigd, neemt u binnen 8 uur contact op met [Azure-ondersteuning](https://azure.microsoft.com/support/options/) , zodat het Azure Cosmos DB team u kan helpen bij het herstellen van de gegevens van de back-ups.

In de volgende afbeelding ziet u hoe een Azure Cosmos-container met alle drie primaire fysieke partities in West VS een back-up maakt in een extern Azure Blob Storage-account in VS-West en vervolgens wordt gerepliceerd naar VS-Oost:

![Periodieke volledige back-ups van alle Cosmos DB entiteiten in GRS Azure Storage](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Opties voor het beheren van uw eigen back-ups

Met Azure Cosmos DB SQL-API-accounts kunt u ook uw eigen back-ups onderhouden met behulp van een van de volgende benaderingen:

* Gebruik [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) om gegevens regel matig te verplaatsen naar een opslag van uw keuze.

* Gebruik Azure Cosmos DB [Change feed](change-feed.md) om gegevens regel matig te lezen voor volledige back-ups, en voor incrementele wijzigingen en op te slaan in uw eigen opslag.

## <a name="backup-retention-period"></a>Bewaar periode voor back-ups

Azure Cosmos DB maakt moment opnamen van uw gegevens om de vier uur. Op elk gewenst moment worden alleen de laatste twee moment opnamen bewaard. Als de container of database echter wordt verwijderd, blijven in Azure Cosmos DB de bestaande momentopnamen van een opgegeven container of database gedurende 30 dagen behouden.

## <a name="restoring-data-from-online-backups"></a>Gegevens herstellen vanuit online back-ups

Het onbedoeld verwijderen of wijzigen van gegevens kan zich in een van de volgende scenario's voordoen:  

* Het hele Azure Cosmos-account wordt verwijderd

* Een of meer Azure Cosmos-data bases zijn verwijderd

* Een of meer Azure Cosmos-containers zijn verwijderd

* Azure Cosmos-items (bijvoorbeeld documenten) binnen een container worden verwijderd of gewijzigd. Dit specifieke geval wordt meestal ' gegevens beschadiging ' genoemd.

* Een Data Base of containers van een gedeeld aanbod binnen een Data Base van het gedeelde aanbod worden verwijderd of beschadigd

Azure Cosmos DB kunt de gegevens in alle bovenstaande scenario's herstellen. Tijdens het herstelproces wordt altijd een nieuw Azure Cosmos-account gemaakt om de herstelde gegevens in te bewaren. De naam van het nieuwe account, indien niet opgegeven, heeft de indeling `<Azure_Cosmos_account_original_name>-restored1`. Het laatste cijfer wordt verhoogd als er meerdere herstel pogingen worden uitgevoerd. U kunt geen gegevens herstellen naar een vooraf gemaakt Azure Cosmos-account.

Wanneer een Azure Cosmos-account wordt verwijderd, kunnen we de gegevens herstellen naar een account met dezelfde naam, op voor waarde dat de account naam niet wordt gebruikt. In dergelijke gevallen is het raadzaam om het account niet opnieuw te maken na het verwijderen, omdat het niet alleen voor komt dat de herstelde gegevens dezelfde naam gebruiken, maar ook het juiste account wordt gedetecteerd om te herstellen van moeilijker. 

Wanneer een Azure Cosmos-data base wordt verwijderd, is het mogelijk om de gehele data base of een subset van de containers in die data base te herstellen. Het is ook mogelijk om containers in data bases te selecteren en deze te herstellen en alle herstelde gegevens worden opgeslagen in een nieuw Azure Cosmos-account.

Wanneer een of meer items in een container per ongeluk worden verwijderd of gewijzigd (de gegevens zijn beschadigd), moet u de tijd opgeven waarop u wilt herstellen. De tijd is in essentie voor deze aanvraag. Omdat de container Live is, wordt de back-up nog steeds uitgevoerd. Als u na de Bewaar periode blijft (de standaard waarde is acht uur), worden de back-ups overschreven. In het geval van verwijderen worden uw gegevens niet meer opgeslagen, omdat deze niet worden overschreven door de back-upcyclus. Back-ups voor verwijderde data bases of containers worden 30 dagen bespaard.

Als u de door Voer inricht op het niveau van de data base (dat wil zeggen, waarbij een set containers de ingerichte door Voer deelt), gebeurt het back-up-en herstel proces in dit geval op het hele database niveau en niet op het niveau van de afzonderlijke containers. In dergelijke gevallen is het selecteren van een subset van containers die moeten worden hersteld, geen optie.

## <a name="migrating-data-to-the-original-account"></a>Gegevens migreren naar het oorspronkelijke account

Het belangrijkste doel van het terugzetten van gegevens is het bieden van een manier om gegevens te herstellen die u per ongeluk wist of wijzigt. Daarom raden we u aan eerst de inhoud van de herstelde gegevens te controleren om er zeker van te zijn dat deze bevat wat u verwacht. Vervolgens werkt u de gegevens terug naar het primaire account. Hoewel het mogelijk is om het teruggezette account als het Live-account te gebruiken, is het geen aanbevolen optie als u werk belastingen voor productie hebt.  

Hier volgen verschillende manieren om gegevens terug te migreren naar het oorspronkelijke Azure Cosmos-account:

* [Cosmos DB Data Migration Tool](import-data.md) gebruiken
* [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md) gebruiken
* [Change feed](change-feed.md) gebruiken in azure Cosmos db 
* Aangepaste code schrijven

Verwijder de herstelde accounts zodra u klaar bent met de migratie, omdat er lopende kosten in rekening worden gebracht.

## <a name="next-steps"></a>Volgende stappen

Hierna vindt u informatie over het herstellen van gegevens uit een Azure Cosmos-account of over het migreren van gegevens naar een Azure Cosmos-account

* Neem contact op met de ondersteuning van Azure, maak [een ticket van de Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om een herstel aanvraag te doen.
* [Gegevens herstellen vanuit een Azure Cosmos-account](how-to-backup-and-restore.md)
* [Gebruik Cosmos DB Change feed](change-feed.md) om gegevens naar Azure Cosmos DB te verplaatsen.
* [Gebruik Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) om gegevens te verplaatsen naar Azure Cosmos db.

