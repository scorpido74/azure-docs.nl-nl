---
title: Service-eind punten voor virtuele netwerken configureren voor Azure Service Bus
description: Dit artikel bevat informatie over het toevoegen van een service-eind punt van micro soft. ServiceBus aan een virtueel netwerk.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 2b3e7d23dcfd3f932aefa3809ebd13b9cfee0c69
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85340991"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>Service-eind punten voor virtuele netwerken configureren voor Azure Service Bus

Dankzij de integratie van Service Bus met de [service-eind punten van Virtual Network (VNet)][vnet-sep] is beveiligde toegang mogelijk tot berichten mogelijkheden van werk belastingen, zoals virtuele machines die zijn gebonden aan virtuele netwerken, waarbij het netwerkpad van het netwerk verkeer aan beide uiteinden wordt beveiligd.

Zodra het is geconfigureerd om te worden gebonden aan ten minste één subnet-service-eind punt van een virtueel netwerk, accepteert de respectieve Service Bus naam ruimte geen verkeer meer vanaf een wille keurige locatie, maar geautoriseerde virtuele netwerken. Vanuit het perspectief van het virtuele netwerk moet u een Service Bus naam ruimte binden aan een service-eind punt een geïsoleerde netwerk tunnel van het subnet van het virtuele netwerk naar de berichten service configureren.

Het resultaat is een privé-en geïsoleerde relatie tussen de werk belastingen die zijn gebonden aan het subnet en de betreffende Service Bus naam ruimte, ondanks het waarneem bare netwerk adres van het berichten service-eind punt in een openbaar IP-bereik.

>[!WARNING]
> Het implementeren van de integratie van virtuele netwerken kan voorkomen dat andere Azure-services interactie hebben met Service Bus.
>
> Vertrouwde micro soft-services worden niet ondersteund wanneer virtuele netwerken zijn geïmplementeerd.
>
> Algemene scenario's voor Azure die niet met virtuele netwerken werken (Houd er rekening mee dat de lijst **niet** volledig is)-
> - Integratie met Azure Event Grid
> - Azure-IoT Hub routes
> - Azure IoT-Device Explorer
>
> De onderstaande micro soft-services moeten zich in een virtueel netwerk bevinden
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> Virtuele netwerken worden alleen ondersteund in de [Premium-laag](service-bus-premium-messaging.md) service bus naam ruimten.
> 
> Wanneer u VNet-service-eind punten met Service Bus gebruikt, moet u deze eind punten niet inschakelen in toepassingen die gebruikmaken van de standaard-en Premium-laag Service Bus naam ruimten. Omdat de standaard tier geen VNets ondersteunt. Het eind punt is alleen toegestaan voor de naam ruimte van de Premium-laag.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Geavanceerde beveiligings scenario's ingeschakeld door VNet-integratie 

Oplossingen die een strakke en compartmentalized beveiliging vereisen, en waarbij virtuele netwerk-subnetten de segmentatie tussen de compartmentalized-services bieden, is over het algemeen nog steeds communicatie paden nodig tussen services die zich in deze compartimenten bevinden.

Elke onmiddellijke IP-route tussen de compartimenten, waaronder die van HTTPS via TCP/IP, vormt het risico van misbruik van beveiligings problemen vanuit de netwerklaag. Berichten services bieden volledig geïsoleerde communicatie paden, waar berichten zelfs naar de schijf worden geschreven wanneer ze tussen partijen worden overgezet. Werk belastingen in twee verschillende virtuele netwerken die beide zijn gebonden aan hetzelfde Service Bus-exemplaar kunnen efficiënt en betrouwbaar communiceren via berichten, terwijl de respectieve integriteit van de isolatie grens van het netwerk behouden blijft.
 
Dit betekent dat uw beveiligings gevoelige cloud oplossingen niet alleen toegang krijgen tot de toonaangevende betrouw bare en schaal bare asynchrone berichten mogelijkheden van Azure, maar ze kunnen nu gebruikmaken van berichten om communicatie paden te maken tussen veilige oplossingen compartimenten die inherent veiliger zijn dan wat kan worden behaald met een peer-to-peer communicatie modus, inclusief HTTPS en andere met TLS beveiligde socket protocollen.

## <a name="binding-service-bus-to-virtual-networks"></a>Service Bus binden aan virtuele netwerken

*Regels voor virtuele netwerken* zijn de firewall beveiligings functie waarmee wordt bepaald of de Azure service bus-server verbindingen van een bepaald subnet van een virtueel netwerk accepteert.

Het binden van een Service Bus naam ruimte aan een virtueel netwerk is een proces dat uit twee stappen bestaat. U moet eerst een **Virtual Network Service-eind punt** maken op een Virtual Network subnet en inschakelen voor **micro soft. ServiceBus** , zoals wordt uitgelegd in het [overzicht van service-eind punten][vnet-sep]. Wanneer u het service-eind punt hebt toegevoegd, bindt u de naam ruimte van de Service Bus met een regel voor het **virtuele netwerk**.

De regel van het virtuele netwerk is een koppeling van de Service Bus naam ruimte met een subnet van een virtueel netwerk. Terwijl de regel bestaat, krijgen alle werk belastingen die aan het subnet zijn gebonden toegang tot de Service Bus naam ruimte. Service Bus zichzelf nooit uitgaande verbindingen tot stand brengt, geen toegang nodig heeft en daarom nooit toegang tot uw subnet verleend door deze regel in te scha kelen.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie wordt beschreven hoe u Azure Portal kunt gebruiken om een service-eind punt voor een virtueel netwerk toe te voegen. Als u de toegang wilt beperken, moet u het eind punt van de virtuele netwerk service voor deze Event Hubs naam ruimte integreren.

1. Navigeer naar uw **Service Bus-naam ruimte** in de [Azure Portal](https://portal.azure.com).
2. Selecteer in het linkermenu **netwerk** optie. Standaard is de optie **alle netwerken** geselecteerd. Uw naam ruimte accepteert verbindingen van elk IP-adres. Deze standaard instelling komt overeen met een regel die het IP-adres bereik 0.0.0.0/0 accepteert. 

    ![Optie Firewall: alle netwerken geselecteerd](./media/service-endpoints/firewall-all-networks-selected.png)
1. Selecteer de optie **geselecteerde netwerken** boven aan de pagina.
2. Selecteer in het gedeelte **Virtual Network** van de pagina **+ bestaand virtueel netwerk toevoegen**. 

    ![bestaand virtueel netwerk toevoegen](./media/service-endpoints/add-vnet-menu.png)
3. Selecteer het virtuele netwerk in de lijst met virtuele netwerken en kies vervolgens het **subnet**. U moet het service-eind punt inschakelen voordat u het virtuele netwerk aan de lijst toevoegt. Als het service-eind punt niet is ingeschakeld, wordt u door de portal gevraagd om dit in te scha kelen.
   
   ![subnet selecteren](./media/service-endpoints/select-subnet.png)

4. U ziet het volgende geslaagde bericht nadat het service-eind punt voor het subnet is ingeschakeld voor **micro soft. ServiceBus**. Selecteer **toevoegen** onder aan de pagina om het netwerk toe te voegen. 

    ![subnet selecteren en eindpunt inschakelen](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Als u het service-eind punt niet kunt inschakelen, kunt u het ontbrekende service-eind punt van het virtuele netwerk negeren met behulp van de Resource Manager-sjabloon. Deze functionaliteit is niet beschikbaar in de portal.
6. Selecteer **Opslaan** op de werk balk om de instellingen op te slaan. Wacht een paar minuten totdat de bevestiging wordt weer gegeven in de portal meldingen. De knop **Opslaan** moet worden uitgeschakeld. 

    ![Netwerk opslaan](./media/service-endpoints/save-vnet.png)

## <a name="use-resource-manager-template"></a>Resource Manager-sjabloon gebruiken
Met de volgende Resource Manager-sjabloon kan een regel voor een virtueel netwerk worden toegevoegd aan een bestaande Service Bus naam ruimte.

Sjabloon parameters:

* **naam ruimte**: Service Bus naam ruimte.
* **virtualNetworkingSubnetId**: volledig gekwalificeerd pad van Resource Manager voor het subnet van het virtuele netwerk; bijvoorbeeld `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` voor het standaard subnet van een virtueel netwerk.

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
- [IP-filtering Azure Service Bus][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
