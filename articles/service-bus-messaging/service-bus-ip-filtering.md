---
title: IP-firewall regels voor Azure Service Bus configureren
description: Hoe u firewall regels kunt gebruiken om verbindingen van bepaalde IP-adressen toe te staan Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 2ba6b1902213af8d968b220a387e419e56e8aa67
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91300952"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>Toegang tot Azure Service Bus naam ruimte van specifieke IP-adressen of bereiken toestaan
Service Bus naam ruimten zijn standaard toegankelijk vanuit Internet zolang de aanvraag een geldige verificatie en autorisatie heeft. Met IP-firewall kunt u dit nog verder beperken tot een aantal IPv4-adressen of IPv4-adresbereiken in CIDR-notatie [(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Deze functie is handig in scenario's waarin Azure Service Bus alleen toegankelijk moet zijn vanaf bepaalde bekende sites. Met firewall regels kunt u regels configureren voor het accepteren van verkeer dat afkomstig is van specifieke IPv4-adressen. Als u bijvoorbeeld Service Bus met [Azure Express route][express-route]gebruikt, kunt u een **firewall regel** maken om alleen verkeer toe te staan van uw on-premises IP-adressen of adressen van een NAT-netwerk gateway. 

> [!IMPORTANT]
> Firewalls en virtuele netwerken worden alleen ondersteund in de **Premium** -laag van service bus. Als de upgrade naar de **premier** -laag geen optie is, raden wij aan dat u het Shared Access Signature SAS-token veilig en met alleen geautoriseerde gebruikers kunt delen. Zie [verificatie en autorisatie](service-bus-authentication-and-authorization.md#shared-access-signature)voor meer informatie over SAS-verificatie.

## <a name="ip-firewall-rules"></a>IP-firewallregels
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
> - Azure Monitor (diagnostische instelling)

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie wordt beschreven hoe u de Azure Portal gebruikt om IP-firewall regels voor een Service Bus naam ruimte te maken. 

1. Navigeer naar uw **Service Bus-naam ruimte** in de [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu links de optie **netwerk** opties onder **instellingen**.  

    > [!NOTE]
    > U ziet het tabblad **netwerken** alleen voor **Premium** -naam ruimten.  
    
    Standaard is de optie **geselecteerde netwerken** geselecteerd. Als u niet ten minste één IP-firewall regel of een virtueel netwerk op deze pagina toevoegt, is de naam ruimte toegankelijk via het open bare Internet (met behulp van de toegangs sleutel).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Pagina netwerk-standaard" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Als u de optie **alle netwerken** selecteert, accepteert uw service bus-naam ruimte verbindingen van elk IP-adres. Deze standaard instelling komt overeen met een regel die het IP-adres bereik 0.0.0.0/0 accepteert. 

    ![Scherm afbeelding van de pagina met het Azure Portal-netwerk. De optie om toegang vanaf alle netwerken toe te staan, is geselecteerd op het tabblad firewalls en virtuele netwerken.](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Als u alleen toegang vanaf een opgegeven IP-adres wilt toestaan, selecteert u de optie **geselecteerde netwerken** als deze nog niet is geselecteerd. Voer de volgende stappen uit in de sectie **firewall** :
    1. Selecteer **de optie uw IP-adres voor client toevoegen** om uw huidige client-IP de toegang tot de naam ruimte te geven. 
    2. Voer bij **adres bereik**een specifiek IPv4-adres of een bereik van IPv4-adres in CIDR-notatie in. 
    3. Geef op of u wilt **toestaan dat vertrouwde micro soft-services deze firewall overs Laan**. 

        > [!WARNING]
        > Als u de optie **geselecteerde netwerken** kiest en geen IP-adres of adres bereik opgeeft, is verkeer van alle netwerken toegestaan door de service. 

        ![Scherm afbeelding van de pagina met het Azure Portal-netwerk. De optie om toegang via geselecteerde netwerken toe te staan is geselecteerd en het gedeelte firewall is gemarkeerd.](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Selecteer **Opslaan** op de werk balk om de instellingen op te slaan. Wacht een paar minuten totdat de bevestiging op de portal meldingen wordt weer gegeven.

    > [!NOTE]
    > Zie toegang tot specifieke [netwerken toestaan](service-bus-service-endpoints.md)om de toegang tot specifieke virtuele netwerken te beperken.

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
> in op
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
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services