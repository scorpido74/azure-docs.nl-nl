---
title: Firewall regels voor Azure Event Hubs | Microsoft Docs
description: Gebruik firewall regels om verbindingen van specifieke IP-adressen toe te staan aan Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: 2350586501fae84726aa2aa2438ea676b90c1dbb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279675"
---
# <a name="use-firewall-rules"></a>Firewall regels gebruiken

Voor scenario's waarin Azure Event Hubs alleen toegankelijk moet zijn vanaf bepaalde bekende sites, kunt u met firewall regels regels configureren voor het accepteren van verkeer dat afkomstig is van specifieke IPv4-adressen. Bijvoorbeeld, kunnen deze adressen die van een zakelijke NAT-gateway zijn.

## <a name="when-to-use"></a>Wanneer gebruikt u dit?

Als u uw Event Hubs naam ruimte zo wilt instellen dat alleen verkeer van een opgegeven IP-adres bereik wordt ontvangen en alles wordt geweigerd, kunt u gebruikmaken van een *firewall regel* voor het blok keren van Event hub-eind punten van andere IP-adressen. Als u bijvoorbeeld Event Hubs met [Azure Express route][express-route]gebruikt, kunt u een *firewall regel* maken om het verkeer van uw on-premises infra structuur-IP-adressen te beperken.

## <a name="how-filter-rules-are-applied"></a>Hoe regels worden toegepast

De IP-filterregels worden toegepast op het niveau van de Event Hubs-naamruimte. Daarom de regels van toepassing op alle verbindingen van clients met behulp van een ondersteund protocol.

Een verbindings poging van een IP-adres dat niet overeenkomt met een toegestane IP-regel op de Event Hubs naam ruimte, wordt geweigerd als niet-geautoriseerd. Het antwoord wordt niet vermeld voor de IP-regel.

## <a name="default-setting"></a>Standaardinstelling

Standaard de **IP-Filter** raster in de portal voor Event Hubs is leeg. Deze instelling betekent dat uw event hub verbindingen van elk IP-adres aanvaardt. Deze instelling is gelijk aan een regel waarmee het 0.0.0.0/0 IP-adresbereik accepteert.

## <a name="ip-filter-rule-evaluation"></a>Evaluatie van IP-filter

IP-filterregels worden toegepast in volgorde en de eerste regel die overeenkomt met het IP-adres bepaalt de actie accepteren of weigeren.

>[!WARNING]
> Het implementeren van firewalls kan verhinderen dat andere Azure-Services communiceren met Event Hubs.
>
> Vertrouwde micro soft-services worden niet ondersteund wanneer IP-filtering (firewalls) worden geïmplementeerd en binnenkort beschikbaar wordt gesteld.
>
> Algemene scenario's voor Azure die niet werken met IP-filtering (Let op: de lijst is **niet** volledig)-
> - Azure Stream Analytics
> - Integratie met Azure Event Grid
> - Azure-IoT Hub routes
> - Azure IoT-Device Explorer
>
> De onderstaande micro soft-services moeten zich in een virtueel netwerk bevinden
> - Azure Web Apps
> - Azure Functions

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Een firewall regel maken met Azure Resource Manager sjablonen

> [!IMPORTANT]
> Firewall regels worden ondersteund in de **standaard** -en **toegewezen** lagen van Event hubs. Deze worden niet ondersteund in de Basic-laag.

De volgende Resource Manager-sjabloon kunt een regel voor IP-filter toe te voegen aan een bestaande Event Hubs-naamruimte.

Sjabloonparameters:

- **ipMask** is één IPv4-adres of een blok IP-adressen in CIDR-notatie. Bijvoorbeeld, in CIDR vertegenwoordigt notatie 70.37.104.0/24 de 256 IPv4-adressen van 70.37.104.0 tot 70.37.104.255, met 24 uur per dag die wijzen op het aantal bits aanzienlijke voorvoegsel voor het bereik.

> [!NOTE]
> Hoewel er geen regels kunnen worden geweigerd, is voor de Azure Resource Manager sjabloon de standaard actie ingesteld op **' toestaan '** , waardoor verbindingen niet worden beperkt.
> Wanneer u Virtual Network of firewall regels maakt, moeten we de ***' defaultAction '*** wijzigen
> 
> from
> ```json
> "defaultAction": "Allow"
> ```
> tot
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Als u de sjabloon wilt implementeren, volgt u de instructies voor [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Volgende stappen

Virtuele netwerken, Zie de volgende koppeling voor beperken de toegang tot Event Hubs naar Azure:

- [Service-eind punten Virtual Network voor Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
