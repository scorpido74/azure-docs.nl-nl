---
title: Globale distributie met Azure DB Cosmos
description: Leer hoe u gegevens globaal kunt repliceren met Azure Cosmos DB in Azure Portal
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.custom: include file
ms.openlocfilehash: cb9899712fd023aaec41f4b6bb34357b60ecb106
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "75751934"
---
## <a name="add-global-database-regions-using-the-azure-portal"></a><a id="addregion"></a>Globale databaseregio's toevoegen met Azure Portal
Azure Cosmos DB is wereldwijd beschikbaar in alle [Azure-regio's][azureregions]. Nadat u het standaardconsistentieniveau voor uw databaseaccount hebt geselecteerd, kunt u een of meer regio's aan het account koppelen (afhankelijk van uw keuze van het standaardconsistentieniveau en behoeften voor globale distributie).

1. Klik in [Azure Portal](https://portal.azure.com/) in de linkerbalk op **Azure Cosmos DB**.
2. Selecteer op de pagina **Azure Cosmos DB** het databaseaccount dat u wilt wijzigen.
3. Klik op de accountpagina op **Gegevens wereldwijd repliceren** in het menu.
4. Selecteer op de pagina **Gegevens globaal repliceren** de regio's die u wilt toevoegen of verwijderen door op de kaart op regio's te klikken. Klik vervolgens op **Opslaan**. Er zijn kosten verbonden aan het toevoegen van regio's. Raadpleeg de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) of het artikel [Gegevens globaal distribueren met Azure Cosmos DB](../articles/cosmos-db/distribute-data-globally.md) voor meer informatie.
   
    ![Klik op de kaart op de regio's die u wilt toevoegen of verwijderen][1]
    
Zodra u een tweede regio toevoegt, komt de optie **Handmatige failover** beschikbaar op de pagina **Gegevens globaal repliceren** in de portal. Deze optie kunt u gebruiken om het failoverproces te testen of om de primaire regio voor schrijfbewerkingen te wijzigen. Als u een derde regio toevoegt, komt op dezelfde pagina de optie **Failoverprioriteiten** beschikbaar. Hiermee kunt u de failovervolgorde voor leesbewerkingen wijzigen.  

### <a name="selecting-global-database-regions"></a>Globale databaseregio's selecteren
Er zijn twee gangbare scenario's voor het configureren van twee of meer regio's:

1. Gegevenstoegang met lage latentie bieden aan eindgebruikers, ongeacht waar deze zich bevinden
2. Regionale tolerantie toevoegen voor bedrijfscontinuïteit en herstel na noodgevallen (BCDR)

In het eerste geval wordt het aanbevolen om zowel de toepassing als Azure Cosmos DB te implementeren in de regio's die overeenkomen met de gebieden waar de gebruikers van de toepassing zich bevinden.

Voor BCDR wordt het aanbevolen om regio's toe te voegen op basis van de regioparen die worden beschreven in het artikel [Bedrijfscontinuïteit en herstel na noodgevallen (BCDR): gekoppelde Azure-regio’s][bcdr].

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
