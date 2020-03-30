---
title: Eindpunten voor virtuele netwerkservice configureren voor Azure Service Bus
description: In dit artikel vindt u informatie over het toevoegen van een eindpunt van een Microsoft.ServiceBus-service aan een virtueel netwerk.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 9dbf65522d5c85e1054ed3f1f6ca9f86180e7f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454978"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>Eindpunten voor virtuele netwerkservice configureren voor Azure Service Bus

De integratie van Service Bus met [VNet-serviceeindpunten (Virtual Network)][vnet-sep] maakt veilige toegang tot berichtenmogelijkheden mogelijk van workloads zoals virtuele machines die gebonden zijn aan virtuele netwerken, waarbij het netwerkverkeer pad aan beide uiteinden wordt beveiligd.

Zodra de naamruimte van de servicebus is geconfigureerd om te zijn gekoppeld aan ten minste één eindpunt van de virtuele netwerksubnetservice, accepteert de desbetreffende servicebusnaamruimte geen verkeer meer van overal, maar van geautoriseerd virtueel netwerk(en). Vanuit het perspectief van het virtuele netwerk configureert het binden van een servicebusnaamruimte aan een serviceeindpunt een geïsoleerde netwerktunnel van het virtuele netwerksubnet naar de berichtenservice.

Het resultaat is een privé- en geïsoleerde relatie tussen de workloads die aan het subnet zijn gebonden en de respectievelijke naamruimte van servicebus, ondanks het waarneembare netwerkadres van het eindpunt van de berichtendienst dat zich in een openbaar IP-bereik bevindt.

> [!IMPORTANT]
> Virtuele netwerken worden alleen ondersteund in naamruimten [van Premium-servicebus.](service-bus-premium-messaging.md)
> 
> Wanneer u VNet-serviceeindpunten gebruikt met Service Bus, moet u deze eindpunten niet inschakelen in toepassingen die standaard- en Premium-servicebusnaamruimten combineren. Omdat standaardlaag geen VNets ondersteunt. Het eindpunt is beperkt tot alleen naamruimten voor premiumlagen.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Geavanceerde beveiligingsscenario's mogelijk gemaakt door VNet-integratie 

Oplossingen die strakke en gecompartimenteerde beveiliging vereisen en waar virtuele netwerksubnetten de segmentatie tussen de gecompartimenteerde services bieden, hebben over het algemeen nog steeds communicatiepaden nodig tussen diensten die in die compartimenten aanwezig zijn.

Elke directe IP-route tussen de compartimenten, inclusief die met HTTPS via TCP/IP, brengt het risico met zich mee dat kwetsbaarheden vanaf de netwerklaag worden gebruikt. Messaging-diensten bieden volledig geïsoleerde communicatiepaden, waar berichten zelfs worden geschreven op schijf als ze de overgang tussen partijen. Workloads in twee afzonderlijke virtuele netwerken die beide gebonden zijn aan dezelfde Service Bus-instantie, kunnen efficiënt en betrouwbaar communiceren via berichten, terwijl de respectieve integriteit van de netwerkisolatiegrens behouden blijft.
 
Dat betekent dat uw beveiligingsgevoelige cloudoplossingen niet alleen toegang krijgen tot azure toonaangevende betrouwbare en schaalbare asynchrone messaging-mogelijkheden, maar ze kunnen nu messaging gebruiken om communicatiepaden te maken tussen beveiligde oplossingscompartimenten die zijn inherent veiliger dan wat haalbaar is met een peer-to-peer communicatiemodus, inclusief HTTPS en andere TLS-beveiligde socketprotocollen.

## <a name="binding-service-bus-to-virtual-networks"></a>Servicebus binden aan virtuele netwerken

*Virtuele netwerkregels* zijn de beveiligingsfunctie voor firewalls die bepaalt of uw Azure Service Bus-server verbindingen van een bepaald virtueel netwerksubnet accepteert.

Het binden van een naamruimte van een servicebus aan een virtueel netwerk is een proces in twee stappen. U moet eerst een eindpunt van de **virtual network-service** maken op een subnet van een virtueel netwerk en het inschakelen voor **Microsoft.ServiceBus,** zoals uitgelegd in het [eindpuntoverzicht van][vnet-sep]de service. Zodra u het serviceeindpunt hebt toegevoegd, bindt u de naamruimte servicebus eraan met een **virtuele netwerkregel**.

De virtuele netwerkregel is een koppeling van de naamruimte servicebus met een virtueel netwerksubnet. Hoewel de regel bestaat, krijgen alle workloads die aan het subnet zijn gebonden, toegang tot de naamruimte servicebus. Service Bus zelf maakt nooit uitgaande verbindingen, hoeft geen toegang te krijgen en krijgt daarom nooit toegang tot uw subnet door deze regel in te schakelen.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie ziet u hoe u Azure-portal gebruiken om een eindpunt voor virtuele netwerkservice toe te voegen. Om de toegang te beperken, moet u het eindpunt van de virtuele netwerkservice voor deze naamruimte voor gebeurtenishubs integreren.

1. Navigeer naar de **naamruimte van** uw servicebus in de [Azure-portal.](https://portal.azure.com)
2. Selecteer **netwerkoptie** in het linkermenu. Standaard is de optie **Alle netwerken** geselecteerd. Uw naamruimte accepteert verbindingen vanaf elk IP-adres. Deze standaardinstelling is gelijk aan een regel die het IP-adresbereik 0.0.0/0/0 accepteert. 

    ![Firewall - Alle netwerken optie geselecteerd](./media/service-endpoints/firewall-all-networks-selected.png)
1. Selecteer de optie **Geselecteerde netwerken** boven aan de pagina.
2. Selecteer **in** het gedeelte Virtueel netwerk van de pagina **+Bestaand virtueel netwerk toevoegen**. 

    ![bestaand virtueel netwerk toevoegen](./media/service-endpoints/add-vnet-menu.png)
3. Selecteer het virtuele netwerk in de lijst met virtuele netwerken en kies het **subnet**. U moet het serviceeindpunt inschakelen voordat u het virtuele netwerk aan de lijst toevoegt. Als het serviceeindpunt niet is ingeschakeld, wordt u door de portal gevraagd het in te schakelen.
   
   ![subnet selecteren](./media/service-endpoints/select-subnet.png)

4. U ziet het volgende succesvolle bericht nadat het eindpunt van de service voor het subnet is ingeschakeld voor **Microsoft.ServiceBus**. Selecteer Onder aan de pagina **toevoegen** om het netwerk toe te voegen. 

    ![subnet selecteren en eindpunt inschakelen](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Als u het eindpunt van de service niet inschakelen, u het ontbrekende eindpunt van de virtuele netwerkservice negeren met behulp van de sjabloon Resourcebeheer. Deze functionaliteit is niet beschikbaar in de portal.
6. Selecteer **Opslaan** op de werkbalk om de instellingen op te slaan. Wacht een paar minuten tot de bevestiging wordt weergegeven in de portalmeldingen. De knop **Opslaan** moet worden uitgeschakeld. 

    ![Netwerk opslaan](./media/service-endpoints/save-vnet.png)

## <a name="use-resource-manager-template"></a>Resource Manager-sjabloon gebruiken
Met de volgende sjabloon Resourcebeheer u een virtuele netwerkregel toevoegen aan een bestaande naamruimte van servicebus.

Sjabloonparameters:

* **namespaceName:** Naamruimte servicebus.
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
- [IP-filtering van Azure Service Bus][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
