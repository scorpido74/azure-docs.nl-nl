---
title: Virtual Network-service-eindpunten - Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over het toevoegen van een service-eind punt van micro soft. EventHub aan een virtueel netwerk.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 2ac89444bde4e2efc918aced9d76c099eb792557
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966000"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Service-eindpunten voor Virtueelnetwerk gebruiken met Azure Event Hubs

Dankzij de integratie van Event Hubs met de [service-eind punten van Virtual Network (VNet)][vnet-sep] is beveiligde toegang mogelijk tot berichten mogelijkheden van werk belastingen, zoals virtuele machines die zijn gebonden aan virtuele netwerken, waarbij het netwerkpad van het netwerk verkeer aan beide uiteinden wordt beveiligd.

Eenmaal geconfigureerd om te zijn gebonden aan ten minste één subnet-service-eind punt van een virtueel netwerk, accepteert de respectieve Event Hubs naam ruimte geen verkeer meer vanaf een wille keurige locatie, maar gemachtigd subnetten in virtuele netwerken. Vanuit het perspectief virtueel netwerk met de binding van een Event Hubs-naamruimte met een service-eindpunt configureert een geïsoleerde netwerken tunnel vanuit het subnet van het virtuele netwerk naar de messaging-service. 

Het resultaat is een privé- en geïsoleerd relatie tussen de werkbelastingen die zijn gebonden aan het subnet en de respectieve Event Hubs-naamruimte, ondanks het waarneembare netwerkadres van de berichten service eindpunt wordt in een openbare IP-adresbereik. Er is een uitzonde ring op dit gedrag. Als u een service-eind punt inschakelt, wordt de denyall-regel standaard ingeschakeld in de IP-firewall die aan het virtuele netwerk is gekoppeld. U kunt specifieke IP-adressen toevoegen aan de IP-firewall om toegang tot het open bare eind punt van Event hub mogelijk te maken. 


>[!WARNING]
> De implementatie van de integratie van virtuele netwerken kan voorkomen dat andere Azure-services interactie hebben met Event Hubs.
>
> Vertrouwde micro soft-services worden niet ondersteund wanneer virtuele netwerken zijn geïmplementeerd.
>
> Algemene scenario's voor Azure die niet met virtuele netwerken werken (Houd er rekening mee dat de lijst **niet** volledig is)-
> - Integratie met Azure Monitor. U kunt geen Diagnostische logboeken van **andere** Azure-Services streamen naar Event hubs. U kunt de diagnostische logboeken van Azure echter inschakelen op het Event Hub zelf. Dit is hetzelfde geval wanneer u de firewall (IP-filtering) hebt ingeschakeld.
> - Azure Stream Analytics
> - Integratie met Azure Event Grid
> - Azure-IoT Hub routes
> - Azure IoT-Device Explorer
>
> De onderstaande micro soft-services moeten zich in een virtueel netwerk bevinden
> - Azure Web Apps
> - Azure Functions

> [!IMPORTANT]
> Virtuele netwerken worden ondersteund in de lagen **Standard** en **Dedicated** van Event Hubs. Deze worden niet ondersteund in de Basic-laag.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Geavanceerde beveiliging mogelijke scenario's met VNet-integratie 

Oplossingen die een strakke en compartmentalized beveiliging vereisen en waarbij virtuele netwerk-subnetten de segmentatie tussen de compartmentalized-services bieden, is nog steeds communicatie paden nodig tussen services die zich in deze compartimenten bevinden.

Een direct IP-route tussen de afdelingen, inclusief de uitvoering van HTTPS via TCP/IP, voert u het risico van misbruik van zwakke plekken van de netwerklaag op omhoog. Messaging-services bieden een volledig geïsoleerd communicatiepaden, waar berichten ook naar de schijf als ze worden overgedragen tussen de partijen worden geschreven. Workloads in twee verschillende virtuele netwerken die zijn beide gekoppeld aan hetzelfde exemplaar van de Event Hubs kunnen communiceren efficiënt en betrouwbaar via berichten, terwijl de respectieve netwerk isolatie grens integriteit behouden blijft.
 
Dit betekent dat de beveiliging van uw gevoelige cloudoplossingen niet alleen toegang krijgen tot toonaangevende betrouwbare en schaalbare asynchrone messaging mogelijkheden van Azure, maar ze kunnen nu gebruiken messaging communicatiepaden tussen veilige oplossing maken die reizigerscompartimenten zijn inherent veiliger is dan wat met een peer-to-peer communicatiemodus is, met inbegrip van HTTPS en andere TLS beveiligde socket-protocollen.

## <a name="bind-event-hubs-to-virtual-networks"></a>Eventhubs koppelen aan virtuele netwerken

*Regels voor virtueel netwerk* zijn van de firewall beveiligingsfunctie die bepaalt of de Azure Event Hubs-naamruimte verbindingen van het subnet van een bepaalde virtuele netwerk aanvaardt.

Een Event Hubs-naamruimte binden aan een virtueel netwerk is een proces in twee stappen. U moet eerst een **Virtual Network Service-eind punt** maken op een Virtual Network subnet en dit inschakelen voor ' micro soft. EventHub ', zoals wordt uitgelegd in het [overzicht van service-eind punten][vnet-sep]. Nadat u het service-eindpunt hebt toegevoegd, verbindt u de Event Hubs-naamruimte toe met een *regel voor virtuele netwerken*.

De regel van het virtuele netwerk is een koppeling van de Event Hubs naam ruimte met een subnet van een virtueel netwerk. Terwijl de regel bestaat, worden alle werkbelastingen die zijn gekoppeld aan het subnet toegang tot de Event Hubs-naamruimte toegekend. Eventhubs zelf nooit uitgaande verbindingen maakt, heeft niet nodig om toegang te krijgen en is daarom nooit toegang verleend tot uw subnet door in te schakelen met deze regel.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Een regel voor virtuele netwerken maken met Azure Resource Manager-sjablonen

De volgende Resource Manager-sjabloon kunt een regel voor virtuele netwerken toe te voegen aan een bestaande Event Hubs-naamruimte.

Sjabloonparameters:

* **namespaceName**: Event Hubs-naamruimte.
* **vnetRuleName**: naam voor de regel van het Virtueelnetwerk moet worden gemaakt.
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
                    "service": "Microsoft.EventHub"
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
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
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
- [IP-filtering van Azure Event Hubs][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
