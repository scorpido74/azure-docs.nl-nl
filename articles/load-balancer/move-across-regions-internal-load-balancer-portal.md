---
title: Azure internal Load Balancer verplaatsen naar een andere Azure-regio met behulp van de Azure-portal
description: Sjabloon Azure Resource Manager gebruiken om Azure internal Load Balancer van het ene Azure-gebied naar het andere te verplaatsen met behulp van de Azure-portal
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: f23923b9d847ef393ebd609eb5fbba530b1a07d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638803"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Azure internal Load Balancer verplaatsen naar een andere regio met behulp van de Azure-portal

Er zijn verschillende scenario's waarin u uw bestaande interne load balancer van de ene regio naar de andere wilt verplaatsen. U bijvoorbeeld een interne load balancer maken met dezelfde configuratie voor het testen. U ook een interne load balancer naar een andere regio verplaatsen als onderdeel van de planning voor noodherstel.

Azure interne load balancers kunnen niet van de ene regio naar de andere worden verplaatst. U echter wel een Azure Resource Manager-sjabloon gebruiken om de bestaande configuratie en het virtuele netwerk van een interne load balancer te exporteren.  U de resource vervolgens in een andere regio fasen door de load balancer en het virtuele netwerk naar een sjabloon te exporteren, de parameters te wijzigen die overeenkomen met het doelgebied en de sjablonen vervolgens naar de nieuwe regio te implementeren.  Zie [Snelaande: Azure Resource Manager-sjablonen maken en implementeren met behulp van de Azure-portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)voor meer informatie over ResourceBeheer en sjablonen.


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de interne load balancer van Azure zich in het Azure-gebied bevindt van waaruit u wilt verplaatsen.

- Azure internal load balancers kunnen niet tussen regio's worden verplaatst.  U moet de nieuwe load balancer koppelen aan resources in het doelgebied.

- Als u een interne configuratie van de load balancer wilt exporteren en een sjabloon wilt implementeren om een interne load balancer in een andere regio te maken, hebt u de rol Netwerkbijdrager of hoger nodig.

- Identificeer de indeling van de bronnetwerkindeling en alle bronnen die u momenteel gebruikt. Deze lay-out omvat, maar is niet beperkt tot load balancers, netwerkbeveiligingsgroepen, virtuele machines en virtuele netwerken.

- Controleer of u met uw Azure-abonnement interne load balancers maken in het doelgebied dat wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende resources heeft om de toevoeging van load balancers voor dit proces te ondersteunen.  Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
In de volgende stappen wordt uitgelegd hoe u de interne load balancer voorbereidt op de verhuizing met behulp van een Resource Manager-sjabloon en de interne configuratie van de load balancer naar het doelgebied verplaatsen met behulp van de Azure-portal.  Als onderdeel van dit proces moet de virtuele netwerkconfiguratie van de interne load balancer worden opgenomen en moet dit eerst worden gedaan voordat de interne load balancer wordt verplaatst.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>De virtuele netwerksjabloon exporteren en implementeren vanuit de Azure-portal

1. Log in bij de [Azure-portal](https://portal.azure.com) > **Resourcegroepen**.
2. Zoek de resourcegroep die het virtuele bronnetwerk bevat en klik erop.
3. Selecteer > **>-instellingen-exportsjabloon**. **Settings**
4. Kies **Implementeren** in het **sjabloonblad Exporteren.**
5. Klik **op PARAMETERS SJABLOON** > **bewerken** om het bestand **parameters.json** in de online editor te openen.
6. Als u de parameter van de naam van het virtuele netwerk wilt bewerken, wijzigt u de **eigenschap waarde** onder **parameters:**

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Wijzig de waarde van de bronvirtuele netwerknaam in de editor in een naam naar keuze voor het doel VNET. Zorg ervoor dat u de naam in aanhalingstekens bijsluit.

8. Klik **op Opslaan** in de editor.

9. Klik **op sjabloon** > **Bewerken sjabloon** om het **bestand template.json** in de online editor te openen.

10. Als u het doelgebied wilt bewerken waar het VNET wordt verplaatst, wijzigt u de **locatieeigenschap** onder resources:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

11. Zie Azure-locaties voor het verkrijgen van [locatiecodes voor regio's.](https://azure.microsoft.com/global-infrastructure/locations/)  De code voor een regio is de regionaam zonder spaties, **Central US** = **centralus**.

12. U desgevraagd ook andere parameters in het **bestand template.json** wijzigen en zijn optioneel, afhankelijk van uw vereisten:

    * **Adresruimte** - De adresruimte van het VNET kan worden gewijzigd voordat u deze opslaat door de sectie **resources** > **addressSpace** te wijzigen en de eigenschap **adresvoorvoegsels** in het **bestand template.json** te wijzigen:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Subnet** - De subnetnaam en de subnetadresruimte kunnen worden gewijzigd of toegevoegd door de **subnettensectie** van het **bestand template.json** te wijzigen. De naam van het subnet kan worden gewijzigd door de **eigenschap naam** te wijzigen. De subnetadresruimte kan worden gewijzigd door de eigenschap **addressPrefix** in het **bestand template.json te** wijzigen:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         In het **bestand template.json** moet het, om het adresvoorvoegsel te wijzigen, op twee plaatsen worden bewerkt, de bovenstaande sectie en de **onderstaande tekstsectie.**  Wijzig de eigenschap **adresvoorvoegsel** dat overeenkomt met de bovenstaande eigenschap:

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

13. Klik **op Opslaan** in de online editor.

14. Klik op > **BASICS-abonnement** om het abonnement te kiezen waarbij het doel VNET wordt geïmplementeerd. **BASICS**

15. Klik op **de** > **groep BASICS Resource** om de resourcegroep te kiezen waarin de doelgroep VNET wordt geïmplementeerd.  U op **Nieuw maken** klikken om een nieuwe resourcegroep voor het doel VNET te maken.  Zorg ervoor dat de naam niet hetzelfde is als de bronbrongroep van het bestaande VNET.

16. Controleer **basics** > **locatie** is ingesteld op de doellocatie waar u wilt dat de VNET worden ingezet.

17. Controleer onder **INSTELLINGEN** of de naam overeenkomt met de naam die u hebt ingevoerd in de bovenstaande parameterseditor.

18. Schakel het selectievakje in onder **ALGEMENE VOORWAARDEN**.

19. Klik **op** de knop Aankoop om het virtuele doelnetwerk te implementeren.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>De sjabloon voor interne lastenbalans exporteren en implementeren vanuit Azure PowerShell

1. Log in bij de [Azure-portal](https://portal.azure.com) > **Resourcegroepen**.
2. Zoek de resourcegroep met de broninterne load balancer en klik erop.
3. Selecteer > **>-instellingen-exportsjabloon**. **Settings**
4. Kies **Implementeren** in het **sjabloonblad Exporteren.**
5. Klik **op PARAMETERS SJABLOON** > **bewerken** om het bestand **parameters.json** in de online editor te openen.

6. Als u de parameter van de naam van de interne load balancer wilt bewerken, wijzigt u de standaardwaarde van de eigenschap **standaardWaarde** van de naam van de broninterne load balancer in de naam van uw doelinterne load balancer, moet u ervoor zorgen dat de naam tussen aanhalingstekens staat:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Als u de waarde wilt bewerken van het hierboven verplaatste doelnetwerk, moet u eerst de bron-id verkrijgen en vervolgens kopiëren en plakken in het bestand **parameters.json.** Ga als het gaat om het verkrijgen van de id:

    1. Meld u aan bij de [Azure-portal](https://portal.azure.com) > **ResourceGroepen** in een ander browsertabblad of -venster.
    2. Zoek de doelgroep die het verplaatste virtuele netwerk bevat vanuit de bovenstaande stappen en klik erop.
    3. Selecteer **>-instellingen** > **eigenschappen**.
    4. Markeer in het blad naar rechts de **resource-id** en kopieer deze naar het klembord.  U ook klikken op de **knop kopiëren naar klembord** rechts van het **Resource ID-pad.**
    5. Plak de resource-id in de eigenschap **defaultValue** in de editor **Parameters bewerken** die is geopend in het andere browservenster of tabblad:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Klik **op Opslaan** in de online editor.

7. Klik **op sjabloon** > **Bewerken sjabloon** om het **bestand template.json** in de online editor te openen.
8. Als u het doelgebied wilt bewerken waar de configuratie van de interne load balancer wordt verplaatst, wijzigt u de **locatieeigenschap** onder **resources** in het **bestand template.json:**

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  Zie Azure-locaties voor het verkrijgen van [locatiecodes voor regio's.](https://azure.microsoft.com/global-infrastructure/locations/)  De code voor een regio is de regionaam zonder spaties, **Central US** = **centralus**.

10. U desgevraagd ook andere parameters in de sjabloon wijzigen en zijn optioneel, afhankelijk van uw vereisten:

    * **Sku** - U de sku van de interne load balancer in de configuratie wijzigen van standaard naar basis- of standaard door de eigenschap **sku-naam** > **name** in het **bestand template.json** te wijzigen:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Zie overzicht van [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) voor meer informatie over de verschillen tussen basis- en standaardsku-loadbalancers

    * **Regels voor taakverdeling** - U regels voor taakverdeling toevoegen of verwijderen in de configuratie door items toe te voegen of te verwijderen aan het gedeelte **loadBalancingRules** van het **bestand template.json:**

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
       Zie Wat is Azure Load [Balancer voor](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) meer informatie over regels voor het balanceren van de belasting?

    * **Probes** - U een sonde voor de load balancer in de configuratie toevoegen of verwijderen door items toe te voegen of te verwijderen aan het **sectie probes-bestand** van het **bestand template.json:**

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
       Zie [Statusssen](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) van Load Balancer voor meer informatie over statussen van Azure Load Balancer.

    * **Binnenkomende NAT-regels** - U binnenkomende NAT-regels voor de load balancer toevoegen of verwijderen door vermeldingen toe te voegen of te verwijderen in het gedeelte **inboundNatRules** van het **sjabloon.json-bestand:**

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
        Als u de toevoeging of verwijdering van een binnenkomende NAT-regel wilt voltooien, moet de regel aanwezig zijn of worden verwijderd als een **eigenschap type** aan het einde van het **bestand template.json:**

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
        Zie Wat is Azure Load Balancer voor meer informatie over binnenkomende [NAT-regels?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

12. Klik **op Opslaan** in de online editor.

13. Klik op > **BASICS-abonnement** om het abonnement te kiezen waarbij de interne load balancer wordt geïmplementeerd. **BASICS**

15. Klik op **de** > **groep BASICS Resource** om de resourcegroep te kiezen waarin de doellastbalancer wordt geïmplementeerd.  U op **Nieuw maken** klikken om een nieuwe resourcegroep voor de interne loadbalancer van de doelgroep te maken of de bestaande resourcegroep te kiezen die hierboven is gemaakt voor het virtuele netwerk.  Zorg ervoor dat de naam niet hetzelfde is als de bronbrongroep van de bestaande broninterne load balancer.

16. Controleer **basics** > **locatie** is ingesteld op de doellocatie waar u wilt dat de interne load balancer wordt geïmplementeerd.

17. Controleer onder **INSTELLINGEN** of de naam overeenkomt met de naam die u hebt ingevoerd in de bovenstaande parameterseditor.  Controleer of de bron-id's worden ingevuld voor virtuele netwerken in de configuratie.

18. Schakel het selectievakje in onder **ALGEMENE VOORWAARDEN**.

19. Klik **op** de knop Aankoop om het virtuele doelnetwerk te implementeren.

## <a name="discard"></a>Verwijderen

Als u het doelvirtuele netwerk en de interne load balancer wilt verwijderen, verwijdert u de brongroep die het doelvirtuele netwerk en de interne load balancer bevat.  Selecteer hiervoor de brongroep in uw dashboard in de portal en selecteer **Verwijderen** boven aan de overzichtspagina.

## <a name="clean-up"></a>Opruimen

Als u de wijzigingen wilt vastleggen en de verplaatsing van het virtuele netwerk en de interne load balancer wilt voltooien, verwijdert u het virtuele netwerk en de interne load balancer of resourcegroep. Selecteer hiervoor de virtuele netwerk- en interne loadbalancer of resourcegroep in uw dashboard in de portal en selecteer **Verwijderen** boven aan elke pagina.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een interne laadbakvan Azure van de ene regio naar de andere verplaatst en de bronbronnen opgeschoond.  Zie voor meer informatie over het verplaatsen van resources tussen regio's en disaster recovery in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
