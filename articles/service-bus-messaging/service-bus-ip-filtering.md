---
title: Firewall regels voor Azure Service Bus | Microsoft Docs
description: Hoe u firewall regels kunt gebruiken om verbindingen van bepaalde IP-adressen toe te staan Azure Service Bus.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 02d6e150e638321e11a8dec9838e360faa00783e
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280943"
---
# <a name="use-firewall-rules"></a>Firewall regels gebruiken

Voor scenario's waarin Azure Service Bus alleen toegankelijk is vanaf bepaalde bekende sites, kunt u met firewall regels regels configureren voor het accepteren van verkeer dat afkomstig is van specifieke IPv4-adressen. Bijvoorbeeld, kunnen deze adressen die van een zakelijke NAT-gateway zijn.

## <a name="when-to-use"></a>Wanneer gebruikt u dit?

Als u wilt instellen Service Bus zodanig dat er alleen verkeer wordt ontvangen van een opgegeven bereik van IP-adressen en alle andere gegevens afwijzen, kunt u een *firewall* gebruiken om service bus-eind punten van andere IP-adressen te blok keren. U gebruikt bijvoorbeeld Service Bus met [Azure Express route][express-route] om particuliere verbindingen te maken met uw on-premises infra structuur. 

## <a name="how-filter-rules-are-applied"></a>Hoe regels worden toegepast

De IP-filter regels worden toegepast op het niveau van de Service Bus naam ruimte. Daarom de regels van toepassing op alle verbindingen van clients met behulp van een ondersteund protocol.

Een verbindings poging van een IP-adres dat niet overeenkomt met een toegestane IP-regel op de Service Bus naam ruimte, wordt geweigerd als niet-geautoriseerd. Het antwoord wordt niet vermeld voor de IP-regel.

## <a name="default-setting"></a>Standaardinstelling

Het **IP-filter** raster in de portal voor service bus is standaard leeg. Deze standaard instelling betekent dat de naam ruimte verbinding kan maken met elk IP-adres. Deze instelling is gelijk aan een regel waarmee het 0.0.0.0/0 IP-adresbereik accepteert.

## <a name="ip-filter-rule-evaluation"></a>Evaluatie van IP-filter

IP-filter regels worden in volg orde toegepast en de eerste regel die overeenkomt met het IP-adres, bepaalt de accepteren of afwijzen.

>[!WARNING]
> Het implementeren van firewall regels kan verhinderen dat andere Azure-Services communiceren met Service Bus.
>
> Vertrouwde micro soft-services worden niet ondersteund wanneer IP-filtering (firewall regels) worden geïmplementeerd en binnenkort beschikbaar wordt gesteld.
>
> Algemene scenario's voor Azure die niet werken met IP-filtering (Let op: de lijst is **niet** volledig)-
> - Azure Stream Analytics
> - Integratie met Azure Event Grid
> - Azure-IoT Hub routes
> - Azure IoT-Device Explorer
>
> De onderstaande micro soft-services moeten zich in een virtueel netwerk bevinden
> - Azure App Service
> - Azure Functions

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Een virtueel netwerk en een firewall regel met Azure Resource Manager sjablonen maken

> [!IMPORTANT]
> Firewalls en virtuele netwerken worden alleen ondersteund in de **Premium** -laag van service bus.

Met de volgende Resource Manager-sjabloon kan een regel voor een virtueel netwerk worden toegevoegd aan een bestaande Service Bus naam ruimte.

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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

Zie de volgende koppeling voor meer informatie over het beperken van toegang tot Service Bus voor virtuele netwerken van Azure:

- [Service-eind punten Virtual Network voor Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
