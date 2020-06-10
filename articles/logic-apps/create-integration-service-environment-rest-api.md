---
title: Integratie service omgevingen (ISEs) met Logic Apps REST API maken
description: Maak een integratie service omgeving (ISE) met behulp van de Logic Apps REST API zodat u Azure Virtual Networks (VNETs) kunt openen vanuit Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: d33207639ebef912307a3c594ec274fd9609bd67
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84656534"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Maak een integratie service omgeving (ISE) met behulp van de Logic Apps REST API

In dit artikel wordt beschreven hoe u een [ISE ( *Integration service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maakt via de Logic apps rest API voor scenario's waarin uw Logic apps en integratie accounts toegang nodig hebben tot een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md). Een ISE is een speciale omgeving waarin speciale opslag en andere bronnen worden gebruikt die gescheiden worden gehouden van de ' wereld wijde ' multi tenant-Logic Apps service. Deze schei ding vermindert ook de invloed die andere Azure-tenants mogelijk hebben op de prestaties van uw apps. Een ISE biedt u ook uw eigen vaste IP-adressen. Deze IP-adressen zijn gescheiden van de statische IP-adressen die worden gedeeld door de Logic apps in de open bare multi tenant-service.

U kunt ook een ISE maken met behulp van de voor [beeld-Azure Resource Manager Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) start-sjabloon of met behulp van de [Azure Portal](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Logic apps, ingebouwde triggers, ingebouwde acties en connectors die worden uitgevoerd in uw ISE, gebruiken een prijs plan dat verschilt van het prijs plan op basis van verbruik. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over de prijzen en facturerings werkzaamheden voor ISEs. Zie [Logic apps prijzen](../logic-apps/logic-apps-pricing.md)voor prijs tarieven.

## <a name="prerequisites"></a>Vereisten

* Dezelfde vereisten [prerequisites](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) en [voor waarden om toegang te krijgen tot uw ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) als bij het maken van een ISE in de Azure Portal

* Een hulp programma dat u kunt gebruiken om uw ISE te maken door de Logic Apps REST API aan te roepen met een HTTPS-aanvraag. U kunt bijvoorbeeld [postman](https://www.getpostman.com/downloads/)gebruiken of u kunt een logische app maken die deze taak uitvoert.

## <a name="send-the-request"></a>De aanvraag verzenden

Als u uw ISE wilt maken door de Logic Apps REST API aan te roepen, maakt u deze HTTPS-aanvraag:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Voor de versie Logic Apps REST API 2019-05-01 moet u uw eigen HTTP PUT-aanvraag voor ISE-connectors maken.

Het volt ooien van de implementatie duurt gewoonlijk binnen twee uur. De implementatie kan af en toe Maxi maal vier uur duren. Als u de implementatie status wilt controleren, selecteert u in de [Azure Portal](https://portal.azure.com)op de Azure-werk balk het meldingen pictogram, waarmee het deel venster meldingen wordt geopend.

> [!NOTE]
> Als de implementatie mislukt of als u uw ISE verwijdert, kan het tot een uur duren voordat de subnetten worden vrijgegeven. Deze vertraging betekent dat u mogelijk moet wachten voordat u deze subnetten opnieuw gebruikt in een andere ISE.
>
> Als u het virtuele netwerk verwijdert, duurt het over het algemeen Maxi maal twee uur voordat de subnetten worden vrijgegeven, maar deze bewerking kan langer duren. 
> Wanneer u virtuele netwerken verwijdert, moet u ervoor zorgen dat er nog geen resources zijn verbonden. 
> Zie [virtueel netwerk verwijderen](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Aanvraagheader

Neem de volgende eigenschappen op in de aanvraag header:

* `Content-type`: Stel de waarde van deze eigenschap in op `application/json` .

* `Authorization`: Stel deze eigenschaps waarde in op het Bearer-token voor de klant die toegang heeft tot het Azure-abonnement of de resource groep die u wilt gebruiken.

<a name="request-body"></a>

## <a name="request-body"></a>Aanvraagbody

Hier volgt de syntaxis van de hoofd tekst van de aanvraag, waarin de eigenschappen worden beschreven die moeten worden gebruikt bij het maken van uw ISE. Als u een ISE wilt maken die het gebruik van een zelfondertekend certificaat dat is geïnstalleerd op de `TrustedRoot` locatie, neemt u het `certificates` object op in de sectie ISE-definitie `properties` . Voor een bestaande ISE kunt u een PATCH aanvraag alleen voor het object verzenden `certificates` . Zie ook [http-connector-zelfondertekende certificaten](../connectors/connectors-native-http.md#self-signed)voor meer informatie over het gebruik van zelfondertekende certificaten.

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
      },
      // Include `certificates` object to enable self-signed certificate support
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>Voor beeld van aanvraag tekst

In dit voor beeld van de aanvraag tekst worden de voorbeeld waarden weer gegeven:

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
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Resources toevoegen aan integratieserviceomgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integratieserviceomgevingen beheren](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

