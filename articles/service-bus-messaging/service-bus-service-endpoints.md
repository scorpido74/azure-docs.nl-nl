---
title: Service-eind punten van virtueel netwerk-Azure Service Bus
description: Dit artikel bevat informatie over het toevoegen van een service-eind punt van micro soft. ServiceBus aan een virtueel netwerk.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 212cd96571561362003e7dcbd89efc5d2c54ab48
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980805"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Virtual Network Service-eind punten gebruiken met Azure Service Bus

Dankzij de integratie van Service Bus met de [service-eind punten van Virtual Network (VNet)][vnet-sep] is beveiligde toegang mogelijk tot berichten mogelijkheden van werk belastingen, zoals virtuele machines die zijn gebonden aan virtuele netwerken, waarbij het netwerkpad van het netwerk verkeer aan beide uiteinden wordt beveiligd.

Zodra het is geconfigureerd om te worden gebonden aan ten minste één subnet-service-eind punt van een virtueel netwerk, accepteert de respectieve Service Bus naam ruimte geen verkeer meer vanaf een wille keurige locatie, maar geautoriseerde virtuele netwerken. Vanuit het perspectief van het virtuele netwerk moet u een Service Bus naam ruimte binden aan een service-eind punt een geïsoleerde netwerk tunnel van het subnet van het virtuele netwerk naar de berichten service configureren.

Het resultaat is een privé-en geïsoleerde relatie tussen de werk belastingen die zijn gebonden aan het subnet en de betreffende Service Bus naam ruimte, ondanks het waarneem bare netwerk adres van het berichten service-eind punt in een openbaar IP-bereik.

>[!WARNING]
> Het implementeren van de integratie van virtuele netwerken kan voorkomen dat andere Azure-services interactie hebben met Service Bus.
>
> Vertrouwde micro soft-services worden niet ondersteund wanneer virtuele netwerken zijn geïmplementeerd.
>
> Algemene scenario's voor Azure die niet met virtuele netwerken werken (Houd er rekening mee dat de lijst **niet** volledig is)-
> - Azure Stream Analytics
> - Integratie met Azure Event Grid
> - Azure-IoT Hub routes
> - Azure IoT-Device Explorer
>
> De onderstaande micro soft-services moeten zich in een virtueel netwerk bevinden
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> Virtuele netwerken worden alleen ondersteund in de [Premium-laag](service-bus-premium-messaging.md) service bus naam ruimten.

## <a name="enable-service-endpoints-with-service-bus"></a>Service-eind punten inschakelen met Service Bus

Een belang rijke overweging bij het gebruik van VNet-service-eind punten met Service Bus is dat u deze eind punten niet moet inschakelen in toepassingen die gebruikmaken van de standaard-en Premium-laag Service Bus naam ruimten. Omdat de standaardlaag geen VNets ondersteunt, wordt het eind punt beperkt tot alleen naam ruimten van de Premium-laag.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Geavanceerde beveiliging mogelijke scenario's met VNet-integratie 

Oplossingen die voorziet in een hechte en compartmentalized beveiliging vereisen, en waarbij virtuele subnetten hardwareoplossing tussen de compartmentalized services, moeten doorgaans nog steeds communicatiepaden tussen services die zich bevinden in deze secties.

Een direct IP-route tussen de afdelingen, inclusief de uitvoering van HTTPS via TCP/IP, voert u het risico van misbruik van zwakke plekken van de netwerklaag op omhoog. Messaging-services bieden een volledig geïsoleerd communicatiepaden, waar berichten ook naar de schijf als ze worden overgedragen tussen de partijen worden geschreven. Werk belastingen in twee verschillende virtuele netwerken die beide zijn gebonden aan hetzelfde Service Bus-exemplaar kunnen efficiënt en betrouwbaar communiceren via berichten, terwijl de respectieve integriteit van de isolatie grens van het netwerk behouden blijft.
 
Dit betekent dat uw beveiligings gevoelige cloud oplossingen niet alleen toegang krijgen tot de toonaangevende betrouw bare en schaal bare asynchrone berichten mogelijkheden van Azure, maar ze kunnen nu berichten gebruiken om communicatie paden te maken tussen de veilige oplossings compartimenten die zijn inherent veiliger dan wat kan worden behaald met een peer-to-peer-communicatie modus, inclusief HTTPS en andere met TLS beveiligde socket protocollen.

## <a name="binding-service-bus-to-virtual-networks"></a>Service Bus binden aan virtuele netwerken

*Regels voor virtuele netwerken* zijn de firewall beveiligings functie waarmee wordt bepaald of de Azure service bus-server verbindingen van een bepaald subnet van een virtueel netwerk accepteert.

Het binden van een Service Bus naam ruimte aan een virtueel netwerk is een proces dat uit twee stappen bestaat. U moet eerst een **Virtual Network Service-eind punt** maken op een Virtual Network subnet en dit inschakelen voor ' micro soft. ServiceBus ', zoals wordt uitgelegd in het [overzicht van service-eind punten][vnet-sep]. Wanneer u het service-eind punt hebt toegevoegd, bindt u de naam ruimte van de Service Bus met een regel voor het *virtuele netwerk*.

De regel van het virtuele netwerk is een koppeling van de Service Bus naam ruimte met een subnet van een virtueel netwerk. Terwijl de regel bestaat, krijgen alle werk belastingen die aan het subnet zijn gebonden toegang tot de Service Bus naam ruimte. Service Bus zichzelf nooit uitgaande verbindingen tot stand brengt, geen toegang nodig heeft en daarom nooit toegang tot uw subnet verleend door deze regel in te scha kelen.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Een regel voor een virtueel netwerk met Azure Resource Manager sjablonen maken

Met de volgende Resource Manager-sjabloon kan een regel voor een virtueel netwerk worden toegevoegd aan een bestaande Service Bus naam ruimte.

Sjabloonparameters:

* **naam ruimte**: Service Bus naam ruimte.
* **virtualNetworkingSubnetId**: volledig gekwalificeerde pad van de Resource Manager voor het subnet van het virtuele netwerk; bijvoorbeeld `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` voor de standaard-subnet van een virtueel netwerk.

> [!NOTE]
> Hoewel er geen regels kunnen worden geweigerd, is voor de Azure Resource Manager sjabloon de standaard actie ingesteld op **' toestaan '** , waardoor verbindingen niet worden beperkt.
> Wanneer u Virtual Network of firewall regels maakt, moeten we de ***' defaultAction '*** wijzigen
> 
> uit
> ```json
> "defaultAction": "Allow"
> ```
> tot
> ```json
> "defaultAction": "Deny"
> ```
>

Sjabloon:

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Als u de sjabloon wilt implementeren, volgt u de instructies voor [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over virtuele netwerken, de volgende koppelingen:

- [Azure Virtual Network-Service-eind punten][vnet-sep]
- [IP-filtering Azure Service Bus][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
