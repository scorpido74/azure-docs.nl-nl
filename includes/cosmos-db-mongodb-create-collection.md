---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 0f471c5aefdb39396ea189984d32a9e8f5419182
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84200557"
---
U kunt nu het hulpprogramma Data Explorer in Azure Portal gebruiken om een API van Azure Cosmos DB voor MongoDB-database en -container te maken. 

1. Selecteer **Data Explorer** > **Nieuwe container**. 
    
    Uiterst rechts wordt het gedeelte **Container toevoegen** weergegeven. Mogelijk moet u naar rechts scrollen om het te kunnen zien.

    ![Data Explorer in Azure Portal, het deelvenster Container toevoegen](./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png)

2. Geef op de pagina **Container toevoegen** de instellingen voor de nieuwe container op.

    |Instelling|Voorgestelde waarde|Beschrijving
    |---|---|---|
    |**Database-id**|db|Voer *db* in als de naam voor de nieuwe database. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Controleer de optie **Doorvoer voor databases inrichten**, zodat u de doorvoer die is ingericht voor de database, kunt delen in alle containers in de database. Met deze optie bespaart u bovendien op de kosten. |
    |**Doorvoer**|400|Wijzig de doorvoer in 400 aanvraageenheden per seconde (RU/s). U kunt de doorvoer later opschalen als u de latentie wilt beperken. U kunt ook de [modus Automatische schaalaanpassing](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale) kiezen, waarmee u een reeks RU/s krijgt die naar behoefte dynamisch wordt vergroot of verkleind.| 
    |**Verzamelings-id**|coll|Voer `coll` in als de naam voor de nieuwe container. Voor id's van containers gelden dezelfde tekenvereisten als voor databasenamen.|
    |**Opslagcapaciteit**|Vast (10 GB)|Voer *Vast (10 GB)* voor deze toepassing in. Als u *Onbeperkt*selecteert, moet u een `Shard Key` maken, die voor alle opgenomen items nodig is.|
    |**Shardsleutel**| /_id| Het voorbeeld dat in dit artikel wordt beschreven, maakt geen gebruik van een shardsleutel, dus als u deze instelt op */_id*, wordt het veld Automatisch gegenereerde id als shardsleutel gebruikt. Meer informatie over sharding, ook wel partitioneren genoemd, vindt u in [Partitionering in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)|
        
    Selecteer **OK**. In Data Explorer worden de nieuwe database en container weergegeven.
