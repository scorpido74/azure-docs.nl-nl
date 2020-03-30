---
title: Een virtueel Azure-netwerk verplaatsen naar een andere Azure-regio met behulp van de Azure-portal.
description: Verplaats een Virtueel Azure-netwerk van de ene Azure-regio naar het andere met behulp van een Resource Manager-sjabloon en de Azure-portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: c54725d9a947b0c912a822686d7b2cffe1a7b5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640785"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Een virtueel Azure-netwerk verplaatsen naar een andere regio met behulp van de Azure-portal

Er zijn verschillende scenario's voor het verplaatsen van een bestaand Virtueel Azure-netwerk van de ene regio naar de andere. U bijvoorbeeld een virtueel netwerk maken met dezelfde configuratie voor testen en beschikbaarheid als uw bestaande virtuele netwerk. Of u wilt een virtueel productienetwerk verplaatsen naar een andere regio als onderdeel van uw planning voor noodherstel.

U een Azure Resource Manager-sjabloon gebruiken om de verplaatsing van het virtuele netwerk naar een andere regio te voltooien. Dit doe je door het virtuele netwerk naar een sjabloon te exporteren, de parameters te wijzigen die overeenkomen met het doelgebied en vervolgens de sjabloon naar het nieuwe gebied te implementeren. Zie [Snelaande: Azure Resource Manager-sjablonen maken en implementeren met behulp van de Azure-portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)voor meer informatie over Resource Manager-sjablonen.


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat uw virtuele netwerk zich in de Azure-regio bevindt waarvan u wilt overstappen.

- Als u een virtueel netwerk wilt exporteren en een sjabloon wilt implementeren om een virtueel netwerk in een andere regio te maken, moet u de rol Netwerkbijdrager of hoger hebben.

- Virtuele netwerkpeeringen worden niet opnieuw gemaakt en ze mislukken als ze nog steeds aanwezig zijn in de sjabloon. Voordat u de sjabloon exporteert, moet u virtuele netwerkpeers verwijderen. U ze vervolgens herstellen na de virtuele netwerkverplaatsing.

- Identificeer de indeling van de bronnetwerkindeling en alle bronnen die u momenteel gebruikt. Deze lay-out omvat, maar is niet beperkt tot load balancers, network security groups (NSGs) en openbare IP's.

- Controleer of u met uw Azure-abonnement virtuele netwerken maken in het doelgebied. Neem contact op met ondersteuning om het vereiste quotum in te schakelen.

- Zorg ervoor dat uw abonnement voldoende middelen heeft om de toevoeging van virtuele netwerken voor dit proces te ondersteunen. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits) voor meer informatie.


## <a name="prepare-for-the-move"></a>Bereid je voor op de verhuizing
In deze sectie bereidt u het virtuele netwerk voor op de verhuizing met behulp van een resourcemanagersjabloon. Vervolgens verplaatst u het virtuele netwerk naar het doelgebied met behulp van de Azure-portal.

Ga als volgt te werk om het virtuele netwerk te exporteren en het virtuele doelnetwerk te implementeren met behulp van de Azure-portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en selecteer **resourcegroepen**.
1. Zoek de brongroep die het virtuele bronnetwerk bevat en selecteer deze vervolgens.
1. Selecteer **De** > **sjabloon Instellingen exporteren**.
1. Selecteer **In** het deelvenster Sjabloon exporteren de optie **Implementeren**.
1. Als u het bestand *parameters.json* wilt openen in uw online editor, selecteert u **Parameters voor sjabloonbewerken** > **Edit parameters**.
1. Als u de parameter van de naam van het virtuele netwerk wilt bewerken, wijzigt u de **eigenschap waarde** onder **parameters:**

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

1. Wijzig in de editor de waarde van de bronvirtuele netwerknaam in de editor in een gewenste naam voor het virtuele doelnetwerk. Zorg ervoor dat u de naam tussen aanhalingstekens insluit.

1. Selecteer **Opslaan** in de editor.

1. Als u het *bestand template.json* wilt openen in de onlineeditor, selecteert u **Sjabloon** > **bewerken .**

1. In de online editor, om de doelregio te bewerken waar het virtuele netwerk wordt verplaatst, wijzigt u de **locatieeigenschap** onder **resources:**

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

1. Zie Azure-locaties voor het verkrijgen van [locatiecodes voor regio's.](https://azure.microsoft.com/global-infrastructure/locations/) De code voor een regio is de regionaam, zonder spaties (bijvoorbeeld **Central US** = **centralus**).

1. (Optioneel) U ook andere parameters in de sjabloon wijzigen, afhankelijk van uw vereisten:

    * **Adresruimte:** Voordat u het bestand opslaat, u de adresruimte van het virtuele netwerk wijzigen door de sectie **resources** > **addressSpace** te wijzigen en de eigenschap **adresvoorvoegsels te** wijzigen:

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

    * **Subnet**: U de subnetnaam en de subnetadresruimte wijzigen of toevoegen door de **subnettensectie** van de sjabloon te wijzigen. U de naam van het subnet wijzigen door de **eigenschap naam** te wijzigen. En u de subnetadresruimte wijzigen door de eigenschap **adresvoorvoegsel te** wijzigen:

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

        Als u het adresvoorvoegsel in het *bestand template.json* wilt wijzigen, bewerkt u het op twee plaatsen: in de code in de vorige sectie en in het **tekstgedeelte** van de volgende code. Wijzig de eigenschap **adresVoorvoegsel** in de volgende code om overeen te komen met de eigenschap **adresVoorvoegsel** in de code in de vorige sectie.

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

1. Selecteer **opslaan**in de online editor .

1. Als u het abonnement wilt kiezen waarbij het doelvirtuele netwerk wordt geïmplementeerd, selecteert u **Basics** > **Subscription**.

1. Als u de resourcegroep wilt kiezen waarin het virtuele doelnetwerk wordt geïmplementeerd, selecteert u de > **groep** **Basisresources**. 

    Als u een nieuwe brongroep voor het virtuele doelnetwerk wilt maken, selecteert u **Nieuw maken**. Zorg ervoor dat de naam niet hetzelfde is als de naam van de bronbrongroep in het bestaande virtuele netwerk.

1. Controleer of > **basislocatie** is ingesteld op de doellocatie waar u het virtuele netwerk wilt implementeren. **Basics**

1. Controleer **onder Instellingen**of de naam overeenkomt met de naam die u eerder hebt ingevoerd in de parameterseditor.

1. Schakel het selectievakje **Algemene voorwaarden** in.

1. Als u het virtuele doelnetwerk wilt implementeren, selecteert u **Aankoop**.

## <a name="delete-the-target-virtual-network"></a>Het virtuele doelnetwerk verwijderen

Als u het virtuele doelnetwerk wilt verwijderen, verwijdert u de brongroep die het virtuele doelnetwerk bevat. Dit doet u als volgt:
1. Selecteer in het Azure-portaldashboard de brongroep.
1. Selecteer Boven aan het deelvenster **Overzicht** de optie **Verwijderen**.

## <a name="clean-up"></a>Opruimen

Als u de wijzigingen wilt vastleggen en de virtuele netwerkverplaatsing wilt voltooien, verwijdert u het virtuele netwerk of de brongroep. Dit doet u als volgt:
1. Selecteer in het Azure-portaldashboard de virtuele netwerk- of resourcegroep.
1. Selecteer Boven aan elk deelvenster **Delete**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een virtueel Azure-netwerk van de ene regio naar de andere verplaatst met behulp van de Azure-portal en vervolgens de onnodige bronbronnen opgeschoond. Zie voor meer informatie over het verplaatsen van resources tussen regio's en disaster recovery in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure-virtuele machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
