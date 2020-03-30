---
title: Azure-functies triggervoor Cosmos DB-verbindingsbeleid
description: Meer informatie over het configureren van het verbindingsbeleid dat wordt gebruikt door Azure Functions trigger voor Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 953121a9a15d4fef56d381e3aab85329fadacce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604966"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Het verbindingsbeleid configureren dat wordt gebruikt door Azure Functions trigger voor Cosmos DB

In dit artikel wordt beschreven hoe u het verbindingsbeleid configureren wanneer u de trigger voor Azure-functies voor Cosmos DB gebruikt om verbinding te maken met uw Azure Cosmos-account.

## <a name="why-is-the-connection-policy-important"></a>Waarom is het verbindingsbeleid belangrijk?

Er zijn twee verbindingsmodi - Direct-modus en Gateway-modus. Zie het artikel [over prestatietips](./performance-tips.md#networking) voor meer informatie over deze verbindingsmodi. **Gateway** wordt standaard gebruikt om alle verbindingen op de Azure-functietrigger voor Cosmos DB tot stand te brengen. Het is echter misschien niet de beste optie voor prestatiegerichte scenario's.

## <a name="changing-the-connection-mode-and-protocol"></a>De verbindingsmodus en het protocol wijzigen

Er zijn twee belangrijke configuratie-instellingen beschikbaar om het clientverbindingsbeleid te configureren: de **verbindingsmodus** en het **verbindingsprotocol**. U de standaardverbindingsmodus en het protocol wijzigen dat wordt gebruikt door de trigger van Azure-functies voor Cosmos DB en alle [Azure Cosmos DB-bindingen).](../azure-functions/functions-bindings-cosmosdb-v2-output.md) Als u de standaardinstellingen wilt `host.json` wijzigen, moet u het bestand in uw Azure Functions-project of Azure Functions App zoeken en de volgende [extra instelling](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)toevoegen:

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Waar `connectionMode` moet de gewenste verbindingsmodus (Direct `protocol` of Gateway) en het gewenste verbindingsprotocol (Tcp of Https) zijn. 

Als uw Azure Functions-project werkt met de runtime van Azure Functions V1, heeft de configuratie een klein naamverschil, u moet het gebruik gebruiken `documentDB` in plaats van: `cosmosDB`

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Wanneer u werkt met azure functions Consumption Plan Hosting-abonnement, heeft elk exemplaar een limiet in de hoeveelheid Socket-verbindingen die het kan onderhouden. Bij het werken met de Direct / TCP-modus worden door het ontwerp meer verbindingen gemaakt en kunnen ze de [limiet voor het verbruiksplan](../azure-functions/manage-connections.md#connection-limit)bereiken, in welk geval u de gatewaymodus gebruiken of uw Azure-functies uitvoeren in [de app-servicemodus.](../azure-functions/functions-scale.md#app-service-plan)

## <a name="next-steps"></a>Volgende stappen

* [Verbindingslimieten in Azure-functies](../azure-functions/manage-connections.md#connection-limit)
* [Prestatietips voor Azure Cosmos DB](./performance-tips.md)
* [Codevoorbeelden](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
