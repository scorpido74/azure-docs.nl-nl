---
title: 'Zelfstudie: Ankers delen met Azure Cosmos DB'
description: In deze zelfstudie leert u hoe u Azure Spatial Anchors-id's deelt op Android/iOS-apparaten in Unity met een back-endservice en Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77615155"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Zelfstudie: Azure Spatial Anchors delen voor sessies en apparaten met een Azure Cosmos DB-back-end

Deze zelfstudie is een voortzetting van het delen van [Azure Spatial Anchors over sessies en apparaten.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Het zal u begeleiden door het proces van het toevoegen van een paar meer mogelijkheden om Azure Cosmos DB te laten dienen als de back-end opslag terwijl het delen van azure ruimtelijke ankers over sessies en apparaten.

![GIF illustreert objectpersistentie](./media/persistence.gif)

Het is vermeldenswaard dat, hoewel je Unity en Azure Cosmos DB gebruikt in deze zelfstudie, het je een voorbeeld geeft van hoe je classificaties voor ruimtelijke ankers op verschillende apparaten delen. U kunt andere talen en back-endtechnologieën gebruiken om hetzelfde doel te bereiken. Ook de ASP.NET Core web app gebruikt in deze tutorial vereist de .NET Core 2.2 SDK. Het draait prima op Web Apps voor Windows, maar het zal momenteel niet draaien op Web Apps voor Linux.

## <a name="create-a-database-account"></a>Een databaseaccount maken

Voeg een Azure Cosmos Database toe aan de resourcegroep die u eerder hebt gemaakt.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Kopieer `Connection String` de omdat je het nodig hebt.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Kleine wijzigingen aanbrengen in de SharingService-bestanden

Open **in** `SharingService\Startup.cs`Solution Explorer .

Zoek `#define INMEMORY_DEMO` boven aan het bestand en geef die regel op. Sla het bestand op.

Open **in** `SharingService\appsettings.json`Solution Explorer .

Zoek `StorageConnectionString` de eigenschap en stel de waarde `Connection String` in op dezelfde waarde als de waarde die u hebt gekopieerd in de [stap Een databaseaccount maken.](#create-a-database-account) Sla het bestand op.

U de deelservice opnieuw publiceren en de voorbeeld-app uitvoeren.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="unity-20193"></a>Eenheid 2019.3

Vanwege het breken van veranderingen wordt Unity 2019.3 momenteel niet ondersteund. Gebruik Unity 2019.1 of 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure Cosmos DB gebruikt voor het delen van anker-id's op apparaten. Ga verder naar de volgende zelfstudie voor meer informatie over het gebruik van Azure Spatial Anchors in een nieuwe Unity HoloLens-app.

> [!div class="nextstepaction"]
> [Een nieuwe HoloLens Unity-app starten](./tutorial-new-unity-hololens-app.md)
