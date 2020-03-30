---
title: Firewallregels azure-gebeurtenishubs | Microsoft Documenten
description: Gebruik Firewallregels om verbindingen toe te staan van specifieke IP-adressen naar Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: fb11d1bdcf8145d4e78285833789b41c92b0ce4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064881"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>IP-firewallregels configureren voor een naamruimte van Azure Event Hubs
Standaard zijn naamruimten van Event Hubs toegankelijk vanaf internet, zolang de aanvraag wordt geleverd met geldige verificatie en autorisatie. Met IP-firewall u deze verder beperken tot alleen een set IPv4-adressen of IPv4-adresbereiken in [CIDR-notatie (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Deze functie is handig in scenario's waarin Azure Event Hubs alleen toegankelijk moeten zijn vanaf bepaalde bekende sites. Met firewallregels u regels configureren om verkeer te accepteren dat afkomstig is van specifieke IPv4-adressen. Als u bijvoorbeeld Gebeurtenishubs met [Azure Express-route][express-route]gebruikt, u een **firewallregel** maken om verkeer toe te staan vanaf alleen uw on-premises infrastructuur-IP-adressen. 

## <a name="ip-firewall-rules"></a>IP-firewallregels
De IP-firewallregels worden toegepast op het naamruimteniveau van Gebeurtenishubs. Daarom zijn de regels van toepassing op alle verbindingen van clients die een ondersteund protocol gebruiken. Elke verbindingspoging vanaf een IP-adres dat niet overeenkomt met een toegestane IP-regel op de naamruimte van gebeurtenishubs, wordt afgewezen als ongeautoriseerd. In het antwoord wordt geen IP-regel vermeld. IP-filterregels worden op volgorde toegepast en de eerste regel die overeenkomt met het IP-adres bepaalt de actie Accepteren of weigeren.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie ziet u hoe u de Azure-portal gebruiken om IP-firewallregels te maken voor een naamruimte voor gebeurtenishubs. 

1. Navigeer naar de **naamruimte van** uw gebeurtenishubs in de [Azure-portal.](https://portal.azure.com)
2. Selecteer **netwerkoptie** in het linkermenu. Als u de optie **Alle netwerken** selecteert, accepteert de gebeurtenishub verbindingen vanaf elk IP-adres. Deze instelling is gelijk aan een regel die het IP-adresbereik 0.0.0/0/0 accepteert. 

    ![Firewall - Alle netwerken optie geselecteerd](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Als u de toegang tot specifieke netwerken en IP-adressen wilt beperken, selecteert u de optie **Geselecteerde netwerken.** Voer in de sectie **Firewall** de volgende stappen uit:
    1. Selecteer De optie **IP-adres van uw client toevoegen** om uw huidige client-IP toegang te geven tot de naamruimte. 
    2. Voer **voor adresbereik**een specifiek IPv4-adres of een bereik van IPv4-adres in CIDR-notatie in. 
    3. Geef op of u **vertrouwde Microsoft-services wilt toestaan om deze firewall te omzeilen.** 

        ![Firewall - Alle netwerken optie geselecteerd](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Selecteer **Opslaan** op de werkbalk om de instellingen op te slaan. Wacht een paar minuten tot de bevestiging wordt weergegeven op de portalmeldingen.


## <a name="use-resource-manager-template"></a>Resource Manager-sjabloon gebruiken

> [!IMPORTANT]
> Firewallregels worden ondersteund in **standaard-** en **speciale** lagen van gebeurtenishubs. Deze worden niet ondersteund in de Basic-laag.

Met de volgende sjabloon Resourcemanager u een IP-filterregel toevoegen aan een bestaande naamruimte voor gebeurtenishubs.

Sjabloonparameters:

- **ipMask** is één IPv4-adres of een blok IP-adressen in CIDR-notatie. In CIDR-notatie 70.37.104.0/24 vertegenwoordigt bijvoorbeeld de 256 IPv4-adressen van 70.37.104.0 tot 70.37.104.255, waarbij 24 het aantal significante voorvoegselbits voor het bereik aangeven.

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

Zie de volgende koppeling voor het beperken van de toegang tot gebeurtenishubs tot virtuele Azure-netwerken:

- [Eindpunten van virtuele netwerkservice voor gebeurtenishubs][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
