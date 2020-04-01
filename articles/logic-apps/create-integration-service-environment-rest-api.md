---
title: Integratieserviceomgevingen (ISE's) maken met Logic Apps REST API
description: Een integratieserviceomgeving (ISE) maken met behulp van de LOGIC Apps REST API, zodat u toegang hebt tot Azure virtual networks (VNETs) vanuit Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 0670331d2338b4b6419ffbff1452b5fbac91029f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478831"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Een integratieserviceomgeving (ISE) maken met de Logic Apps REST API

In dit artikel ziet u hoe u een [ *integratieserviceomgeving* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maakt via de Logic Apps REST API voor scenario's waarin uw logische apps en integratieaccounts toegang nodig hebben tot een [virtueel Azure-netwerk.](../virtual-network/virtual-networks-overview.md) Een ISE is een geïsoleerde omgeving die gebruik maakt van speciale opslag en andere resources die gescheiden worden gehouden van de 'globale' Logic Apps-service met meerdere tenant. Deze scheiding vermindert ook de impact die andere Azure-tenants kunnen hebben op de prestaties van uw apps. Een ISE voorziet u ook van uw eigen statische IP-adressen. Deze IP-adressen staan los van de statische IP-adressen die worden gedeeld door de logische apps in de openbare, multi-tenant service.

U ook een ISE maken met behulp van de [sjabloon Voor snelstartvan Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) of met behulp van de [Azure-portal.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

> [!IMPORTANT]
> Logische apps, ingebouwde triggers, ingebouwde acties en connectors die in uw ISE worden uitgevoerd, gebruiken een prijsplan dat verschilt van het prijsplan voor consumptie. Zie het [prijsmodel van Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over hoe prijzen en facturering werken voor ISE's. Zie [Logic Apps-prijzen](../logic-apps/logic-apps-pricing.md)voor prijstarieven.

## <a name="prerequisites"></a>Vereisten

* Dezelfde [voorwaarden](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) en [vereisten om toegang voor uw ISE in te schakelen](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) als wanneer u een ISE maakt in de Azure-portal

* Een tool die u gebruiken om uw ISE te maken door de Logic Apps REST API aan te roepen met een HTTPS PUT-aanvraag. U bijvoorbeeld [Postman](https://www.getpostman.com/downloads/)gebruiken of een logische app maken die deze taak uitvoert.

## <a name="send-the-request"></a>De aanvraag verzenden

Als u uw ISE wilt maken door de LOGIC Apps REST API aan te roepen, voert u dit HTTPS PUT-verzoek in:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> De Logic Apps REST API 2019-05-01 versie vereist dat u uw eigen HTTP PUT-aanvraag voor ISE-connectors maakt.

Implementatie duurt meestal binnen twee uur om te voltooien. Af en toe kan de implementatie tot vier uur duren. Als u de implementatiestatus wilt controleren, selecteert u in de [Azure-portal](https://portal.azure.com)op uw Azure-werkbalk het pictogram meldingen, waarmee het deelvenster meldingen wordt geopend.

> [!NOTE]
> Als de implementatie mislukt of als u uw ISE verwijdert, kan het tot een uur duren voordat azure uw subnetten vrijgeeft. Deze vertraging betekent dat u mogelijk moet wachten voordat u deze subnetten in een andere ISE opnieuw gebruikt.
>
> Als u uw virtuele netwerk verwijdert, duurt het over het algemeen maximaal twee uur voordat azure uw subnetten vrijgeeft, maar deze bewerking kan langer duren. 
> Controleer bij het verwijderen van virtuele netwerken of er nog steeds geen bronnen zijn verbonden. 
> Zie [Virtueel netwerk verwijderen](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Aanvraagheader

Neem in de koptekst van de aanvraag de volgende eigenschappen op:

* `Content-type`: Stel deze `application/json`eigenschapswaarde in op .

* `Authorization`: Stel deze eigenschapswaarde in op het token aan toonder voor de klant die toegang heeft tot het Azure-abonnement of de brongroep die u wilt gebruiken.

### <a name="request-body-syntax"></a>Syntaxis van de hoofdtekst van het verzoek

Hier is de syntaxis van de aanvraaginstantie, waarin de eigenschappen worden beschreven die moeten worden gebruikt wanneer u uw ISE maakt:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>Voorbeeld van het hoofdvoorbeeld van aanvraag

In dit voorbeeldaanvraaghoofd worden de voorbeeldwaarden weergegeven:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Resources toevoegen aan integratieserviceomgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integratieserviceomgevingen beheren](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

