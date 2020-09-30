---
title: Herstel van online back-ups en gegevens op aanvraag in Azure Cosmos DB
description: In dit artikel wordt beschreven hoe automatische back-ups en gegevens herstel op aanvraag werken, hoe u een back-upinterval en retentie in Azure Cosmos DB kunt configureren.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 310fee91ed98409e5a724d1be8de7bc9ccb5601b
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570920"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Herstel van online back-ups en gegevens op aanvraag in Azure Cosmos DB

Azure Cosmos DB maakt met regel matige tussen pozen automatisch back-ups van uw gegevens. De automatische back-ups worden gemaakt zonder dat dit van invloed is op de prestaties of Beschik baarheid van de database bewerkingen. Alle back-ups worden afzonderlijk in een opslag service opgeslagen en deze back-ups worden wereld wijd gerepliceerd voor flexibiliteit tegen regionale rampen. De automatische back-ups zijn handig in scenario's wanneer u per ongeluk uw Azure Cosmos-account,-data base of-container verwijdert of bijwerkt en later het gegevens herstel vereist.

## <a name="automatic-and-online-backups"></a>Automatische en online back-ups

Met Azure Cosmos DB, niet alleen uw gegevens, maar ook de back-ups van uw gegevens zijn zeer redundant en robuust voor regionale rampen. De volgende stappen laten zien hoe Azure Cosmos DB gegevens back-up uitvoert:

* Azure Cosmos DB maakt automatisch een volledige back-up van uw data base in elke 4 uur en op elk gewenst moment worden alleen de meest recente twee back-ups standaard opgeslagen. Als de standaard intervallen niet voldoende zijn voor uw workloads, kunt u het back-upinterval en de retentie periode van de Azure Portal wijzigen. U kunt de back-upconfiguratie wijzigen tijdens of nadat het Azure Cosmos-account is gemaakt. Als de container of Data Base wordt verwijderd, Azure Cosmos DB de bestaande moment opnamen van een bepaalde container of data base gedurende 30 dagen behouden.

* Azure Cosmos DB slaat deze back-ups op in Azure Blob-opslag terwijl de daad werkelijke gegevens lokaal zijn opgeslagen in Azure Cosmos DB.

* Om een lage latentie te garanderen, wordt de moment opname van uw back-up opgeslagen in Azure Blob Storage in dezelfde regio als de huidige schrijf regio (of **een** van de schrijf regio's, voor het geval u een multi-regio schrijf configuratie hebt). Voor tolerantie tegen regionale noodsituaties wordt elke momentopname van de back-upgegevens in Azure Blob-opslag opnieuw gerepliceerd naar een andere regio via geografisch redundante opslag (GRS). De regio waarnaar de back-up wordt gerepliceerd, is gebaseerd op de bronregio en het regionale paar dat aan de bronregio is gekoppeld. Zie de [lijst met geo-redundante paren van Azure-regio's](../best-practices-availability-paired-regions.md) voor meer informatie. U hebt geen rechtstreekse toegang tot deze back-up. Azure Cosmos DB team herstelt uw back-up wanneer u daartoe een verzoek via een ondersteuningsaanvraag doet.

   In de volgende afbeelding ziet u hoe een Azure Cosmos-container met alle drie primaire fysieke partities in West VS een back-up maakt in een extern Azure Blob Storage-account in VS-West en vervolgens wordt gerepliceerd naar VS-Oost:

  :::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="Periodieke volledige back-ups van alle Cosmos DB entiteiten in GRS Azure Storage" border="false":::

* De back-ups worden gemaakt zonder dat dit van invloed is op de prestaties of Beschik baarheid van uw toepassing. Azure Cosmos DB voert gegevens back-ups op de achtergrond uit zonder enige extra ingerichte door Voer (RUs) te gebruiken of de prestaties en beschik baarheid van uw data base te beïnvloeden.

## <a name="options-to-manage-your-own-backups"></a>Opties voor het beheren van uw eigen back-ups

Met Azure Cosmos DB SQL-API-accounts kunt u ook uw eigen back-ups onderhouden met behulp van een van de volgende benaderingen:

* Gebruik [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) om gegevens regel matig te verplaatsen naar een opslag van uw keuze.

* Gebruik Azure Cosmos DB [Change feed](change-feed.md) om gegevens periodiek te lezen voor volledige back-ups of voor incrementele wijzigingen en sla deze op in uw eigen opslag.

## <a name="modify-the-backup-interval-and-retention-period"></a>Het back-upinterval en de Bewaar periode wijzigen

Azure Cosmos DB maakt automatisch een volledige back-up van uw gegevens voor elke 4 uur en op elk gewenst moment worden de meest recente twee back-ups opgeslagen. Deze configuratie is de standaard optie en wordt aangeboden zonder dat er extra kosten in rekening worden gebracht. U kunt het standaard interval voor back-ups en de Bewaar periode wijzigen tijdens het maken van het Azure Cosmos-account of nadat het account is gemaakt. De back-upconfiguratie wordt ingesteld op het niveau van het Azure Cosmos-account en u moet deze voor elk account configureren. Nadat u de back-upopties voor een account hebt geconfigureerd, wordt deze toegepast op alle containers in dat account. U kunt de back-upopties momenteel alleen wijzigen in Azure Portal.

Als u uw gegevens per ongeluk hebt verwijderd of beschadigd **voordat u een ondersteunings aanvraag voor het herstellen van de gegevens maakt, moet u de retentie van de back-up voor uw account verg Roten tot ten minste zeven dagen. Het is het beste om uw Bewaar periode binnen 8 uur na deze gebeurtenis te verg Roten.** Op deze manier heeft het Azure Cosmos DB-team voldoende tijd om uw account te herstellen.

Gebruik de volgende stappen om de standaard back-upopties voor een bestaand Azure Cosmos-account te wijzigen:

1. Meld u aan bij de [Azure Portal.](https://portal.azure.com/)
1. Navigeer naar uw Azure Cosmos-account en open het deel venster **back-up & herstellen** . Werk het back-upinterval en de Bewaar periode voor back-ups zo nodig bij.

   * **Back-upinterval** : dit is het interval waarmee Azure Cosmos DB probeert een back-up van uw gegevens te maken. Het maken van een back-up kost een periode van minder dan nul en in sommige gevallen kan dit mislukken vanwege downstream-afhankelijkheden. Azure Cosmos DB probeert echter het beste een back-up te maken met het geconfigureerde interval, maar niet dat de back-up binnen dat tijds interval is voltooid. U kunt deze waarde configureren in uren of minuten. Het back-upinterval mag niet minder dan 1 uur en langer dan 24 uur zijn. Wanneer u dit interval wijzigt, wordt het nieuwe interval van kracht vanaf het moment waarop de laatste back-up werd gemaakt.

   * **Bewaren van back-ups** : deze vertegenwoordigt de periode waarin elke back-up wordt bewaard. U kunt deze configureren in uren of dagen. De minimale Bewaar periode mag niet korter zijn dan twee keer het back-upinterval (in uren) en mag niet groter zijn dan 720 uur.

   * **Kopieën van opgeslagen gegevens** -standaard worden twee back-ups van uw gegevens gratis aangeboden. Er worden extra kosten in rekening gebracht als u meer dan twee exemplaren nodig hebt. Zie de sectie over verbruikte opslag in de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) als u wilt weten wat de exacte prijs voor extra exemplaren is.

   :::image type="content" source="./media/online-backup-and-restore/configure-backup-interval-retention.png" alt-text="Periodieke volledige back-ups van alle Cosmos DB entiteiten in GRS Azure Storage" border="true":::

Als u de opties voor back-up configureert tijdens het maken van het account, kunt u het **back-upbeleid**configureren, ofwel **periodiek** of **doorlopend**. Met het periodieke beleid kunt u het back-upinterval en de retentie van back-ups configureren. Het doorlopende beleid is momenteel alleen beschikbaar als u zich aanmeldt. Het Azure Cosmos DB-team zal uw werk belasting beoordelen en uw aanvraag goed keuren.

:::image type="content" source="./media/online-backup-and-restore/configure-periodic-continuous-backup-policy.png" alt-text="Periodieke volledige back-ups van alle Cosmos DB entiteiten in GRS Azure Storage" border="true":::

## <a name="restore-data-from-an-online-backup"></a>Gegevens herstellen vanuit een online back-up

U kunt uw gegevens per ongeluk verwijderen of wijzigen in een van de volgende scenario's:  

* Verwijder het hele Azure Cosmos-account.

* Een of meer Azure Cosmos-data bases verwijderen.

* Verwijder een of meer Azure Cosmos-containers.

* De Azure Cosmos-items (bijvoorbeeld documenten) in een container verwijderen of wijzigen. Dit specifieke geval wordt meestal gegevens beschadiging genoemd.

* Een Data Base of containers van een gedeeld aanbod binnen een Data Base van het gedeelde aanbod worden verwijderd of beschadigd.

Azure Cosmos DB kunt de gegevens in alle bovenstaande scenario's herstellen. Bij het herstellen wordt een nieuw Azure Cosmos-account gemaakt om de herstelde gegevens te bewaren. De naam van het nieuwe account, als dit niet is opgegeven, heeft de indeling `<Azure_Cosmos_account_original_name>-restored1` . Het laatste cijfer wordt verhoogd wanneer er meerdere herstel pogingen worden gedaan. U kunt geen gegevens herstellen naar een vooraf gemaakt Azure Cosmos-account.

Wanneer u per ongeluk een Azure Cosmos-account verwijdert, kunnen we de gegevens herstellen naar een nieuw account met dezelfde naam, op voor waarde dat de account naam niet wordt gebruikt. U wordt aangeraden het account niet opnieuw te maken nadat u het hebt verwijderd. Omdat het niet alleen voor komt dat de herstelde gegevens dezelfde naam gebruiken, maar ook de juiste account wordt gedetecteerd om van moeilijk te herstellen.

Wanneer u per ongeluk een Azure Cosmos-data base verwijdert, kunnen we de gehele data base of een subset van de containers in die data base herstellen. Het is ook mogelijk om specifieke containers in data bases te selecteren en deze terug te zetten naar een nieuw Azure Cosmos-account.

Wanneer u per ongeluk een of meer items in een container verwijdert of wijzigt (het geval van gegevens beschadiging), moet u de tijd opgeven waarop u wilt herstellen. Tijd is belang rijk als er gegevens beschadigd zijn. Omdat de container Live is, wordt de back-up nog steeds uitgevoerd, dus als u na de Bewaar periode blijft (de standaard instelling is acht uur), worden de back-ups overschreven. **Als u wilt voor komen dat de back-up wordt overschreven, verhoogt u de retentie van de back-up voor uw account tot ten minste zeven dagen. Het is het beste om uw Bewaar periode binnen 8 uur na de beschadiging van de gegevens te verg Roten.**

Als u uw gegevens per ongeluk hebt verwijderd of beschadigd, neemt u binnen 8 uur contact op met [Azure-ondersteuning](https://azure.microsoft.com/support/options/) , zodat het Azure Cosmos DB team u kan helpen bij het herstellen van de gegevens van de back-ups. Op deze manier kan het ondersteunings team van Azure Cosmos DB voldoende tijd hebben om uw account te herstellen.

Als u de door Voer inricht op database niveau, gebeurt het back-up-en herstel proces in dit geval op het hele database niveau en niet op het niveau van de afzonderlijke containers. In dergelijke gevallen kunt u geen subset van containers selecteren die u wilt herstellen.

## <a name="migrate-data-to-the-original-account"></a>Gegevens migreren naar het oorspronkelijke account

Het belangrijkste doel van het terugzetten van gegevens is het herstellen van de gegevens die u per ongeluk hebt verwijderd of gewijzigd. Daarom raden we u aan eerst de inhoud van de herstelde gegevens te controleren om er zeker van te zijn dat deze bevat wat u verwacht. Later kunt u de gegevens opnieuw naar het primaire account migreren. Hoewel het mogelijk is om het herstelde account te gebruiken als uw nieuwe actieve account, is dit niet de aanbevolen optie als u werk belastingen voor productie hebt.  

Hier volgen verschillende manieren om gegevens terug te migreren naar het oorspronkelijke Azure Cosmos-account:

* Gebruik het [Azure Cosmos DB hulp programma voor gegevens migratie](import-data.md).
* Gebruik de [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Gebruik de [wijzigings feed](change-feed.md) in azure Cosmos db.
* U kunt uw eigen aangepaste code schrijven.

Zorg ervoor dat u de herstelde accounts verwijdert zodra u uw gegevens hebt gemigreerd, omdat er lopende kosten in rekening worden gebracht.

## <a name="next-steps"></a>Volgende stappen

Hierna vindt u informatie over het herstellen van gegevens uit een Azure Cosmos-account of over het migreren van gegevens naar een Azure Cosmos-account

* Neem contact op met de ondersteuning van Azure, maak [een ticket van de Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om een herstel aanvraag te doen.
* [Gegevens herstellen vanuit een Azure Cosmos-account](how-to-backup-and-restore.md)
* [Gebruik Cosmos DB Change feed](change-feed.md) om gegevens naar Azure Cosmos DB te verplaatsen.
* [Gebruik Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) om gegevens te verplaatsen naar Azure Cosmos db.

