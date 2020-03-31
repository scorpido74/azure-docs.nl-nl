---
title: Eindpunten van virtual network-service - Azure-gebeurtenishubs | Microsoft Documenten
description: In dit artikel vindt u informatie over het toevoegen van een eindpunt van een Microsoft.EventHub-service aan een virtueel netwerk.
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
ms.openlocfilehash: 6de51c23bd6358a6f54fe3baf9e9b256047d4ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064899"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Eindpunten van virtuele netwerkservices gebruiken met Azure Event Hubs

De integratie van Event Hubs met [Virtual Network (VNet) Service Endpoints][vnet-sep] maakt veilige toegang tot messaging-mogelijkheden mogelijk van workloads zoals virtuele machines die gebonden zijn aan virtuele netwerken, waarbij het netwerkverkeer pad aan beide uiteinden wordt beveiligd.

Eenmaal geconfigureerd om gebonden te zijn aan ten minste één eindpunt van de virtuele netwerksubnetservice, accepteert de respectievelijke naamruimte van Gebeurtenishubs niet langer verkeer van overal, maar geautoriseerde subnetten in virtuele netwerken. Vanuit het perspectief van het virtuele netwerk configureert het binden van een naamruimte van Gebeurtenishubs aan een serviceeindpunt een geïsoleerde netwerktunnel van het virtuele netwerksubnet naar de berichtenservice. 

Het resultaat is een privé- en geïsoleerde relatie tussen de workloads die aan het subnet zijn gebonden en de respectievelijke naamruimte van Gebeurtenishubs, ondanks het waarneembare netwerkadres van het eindpunt van de berichtenservice dat zich in een openbaar IP-bereik bevindt. Er is een uitzondering op dit gedrag. Als u standaard een serviceeindpunt `denyall` inschakelt, wordt de regel in de [IP-firewall](event-hubs-ip-filtering.md) die aan het virtuele netwerk is gekoppeld, ingeschakeld. U specifieke IP-adressen toevoegen in de IP-firewall om toegang tot het openbare eindpunt van de Gebeurtenishub mogelijk te maken. 

> [!IMPORTANT]
> Virtuele netwerken worden ondersteund in de lagen **Standard** en **Dedicated** van Event Hubs. Het wordt niet ondersteund in de **basislaag.**

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Geavanceerde beveiligingsscenario's mogelijk gemaakt door VNet-integratie 

Oplossingen die strakke en gecompartimenteerde beveiliging vereisen en waar virtuele netwerksubnetten de segmentatie tussen de gecompartimenteerde services bieden, moeten nog steeds communicatiepaden worden nodig tussen services die zich in die compartimenten bevinden.

Elke directe IP-route tussen de compartimenten, inclusief die met HTTPS via TCP/IP, brengt het risico met zich mee dat kwetsbaarheden vanaf de netwerklaag worden gebruikt. Messaging-diensten bieden geïsoleerde communicatiepaden, waar berichten zelfs naar de schijf worden geschreven terwijl ze tussen partijen overstappen. Workloads in twee afzonderlijke virtuele netwerken die beide gebonden zijn aan dezelfde instantie Event Hubs, kunnen efficiënt en betrouwbaar communiceren via berichten, terwijl de respectieve integriteit van de netwerkisolatiegrens behouden blijft.
 
Dat betekent dat uw beveiligingsgevoelige cloudoplossingen niet alleen toegang krijgen tot azure toonaangevende betrouwbare en schaalbare asynchrone messaging-mogelijkheden, maar ze kunnen nu messaging gebruiken om communicatiepaden te maken tussen beveiligde oplossingscompartimenten die zijn inherent veiliger dan wat haalbaar is met een peer-to-peer communicatiemodus, inclusief HTTPS en andere TLS-beveiligde socketprotocollen.

## <a name="bind-event-hubs-to-virtual-networks"></a>Gebeurtenishubs binden aan virtuele netwerken

**Virtuele netwerkregels** zijn de beveiligingsfunctie voor firewalls die bepaalt of uw Azure Event Hubs naamruimte verbindingen van een bepaald virtueel netwerksubnet accepteert.

Het binden van een naamruimte voor gebeurtenishubs aan een virtueel netwerk is een proces in twee stappen. U moet eerst een eindpunt van de **virtuele netwerkservice** maken op het subnet van een virtueel netwerk en dit inschakelen voor **Microsoft.EventHub,** zoals uitgelegd in het artikel [over eindpuntvan][vnet-sep] de service. Zodra u het serviceeindpunt hebt toegevoegd, bindt u de naamruimte van gebeurtenishubs eraan met een **virtuele netwerkregel**.

De virtuele netwerkregel is een koppeling van de naamruimte van gebeurtenishubs met een virtueel netwerksubnet. Hoewel de regel bestaat, krijgen alle workloads die aan het subnet zijn gebonden, toegang tot de naamruimte van gebeurtenishubs. Event Hubs zelf maakt nooit uitgaande verbindingen, hoeft geen toegang te krijgen en krijgt daarom nooit toegang tot je subnet door deze regel in te schakelen.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie ziet u hoe u Azure-portal gebruiken om een eindpunt voor virtuele netwerkservice toe te voegen. Om de toegang te beperken, moet u het eindpunt van de virtuele netwerkservice voor deze naamruimte voor gebeurtenishubs integreren.

1. Navigeer naar de **naamruimte van** uw gebeurtenishubs in de [Azure-portal.](https://portal.azure.com)
2. Selecteer **netwerkoptie** in het linkermenu. Als u de optie **Alle netwerken** selecteert, accepteert de gebeurtenishub verbindingen vanaf elk IP-adres. Deze instelling is gelijk aan een regel die het IP-adresbereik 0.0.0/0/0 accepteert. 

    ![Firewall - Alle netwerken optie geselecteerd](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Als u de toegang tot specifieke netwerken wilt herstellen, selecteert u de optie **Geselecteerde netwerken** boven aan de pagina.
2. Selecteer in het gedeelte **Virtueel netwerk** van de pagina **+Bestaand virtueel netwerk toevoegen***. Selecteer **+ Maak een nieuw virtueel netwerk** als u een nieuw VNet wilt maken. 

    ![bestaand virtueel netwerk toevoegen](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Selecteer het virtuele netwerk in de lijst met virtuele netwerken en kies het **subnet**. U moet het serviceeindpunt inschakelen voordat u het virtuele netwerk aan de lijst toevoegt. Als het serviceeindpunt niet is ingeschakeld, wordt u door de portal gevraagd het in te schakelen.
   
   ![subnet selecteren](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. U ziet het volgende succesvolle bericht nadat het eindpunt van de service voor het subnet is ingeschakeld voor **Microsoft.EventHub**. Selecteer Onder aan de pagina **toevoegen** om het netwerk toe te voegen. 

    ![subnet selecteren en eindpunt inschakelen](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Als u het eindpunt van de service niet inschakelen, u het ontbrekende eindpunt van de virtuele netwerkservice negeren met behulp van de sjabloon Resourcebeheer. Deze functionaliteit is niet beschikbaar in de portal.
6. Selecteer **Opslaan** op de werkbalk om de instellingen op te slaan. Wacht een paar minuten tot de bevestiging wordt weergegeven op de portalmeldingen.

    ![Netwerk opslaan](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>Resource Manager-sjabloon gebruiken

Met de volgende sjabloon Resourcebeheer u een virtuele netwerkregel toevoegen aan een bestaande naamruimte voor gebeurtenishubs.

Sjabloonparameters:

* **naamruimteNaam:** naamruimte voor gebeurtenishubs.
* **vnetRuleName:** Naam voor de regel Virtueel netwerk die moet worden gemaakt.
* **virtualNetworkingSubnetId**: Volledig gekwalificeerd Resource Manager-pad voor het virtuele netwerksubnet; bijvoorbeeld `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` voor het standaardsubnet van een virtueel netwerk.

> [!NOTE]
> Hoewel er geen weigeringsregels mogelijk zijn, is de sjabloon Azure Resource Manager ingesteld op **'Toestaan'** waardoor verbindingen niet worden beperkt.
> Bij het maken van virtual network- of firewalls-regels moeten we de ***'defaultAction'*** wijzigen
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
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Als u de sjabloon wilt implementeren, volgt u de instructies voor [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Volgende stappen

Zie de volgende koppelingen voor meer informatie over virtuele netwerken:

- [Service-eindpunten voor een virtueel Azure-netwerk][vnet-sep]
- [IP-filtering van Azure Event Hubs][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
