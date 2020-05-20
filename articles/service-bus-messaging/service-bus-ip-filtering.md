---
title: IP-firewall regels voor Azure Service Bus configureren
description: Hoe u firewall regels kunt gebruiken om verbindingen van bepaalde IP-adressen toe te staan Azure Service Bus.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 05/14/2020
ms.author: aschhab
ms.openlocfilehash: fdd3540248c5210e2f6fc47f439641c007a793d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647817"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>IP-firewall regels voor Azure Service Bus configureren
Service Bus naam ruimten zijn standaard toegankelijk vanuit Internet zolang de aanvraag een geldige verificatie en autorisatie heeft. Met IP-firewall kunt u dit nog verder beperken tot een aantal IPv4-adressen of IPv4-adresbereiken in CIDR-notatie [(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Deze functie is handig in scenario's waarin Azure Service Bus alleen toegankelijk moet zijn vanaf bepaalde bekende sites. Met firewall regels kunt u regels configureren voor het accepteren van verkeer dat afkomstig is van specifieke IPv4-adressen. Als u bijvoorbeeld Service Bus met [Azure Express route][express-route]gebruikt, kunt u een **firewall regel** maken om alleen verkeer toe te staan van uw on-premises IP-adressen of adressen van een NAT-netwerk gateway. 

> [!IMPORTANT]
> Firewalls en virtuele netwerken worden alleen ondersteund in de **Premium** -laag van service bus. Als de upgrade naar de **premier** -laag geen optie is, raden wij aan dat u het Shared Access Signature SAS-token veilig en met alleen geautoriseerde gebruikers kunt delen. Zie [verificatie en autorisatie](service-bus-authentication-and-authorization.md#shared-access-signature)voor meer informatie over SAS-verificatie.

## <a name="ip-firewall-rules"></a>IP-firewall regels
De IP-firewall regels worden toegepast op het niveau van de Service Bus naam ruimte. Daarom gelden de regels voor alle verbindingen van clients die gebruikmaken van elk ondersteund protocol. Een verbindings poging van een IP-adres dat niet overeenkomt met een toegestane IP-regel op de Service Bus naam ruimte, wordt geweigerd als niet-geautoriseerd. De IP-regel wordt niet vermeld in het antwoord. IP-filter regels worden in volg orde toegepast en de eerste regel die overeenkomt met het IP-adres, bepaalt de accepteren of afwijzen.

>[!WARNING]
> Het implementeren van firewall regels kan verhinderen dat andere Azure-Services communiceren met Service Bus.
>
> Vertrouwde micro soft-services worden niet ondersteund wanneer IP-filtering (firewall regels) worden geïmplementeerd en binnenkort beschikbaar wordt gesteld.
>
> Algemene scenario's voor Azure die niet werken met IP-filtering (Let op: de lijst is **niet** volledig)-
> - Integratie met Azure Event Grid
> - Azure-IoT Hub routes
> - Azure IoT-Device Explorer
>
> De volgende micro soft-services moeten zich in een virtueel netwerk bevinden
> - Azure App Service
> - Azure Functions

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie wordt beschreven hoe u de Azure Portal gebruikt om IP-firewall regels voor een Service Bus naam ruimte te maken. 

1. Navigeer naar uw **Service Bus-naam ruimte** in de [Azure Portal](https://portal.azure.com).
2. Selecteer in het linkermenu **netwerk** optie. Standaard is de optie **alle netwerken** geselecteerd. De naam ruimte van uw Service Bus accepteert verbindingen van elk IP-adres. Deze standaard instelling komt overeen met een regel die het IP-adres bereik 0.0.0.0/0 accepteert. 

    ![Optie Firewall: alle netwerken geselecteerd](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Selecteer de optie **geselecteerde netwerken** boven aan de pagina. Voer de volgende stappen uit in de sectie **firewall** :
    1. Selecteer **de optie uw IP-adres voor client toevoegen** om uw huidige client-IP de toegang tot de naam ruimte te geven. 
    2. Voer bij **adres bereik**een specifiek IPv4-adres of een bereik van IPv4-adres in CIDR-notatie in. 
    3. Geef op of u wilt **toestaan dat vertrouwde micro soft-services deze firewall overs Laan**. 

        > [!WARNING]
        > Als u de optie **geselecteerde netwerken** kiest en geen IP-adres of adres bereik opgeeft, is verkeer van alle netwerken toegestaan door de service. 

        ![Optie Firewall: alle netwerken geselecteerd](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Selecteer **Opslaan** op de werk balk om de instellingen op te slaan. Wacht een paar minuten totdat de bevestiging op de portal meldingen wordt weer gegeven.

## <a name="use-resource-manager-template"></a>Resource Manager-sjabloon gebruiken
Deze sectie bevat een voor beeld Azure Resource Manager sjabloon waarmee een virtueel netwerk en een firewall regel worden gemaakt.


Met de volgende Resource Manager-sjabloon kan een regel voor een virtueel netwerk worden toegevoegd aan een bestaande Service Bus naam ruimte.

Sjabloon parameters:

- **ipMask** is een enkel IPv4-adres of een blok met IP-adressen in CIDR-notatie. Bijvoorbeeld, in CIDR-notatie 70.37.104.0/24 staat voor de IPv4-adressen 256 van 70.37.104.0 naar 70.37.104.255, met 24 waarmee het aantal belang rijke voorvoegsel bits voor het bereik wordt aangegeven.

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

Zie de volgende koppeling voor meer informatie over het beperken van toegang tot Service Bus voor virtuele netwerken van Azure:

- [Service-eind punten Virtual Network voor Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
