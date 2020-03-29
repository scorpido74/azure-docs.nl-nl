---
title: Een azure externe load balancer verplaatsen naar een andere Azure-regio met behulp van de Azure-portal
description: Gebruik een Azure Resource Manager-sjabloon om een externe load balancer van het ene Azure-gebied naar het andere te verplaatsen met behulp van de Azure-portal.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 5cd5ce2635ce05c4d5962f12ddc3945342897ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638490"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Een externe load balancer verplaatsen naar een andere regio met behulp van de Azure-portal

Er zijn verschillende scenario's waarin u een externe load balancer van de ene regio naar de andere wilt verplaatsen. U bijvoorbeeld een andere externe load balancer maken met dezelfde configuratie voor het testen. U ook een externe load balancer naar een andere regio verplaatsen als onderdeel van de planning voor noodherstel.

In letterlijke zin u een externe laadbakvan Azure niet van de ene regio naar de andere verplaatsen. U echter een Azure Resource Manager-sjabloon gebruiken om de bestaande configuratie en het openbare IP-adres van een externe load balancer te exporteren. U de resource vervolgens in een andere regio faseren door de load balancer en het openbare IP-adres naar een sjabloon te exporteren, de parameters te wijzigen die overeenkomen met het doelgebied en vervolgens de sjabloon naar de nieuwe regio te implementeren. Zie [Brongroepen exporteren naar sjablonen voor](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)meer informatie over Resourcemanager en sjablonen.


## <a name="prerequisites"></a>Vereisten

- Controleer of de externe load balancer van Azure zich in het Azure-gebied bevindt van waaruit u wilt verplaatsen.

- Azure externe load balancers kunnen niet worden verplaatst tussen regio's. U moet de nieuwe load balancer koppelen aan resources in het doelgebied.

- Als u een externe configuratie van de load balancer wilt exporteren en een sjabloon wilt implementeren om een externe load balancer in een andere regio te maken, moet u de rol Netwerkinzender of hoger toegewezen krijgen.

- Identificeer de indeling van de bronnetwerkindeling en alle bronnen die u momenteel gebruikt. Deze lay-out omvat, maar is niet beperkt tot load balancers, netwerkbeveiligingsgroepen, openbare IP's en virtuele netwerken.

- Controleer of u met uw Azure-abonnement externe load balancers maken in het doelgebied. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende middelen heeft om de toevoeging van de load balancers te ondersteunen. Zie [Azure-abonnement en -servicelimieten, quotums en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).

## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
In de volgende procedures wordt uitgelegd hoe u de externe load balancer voorbereidt op de verplaatsing met behulp van een resourcebeheersjabloon en de externe configuratie van de load balancer naar het doelgebied verplaatst met behulp van de Azure-portal. U moet eerst de openbare IP-configuratie van externe load balancer exporteren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>De openbare IP-sjabloon exporteren en het openbare IP-adres implementeren vanuit de portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Resourcegroepen**.
2. Zoek de brongroep die het openbare bron-IP-ip bevat en selecteer deze.
3. Selecteer **De** > **sjabloon Instellingen exporteren**.
4. Selecteer **Implementeren** onder **Sjabloon Exporteren**.
5. Selecteer **parameters voor sjabloon** > **bewerken** om het bestand parameters.json in de onlineeditor te openen.
8. Als u de parameter van de openbare IP-naam wilt bewerken, wijzigt u de **waardeeigenschap** onder **parameters** van de ip-naam van de bronopenbare bron in de naam van uw openbare doel-IP. Sluit de naam in aanhalingstekens.

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

    Selecteer **Opslaan** in de editor.

9.  Selecteer **sjabloon SJABLOON** > **bewerken** om het bestand template.json in de online editor te openen.

10. Als u het doelgebied wilt bewerken waarnaar het openbare IP wordt verplaatst, wijzigt u de **locatieeigenschap** onder **resources:**

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
  
    Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het opmaken van locatiecodes voor regio's. De code voor een regio is de regionaam zonder spaties. De code voor Centraal VS is bijvoorbeeld **centralus.**
    
12. U ook andere parameters in de sjabloon wijzigen als u dat wilt of wilt, afhankelijk van uw vereisten:

    * **SKU**. U de SKU van het openbare IP-adres in de configuratie wijzigen van standaard naar basis of van basis naar standaard door de **eigenschap naam** onder **sku** in het bestand template.json te wijzigen:

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

        Zie [Een openbaar IP-adres maken, wijzigen of verwijderen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)voor informatie over de verschillen tussen basis- en standaard IP-adressen van sku's en standaard-SKU.

    * **Openbare IP-toewijzingsmethode** en **niet-actieve time-out**. U de openbare IP-toewijzingsmethode wijzigen door de eigenschap **publicIPAllocationMethod** te wijzigen van **Dynamisch** naar **Statisch** of van **Statisch** naar **Dynamisch**. U de niet-actieve time-out wijzigen door de eigenschap **idleTimeoutInMinutes** in de gewenste waarde te wijzigen. De standaardinstelling is **4**.

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

        Zie [Een openbaar IP-adres maken, wijzigen of verwijderen voor](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)informatie over de toewijzingsmethoden en inactieve time-outwaarden.

 
13. Selecteer **Opslaan** in de online editor.

14. Selecteer > **BASICS-abonnement** om het abonnement te kiezen waarbij het doel-openbaar IP wordt geïmplementeerd. **BASICS**

15. Selecteer **de groep BASICS** > **Resource** om de resourcegroep te kiezen waar het openbare target-IP-adres wordt geïmplementeerd. U **Nieuw maken** selecteren om een nieuwe brongroep voor het ip-adres van de doelgroep te maken. Zorg ervoor dat de naam niet hetzelfde is als de bronbrongroep van het bestaande openbare bron-IP.Zorg ervoor dat de naam niet hetzelfde is als de bronbrongroep van het bestaande openbare bron-IP.

16. Controleer of > **BASICS-locatie** is ingesteld op de doellocatie waar u het openbare IP-adres wilt implementeren. **BASICS**

17. Controleer **onder INSTELLINGEN**of de naam overeenkomt met de naam die u eerder in de parameterseditor hebt ingevoerd.

18. Schakel het selectievakje **ALGEMENE VOORWAARDEN** in.

19. Selecteer **Kopen** om het openbare doel-IP te implementeren.

20. Als u nog een openbaar IP-adres hebt dat wordt gebruikt voor uitgaande NAT voor de load balancer die wordt verplaatst, herhaalt u de vorige stappen om het tweede uitgaande openbare IP-adres naar het doelgebied te exporteren en te implementeren.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>De sjabloon externe load balancer exporteren en de load balancer implementeren vanuit de Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Resourcegroepen**.
2. Zoek de brongroep die de externe load balancer van de bron bevat en selecteer deze.
3. Selecteer **De** > **sjabloon Instellingen exporteren**.
4. Selecteer **Implementeren** onder **Sjabloon Exporteren**.
5. Selecteer **parameters voor sjabloon** > **bewerken** om het bestand parameters.json in de onlineeditor te openen.

5. Als u de parameter van de naam van de externe load balancer wilt bewerken, wijzigt u de **waardeeigenschap** van de naam van de externe load balancer van de bron in de naam van uw externe laadbalansr. Sluit de naam in aanhalingstekens.

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

6.  Als u de waarde wilt bewerken van het doel-openbaar IP dat u in de voorgaande stappen hebt verplaatst, moet u eerst de bron-id verkrijgen en vervolgens plakken in het bestand parameters.json. Ga als het gaat om het verkrijgen van de id:

    1. Meld u in een ander browsertabblad of -venster aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Resourcegroepen**.
    2. Zoek de doelgroep die het openbare IP-adres bevat dat u in de voorgaande stappen hebt verplaatst. Selecteer het.
    3. Selecteer > **Instellingen-eigenschappen**. **Settings**
    4. Markeer in het blad naar rechts de **resource-id** en kopieer deze naar het klembord. U ook **kopiëren naar klembord** rechts van het **Resource-id-pad** selecteren.
    5. Plak de resource-id in de **eigenschap value** in de editor **Parameters bewerken** die is geopend in het andere browservenster of tabblad:

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
    6. Selecteer **Opslaan** in de online editor.


7.  Als u uitgaande NAT- en uitgaande regels voor de load balancer hebt geconfigureerd, ziet u een derde vermelding in dit bestand voor de externe id van het uitgaande openbare IP.If you've configured outbound NAT and outbound rules for the load balancer, you'll see a third entry in this file for the external ID of the outbound public IP. Herhaal de voorgaande stappen in het **doelgebied** om de id voor het uitgaande openbare IP te verkrijgen. Plak die ID in het bestand parameters.json:

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

8.  Selecteer **sjabloon SJABLOON** > **bewerken** om het bestand template.json in de online editor te openen.
9.  Als u het doelgebied wilt bewerken waarnaar de externe configuratie van de lastenbalans wordt verplaatst, wijzigt u de **locatieeigenschap** onder **resources** in het bestand template.json:

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

10. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het opmaken van locatiecodes voor regio's. De code voor een regio is de regionaam zonder spaties. De code voor Centraal VS is bijvoorbeeld **centralus.**

11. U ook andere parameters in de sjabloon wijzigen als u dat wilt of wilt, afhankelijk van uw vereisten:

    * **SKU**. U de SKU van de externe load balancer in de configuratie wijzigen van standaard naar basis of van basis naar standaard door de **eigenschap naam** onder **sku** in het bestand template.json te wijzigen:

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
      Zie overzicht van [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)voor informatie over de verschillen tussen basis- en standaard SKU-loadbalancers.

    * **Regels voor taakverdeling**. U regels voor taakverdeling toevoegen of verwijderen in de configuratie door items toe te voegen of te verwijderen in het gedeelte **loadBalancingRules** van het bestand template.json:

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
       Zie [Wat is Azure Load Balancer voor](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)informatie over regels voor het balanceren van de belasting?

    * **Sondes.** U een sonde voor de load balancer in de configuratie toevoegen of verwijderen door items toe te voegen of te verwijderen in het **sectie probes** van het bestand template.json:

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
       Zie [Health Probes van Load Balancer voor](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)meer informatie .

    * **Binnenkomende NAT-regels**. U binnenkomende NAT-regels voor de load balancer toevoegen of verwijderen door vermeldingen toe te voegen of te verwijderen in het gedeelte **inboundNatRules** van het bestand template.json:

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
        Als u de toevoeging of verwijdering van een binnenkomende NAT-regel wilt voltooien, moet de regel aanwezig zijn of worden verwijderd als een **eigenschap type** aan het einde van het bestand template.json:

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
        Zie Wat is Azure Load Balancer voor informatie over binnenkomende [NAT-regels?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Uitgaande regels**. U uitgaande regels in de configuratie toevoegen of verwijderen door de eigenschap **outboundRules** in het bestand template.json te bewerken:

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

         Zie [Uitgaande regels voor Load Balancer voor](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)meer informatie .

12. Selecteer **Opslaan** in de online editor.

13. Selecteer > **BASICS-abonnement** om het abonnement te kiezen waarbij de externe laadbakter wordt geïmplementeerd. **BASICS**

15. Selecteer **de groep BASICS** > **Resource** om de resourcegroep te kiezen waar de doellastbalancer wordt geïmplementeerd. U **Nieuw maken** selecteren om een nieuwe resourcegroep te maken voor de externe taakreder van de doelgroep. U ook de bestaande resourcegroep kiezen die u eerder hebt gemaakt voor het openbare IP-adres. Controleer of de naam niet hetzelfde is als de bronbrongroep van de bestaande externe bronbalans.

16. Controleer of > **BASICS-locatie** is ingesteld op de doellocatie waar u de externe load balancer wilt implementeren. **BASICS**

17. Controleer **onder INSTELLINGEN**of de naam overeenkomt met de naam die u eerder in de parameterseditor hebt ingevoerd. Controleer of de bron-id's worden ingevuld voor openbare IP's in de configuratie.

18. Schakel het selectievakje **ALGEMENE VOORWAARDEN** in.

19. Selecteer **Kopen** om het openbare doel-IP te implementeren.

## <a name="discard"></a>Verwijderen

Als u het doel-ip-ip en de externe load balancer wilt verwijderen, verwijdert u de brongroep die deze bevat. Selecteer hiervoor de brongroep in uw dashboard in de portal en selecteer **Verwijderen** boven aan de overzichtspagina.

## <a name="clean-up"></a>Opruimen

Als u de wijzigingen wilt vastleggen en de verplaatsing van de openbare IP- en externe load balancer wilt voltooien, verwijdert u de bronopenbare IP- en externe load balancer of resourcegroep. Selecteer hiervoor die brongroep in uw dashboard in de portal en selecteer **Verwijderen** boven aan elke pagina.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een externe laadbakr van Azure van de ene regio naar de andere verplaatst en de bronbronnen opgeschoond. Zie voor meer informatie over het verplaatsen van resources tussen regio's en disaster recovery in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
