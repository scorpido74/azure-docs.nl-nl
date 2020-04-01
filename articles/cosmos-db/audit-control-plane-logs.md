---
title: Azure Cosmos DB-besturingsvlakbewerkingen controleren
description: Meer informatie over het controleren van de bewerkingen van het besturingsvlak, zoals het toevoegen van een regio, updatedoorvoer, regiofailover, het toevoegen van een VNet enz.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420248"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Azure Cosmos DB-besturingsvlakbewerkingen controleren

Beheervlakbewerkingen omvatten wijzigingen in het Azure Cosmos-account of de container. Maak bijvoorbeeld een Azure Cosmos-account aan, voeg een regio toe, werk doorvoer bij, regiofailover, voeg een VNet toe enz. In dit artikel wordt uitgelegd hoe u de bewerkingen van het besturingsvlak in Azure Cosmos DB controleren.

## <a name="disable-key-based-metadata-write-access"></a>Schrijftoegang voor metagegevens op basis van sleutels uitschakelen
 
Schakel voordat u de beheervlakbewerkingen in Azure Cosmos DB controleert, de op sleutels gebaseerde metagegevens voor schrijftoegang op uw account uit. Wanneer toegang tot metagegevens op basis van sleutelgegevens is uitgeschakeld, kunnen clients die verbinding maken met het Azure Cosmos-account via accountsleutels geen toegang krijgen tot het account. U schrijftoegang uitschakelen `disableKeyBasedMetadataWriteAccess` door de eigenschap op true in te stellen. Nadat u deze eigenschap hebt ingesteld, kunnen wijzigingen in een resource plaatsvinden van een gebruiker met alleen de rbac-rol en referenties (Role-based Access Control). Zie het artikel Wijzigingen voorkomen uit [SDKs](role-based-access-control.md#preventing-changes-from-cosmos-sdk) voor meer informatie over het instellen van deze eigenschap.

 Houd rekening met de volgende punten bij het uitschakelen van de schrijftoegang met metagegevens:

* Evalueer en zorg ervoor dat uw toepassingen geen metagegevens oproepen die de bovenstaande bronnen wijzigen (bijvoorbeeld verzameling maken, doorvoer bijwerken, ...) met behulp van de SDK- of accountsleutels.

* Momenteel gebruikt de Azure-portal accountsleutels voor metagegevensbewerkingen en daarom worden deze bewerkingen geblokkeerd. U ook de sjabloonimplementaties azure CLI, SDKs of Resource Manager gebruiken om dergelijke bewerkingen uit te voeren.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Diagnostische logboeken inschakelen voor bewerkingen van besturingsvlak

U diagnostische logboeken inschakelen voor beheervlakbewerkingen met behulp van de Azure-portal. Gebruik de volgende stappen om logboekregistratie op besturingsvlakbewerkingen in te schakelen:

1. Meld u aan bij [azure portal](https://portal.azure.com) en navigeer naar uw Azure Cosmos-account.

1. Open het deelvenster **Diagnostische instellingen** en geef een **naam** op voor de logboeken die u wilt maken.

1. Selecteer **ControlPlaneRequests** voor logboektype en selecteer de optie **Verzenden naar logboekanalyse.**

U de logboeken ook opslaan in een opslagaccount of streamen naar een gebeurtenishub. In dit artikel ziet u hoe u logboeken verzendt om analyses te registreren en deze vervolgens op te vragen. Nadat u dit hebt ingeschakeld, duurt het enkele minuten voordat de diagnostische logboeken van kracht worden. Alle bewerkingen van het controlevlak die na dat punt worden uitgevoerd, kunnen worden bijgehouden. In de volgende schermafbeelding ziet u hoe u logboeken van het controlevlak inschakelt:

![Logboekregistratie van besturingsvlakaanvragen inschakelen](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>De besturingsvlakbewerkingen weergeven

Nadat u logboekregistratie hebt ingeschakeld, gebruikt u de volgende stappen om bewerkingen voor een specifiek account op te sporen:

1. Meld u aan bij [Azure portal](https://portal.azure.com).
1. Open het tabblad **Monitor** vanuit de navigatie aan de linkerkant en selecteer vervolgens het deelvenster **Logboeken.** Het opent een gebruikersinterface waar u eenvoudig query's uitvoeren met dat specifieke account in het bereik. Voer de volgende query uit om de logboeken van het besturingselement te bekijken:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

De volgende screenshots maken logboeken wanneer een VNET wordt toegevoegd aan een Azure Cosmos-account:

![Vliegtuiglogboeken beheren wanneer een VNet wordt toegevoegd](./media/audit-control-plane-logs/add-ip-filter-logs.png)

De volgende screenshots leggen logboeken vast wanneer de doorvoer van een Cassandra-tabel wordt bijgewerkt:

![Vliegtuiglogboeken beheren wanneer de doorvoer wordt bijgewerkt](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>De identiteit identificeren die is gekoppeld aan een specifieke bewerking

Als u verder wilt debuggen, u een specifieke bewerking in het **activiteitslogboek** identificeren met behulp van de activiteits-id of aan de tijdstempel van de bewerking. Tijdstempel wordt gebruikt voor sommige Resource Manager-clients waarbij de activiteits-id niet expliciet wordt doorgegeven. Het logboek Activiteit geeft details over de identiteit waarmee de bewerking is gestart. In de volgende schermafbeelding ziet u hoe u de activiteits-id gebruikt en de bewerkingen die eraan zijn gekoppeld, vinden in het logboek Activiteit:

![De activiteits-id gebruiken en de bewerkingen zoeken](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="next-steps"></a>Volgende stappen

* [Azure-monitor voor Azure Cosmos DB verkennen](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Controleren en debuggen met statistieken in Azure Cosmos DB](use-metrics.md)