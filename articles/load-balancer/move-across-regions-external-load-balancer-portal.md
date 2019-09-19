---
title: Verplaats Azure externe Load Balancer naar een andere Azure-regio met behulp van de Azure Portal
description: Gebruik Azure Resource Manager sjabloon om Azure External Load Balancer van de ene Azure-regio naar de andere te verplaatsen met behulp van de Azure Portal.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: eda0d6e8fe56b985c3b29fa80cee880444d63741
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105297"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-the-azure-portal"></a>Verplaats Azure externe Load Balancer naar een andere regio met behulp van de Azure Portal

Er zijn verschillende scenario's waarin u uw bestaande externe load balancer van de ene naar de andere regio wilt verplaatsen. U kunt bijvoorbeeld een externe load balancer maken met dezelfde configuratie voor testen. Het is ook mogelijk dat u een externe load balancer naar een andere regio wilt verplaatsen als onderdeel van de planning voor nood herstel.

Externe Azure load balancers kunnen niet van de ene regio naar de andere worden verplaatst. U kunt echter een Azure Resource Manager sjabloon gebruiken om de bestaande configuratie en het open bare IP-adres van een externe load balancer te exporteren.  U kunt de resource vervolgens in een andere regio zetten door de load balancer en het open bare IP-adres naar een sjabloon te exporteren, de para meters te wijzigen zodat deze overeenkomen met de doel regio en vervolgens de sjablonen te implementeren in de nieuwe regio.  Zie [resource groepen exporteren naar sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) voor meer informatie over Resource Manager en sjablonen


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de externe Azure-load balancer zich bevindt in de Azure-regio van waaruit u wilt verplaatsen.

- Externe Azure load balancers kunnen niet tussen regio's worden verplaatst.  U moet de nieuwe load balancer koppelen aan resources in de doel regio.

- Als u een externe load balancer configuratie wilt exporteren en een sjabloon wilt implementeren voor het maken van een externe load balancer in een andere regio, hebt u de rol netwerk bijdrager of hoger nodig.
   
- Identificeer de bron netwerk indeling en alle resources die u momenteel gebruikt. Deze indeling bevat, maar is niet beperkt tot load balancers, netwerk beveiligings groepen, open bare Ip's en virtuele netwerken.

- Controleer of u met uw Azure-abonnement externe load balancers kunt maken in de doel regio die wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft ter ondersteuning van het toevoegen van load balancers voor dit proces.  Zie [Azure-abonnement en service limieten, quota's en beperkingen](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
De volgende stappen laten zien hoe u de externe load balancer voorbereidt voor de verplaatsing met behulp van een resource manager-sjabloon en de externe load balancer configuratie verplaatst naar de doel regio met behulp van de Azure Portal.  Als onderdeel van dit proces moet de open bare IP-configuratie van de externe load balancer worden opgenomen en moet ik dit eerst doen voordat de externe load balancer wordt verplaatst.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-the-portal"></a>De open bare IP-sjabloon exporteren en implementeren vanuit de portal

1. Meld u aan bij de [Azure Portal](http://portal.azure.com) > -**resource groepen**.
2. Zoek de resource groep die de open bare bron-IP bevat en klik erop.
3. Selecteer > **instellingen** > **sjabloon exporteren**.
4. Kies **implementeren** op de Blade **sjabloon exporteren** .
5. Klik op **sjabloon** > **bewerken para meters** om het bestand **para meters. json** in de online-editor te openen.
8. Als u de para meter van de open bare IP-naam wilt bewerken, wijzigt u de eigenschap onder **parameter** > **waarde** van de naam van het open bare IP-bron adres in de naam van het open bare IP-adres. Controleer of de naam tussen aanhalings tekens is:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Klik op **Opslaan** in de editor.

9.  Klik op sjabloon**bewerken sjabloon** om het bestand **Template. json** in de online-editor te openen. >  

10. Als u de doel regio wilt bewerken waar het open bare IP-adres wordt verplaatst, wijzigt u de eigenschap **Location** onder **resources**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
11. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van regio-locatie codes.  De code voor een regio is de naam van de regio zonder spaties, **Central VS** = -**Midden**.
    
12. U kunt ook andere para meters in de sjabloon wijzigen als u ervoor kiest en zijn optioneel, afhankelijk van uw vereisten:

    * **SKU** : u kunt de SKU van het open bare IP-adres in de configuratie wijzigen van standaard in Basic of Basic naar Standard door de eigenschap **SKU** > -**naam** te wijzigen in het bestand **sjabloon. json** :

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Zie [een openbaar IP-adres maken, wijzigen of verwijderen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)voor meer informatie over de verschillen tussen open bare ip's van de Basic-en Standard-SKU:

    * **Toewijzings methode voor openbaar IP-adres** en **time-out voor inactiviteit** : u kunt beide opties in de sjabloon wijzigen door de eigenschap **publicIPAllocationMethod** van **dynamisch** naar **statisch** of **statisch** naar dynamisch te veranderen. U kunt de time-out voor inactiviteit wijzigen door de eigenschap **idleTimeoutInMinutes** te wijzigen in de gewenste hoeveelheid.  De standaard waarde is **4**:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
        
        ```

        Zie [een openbaar IP-adres maken, wijzigen of verwijderen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)voor meer informatie over de toewijzings methoden en de time-outwaarden voor inactiviteit.

 
13. Klik op **Opslaan** in de online editor.

14. Klik op **basis** > **abonnement** om het abonnement te kiezen waarin het open bare doel-IP-adres wordt geïmplementeerd.

15. Klik op **basis** > **bronnen groep** om de resource groep te kiezen waarin het open bare doel-IP-adres wordt geïmplementeerd.  U kunt op **Nieuw maken** klikken om een nieuwe resource groep te maken voor het open bare doel-IP-adres.  Zorg ervoor dat de naam niet hetzelfde is als de bron resource groep van de bestaande open bare bron-IP. 

16. De**locatie** van de **basis beginselen** > controleren is ingesteld op de doel locatie waar u het open bare IP-adres wilt implementeren.

17. Controleer onder **instellingen** of de naam overeenkomt met de naam die u hebt ingevoerd in de bovenstaande para meters-editor.

18. Schakel het selectie vakje onder **voor waarden**in.

19. Klik op de knop **aanschaffen** om het doel-open bare IP-adres te implementeren.
20. Als u een ander openbaar IP-adres gebruikt voor een uitgaand NAT-verkeer voor het load balancer dat wordt verplaatst, herhaalt u de bovenstaande stappen voor het exporteren en implementeren van het tweede uitgaande open bare IP-adres naar de doel regio.

### <a name="export-the-external-load-balancer-template-and-deploy-from-the-azure-portal"></a>De externe load balancer sjabloon exporteren en implementeren vanuit de Azure Portal

1. Meld u aan bij de [Azure Portal](http://portal.azure.com) > -**resource groepen**.
2. Zoek de resource groep die de bron externe load balancer bevat en klik erop.
3. Selecteer > **instellingen** > **sjabloon exporteren**.
4. Kies **implementeren** op de Blade **sjabloon exporteren** .
5. Klik op **sjabloon** > **bewerken para meters** om het bestand **para meters. json** in de online-editor te openen.

5. Als u de para meter van de naam van de externe load balancer wilt bewerken, wijzigt u de **waarde** van de eigenschap van de bron externe Load Balancer naam in de naam van de externe doel Load Balancer en controleert u of de naam tussen aanhalings tekens is:

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Als u de waarde van de open bare doel-IP die hierboven is verplaatst, wilt bewerken, moet u eerst de resource-ID ophalen en deze vervolgens kopiëren en plakken in het bestand **para meters. json** . De ID ophalen:
    
    1. Meld u aan bij de [Azure Portal](http://portal.azure.com) > **resource groepen** op een ander browser tabblad of-venster.
    2. Zoek de doel resource groep die het verplaatste open bare IP bevat uit de bovenstaande stappen en klik erop.
    3. Selecteer**Eigenschappen**van > **instellingen** > .
    4. Markeer de **resource-id** op de Blade aan de rechter kant en kopieer deze naar het klem bord.  U kunt ook op de knop **kopiëren naar klem bord** rechts van het **resource-id-** pad klikken.
    5. Plak de resource-ID in de eigenschap **Value** in de editor **para meters bewerken** open in het andere browser venster of tabblad:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Klik op **Opslaan** in de online editor.
   

7.  Als u uitgaande NAT en regels voor uitgaande verbindingen voor de load balancer hebt geconfigureerd, wordt in dit bestand een derde vermelding weer gegeven voor de externe ID voor het uitgaande open bare IP-adres.  Herhaal de bovenstaande stappen in de **doel regio** om de id voor het uitgaande open bare IP-adres te verkrijgen en plak die vermelding in het bestand **para meters. json** :

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",
                
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",
                
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                
            }
        },
    ```

8.  Klik op sjabloon**bewerken sjabloon** om het bestand **Template. json** in de online-editor te openen. > 
9.  Als u de doel regio wilt bewerken waar de configuratie van de externe load balancer wordt verplaatst, wijzigt u de eigenschap **Location** onder **resources** in het bestand **sjabloon. json** :

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van regio-locatie codes.  De code voor een regio is de naam van de regio zonder spaties, **Central VS** = -**Midden**.

11. U kunt ook andere para meters in de sjabloon wijzigen als u ervoor kiest en zijn optioneel, afhankelijk van uw vereisten:
    
    * **SKU** : u kunt de SKU van de externe Load Balancer in de configuratie wijzigen van standaard in Basic of Basic naar Standard door de eigenschap **SKU** > -**naam** te wijzigen in het bestand **sjabloon. json** :

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Zie [overzicht van Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) voor meer informatie over de verschillen tussen de Basic-en Standard SKU load balancers

    * Taakverdelings **regels** : u kunt regels voor taak verdeling toevoegen aan of verwijderen uit de configuratie door vermeldingen toe te voegen aan of te verwijderen uit de sectie **loadBalancingRules** van het bestand **sjabloon. json** :

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Zie [Wat is Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) voor meer informatie over taakverdelings regels.

    * **Tests** : u kunt een test toevoegen aan of verwijderen uit de Load Balancer in de configuratie door vermeldingen toe te voegen aan of te verwijderen uit het gedeelte **tests** van het bestand **sjabloon. json** :

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Zie [Load Balancer Health probe](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) (Engelstalig) voor meer informatie over Azure Load Balancer status tests

    * **Binnenkomende NAT-regels** : u kunt binnenkomende NAT-regels voor de Load Balancer toevoegen of verwijderen door vermeldingen toe te voegen aan de sectie **inboundNatRules** van het bestand **sjabloon. json** :

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Als u het toevoegen of verwijderen van een binnenkomende NAT-regel wilt volt ooien, moet de regel aanwezig zijn of worden verwijderd als een **type** -eigenschap aan het einde van het bestand **sjabloon. json** :

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Zie [Wat is Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) voor meer informatie over binnenkomende NAT-regels.

    * **Regels voor uitgaande verbindingen** : u kunt uitgaande regels toevoegen aan of verwijderen uit de configuratie door de eigenschap **outboundRules** in het bestand **Template. json** te bewerken:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Zie [Load Balancer regels voor uitgaande verbindingen](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview) voor meer informatie over uitgaande regels

12. Klik op **Opslaan** in de online editor.
    
13. Klik op **basis** > **abonnement** om het abonnement te kiezen waarin de externe Load Balancer worden geïmplementeerd.

15. Klik op **basis** > **bronnen groep** om de resource groep te kiezen waarin de doel-Load Balancer worden geïmplementeerd.  U kunt op **Nieuw maken** klikken om een nieuwe resource groep te maken voor de externe doel Load Balancer of de bestaande resource groep kiezen die hierboven is gemaakt voor het open bare IP-adres.  Zorg ervoor dat de naam niet hetzelfde is als de bron resource groep van de bestaande externe bron load balancer. 

16. De**locatie** van de **basis beginselen** > controleren is ingesteld op de doel locatie waar u de externe Load Balancer wilt implementeren.

17. Controleer onder **instellingen** of de naam overeenkomt met de naam die u hebt ingevoerd in de bovenstaande para meters-editor.  Controleer of de resource-Id's zijn ingevuld voor open bare IP-adressen in de configuratie.

18. Schakel het selectie vakje onder **voor waarden**in.

19. Klik op de knop **aanschaffen** om het doel-open bare IP-adres te implementeren.

## <a name="discard"></a>Verwijderen 

Als u het doel publiek en de externe load balancer wilt verwijderen, verwijdert u de resource groep die het open bare IP-adres en het externe load balancer bevat.  Hiervoor selecteert u de resource groep in het dash board in de portal en selecteert u **verwijderen** boven aan de pagina overzicht.

## <a name="clean-up"></a>Opruimen

Als u de wijzigingen wilt door voeren en de open bare IP-en externe load balancer wilt verplaatsen, verwijdert u de open bare IP-bron en de externe load balancer of resource groep. Als u dit wilt doen, selecteert u het open bare IP-adres en het externe load balancer of de resource groep in het dash board in de portal en selecteert u **verwijderen** boven aan elke pagina.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een externe Azure-load balancer verplaatst van de ene regio naar de andere en de bron resources opgeschoond.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
