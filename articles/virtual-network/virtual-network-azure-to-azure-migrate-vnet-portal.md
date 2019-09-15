---
title: Verplaats Azure-Virtual Network naar een andere Azure-regio met behulp van de Azure Portal.
description: Gebruik Azure Resource Manager sjabloon om Azure Virtual Network van de ene Azure-regio naar de andere te verplaatsen met behulp van de Azure Portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: d0f8a52eace9af4dc0865e1a2f277b9ab31c6858
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997417"
---
# <a name="move-azure-virtual-network-to-another-region-using-the-azure-portal"></a>Verplaats Azure-Virtual Network naar een andere regio met behulp van de Azure Portal

Er zijn verschillende scenario's waarin u uw bestaande Azure Virtual Networks (VNETs) wilt verplaatsen van de ene regio naar een andere. U kunt bijvoorbeeld een virtueel netwerk maken met dezelfde configuratie voor testen en beschik baarheid van uw bestaande virtuele netwerk. Het is ook mogelijk dat u een virtueel netwerk voor productie naar een andere regio wilt verplaatsen als onderdeel van de planning voor nood herstel.

U kunt een Azure Resource Manager sjabloon gebruiken om de verplaatsing van het virtuele netwerk naar een andere regio te volt ooien. Hiervoor exporteert u het virtuele netwerk naar een sjabloon, wijzigt u de para meters zodat deze overeenkomen met de doel regio en implementeert u de sjabloon vervolgens in de nieuwe regio.  Voor meer informatie over Resource Manager en sjablonen, Zie [Quick Start: Azure Resource Manager-sjablonen maken en implementeren via Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de Azure-Virtual Network zich bevindt in de Azure-regio van waaruit u wilt verplaatsen.

- Als u een virtueel netwerk wilt exporteren en een sjabloon wilt implementeren om een virtueel netwerk in een andere regio te maken, hebt u de rol netwerk bijdrager of hoger nodig.

- Peering van virtuele netwerken wordt niet opnieuw gemaakt en mislukt als deze nog in de sjabloon aanwezig zijn.  U moet alle peers van het virtuele netwerk verwijderen voordat u de sjabloon exporteert en vervolgens de peers na het verplaatsen van het virtuele netwerk opnieuw tot stand brengt.
    
- Identificeer de bron netwerk indeling en alle resources die u momenteel gebruikt. Deze indeling bevat, maar is niet beperkt tot load balancers, netwerk beveiligings groepen (Nsg's) en open bare Ip's.

- Controleer of u met uw Azure-abonnement virtuele netwerken kunt maken in de doel regio die wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft ter ondersteuning van het toevoegen van virtuele netwerken voor dit proces.  Zie [Azure-abonnement en service limieten, quota's en beperkingen](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
De volgende stappen laten zien hoe u het virtuele netwerk voorbereidt voor het verplaatsen met behulp van een resource manager-sjabloon en het virtuele netwerk naar de doel regio verplaatst met behulp van de portal.

### <a name="export-the-template-and-deploy-from-the-portal"></a>De sjabloon exporteren en implementeren vanuit de portal

1. Meld u aan bij de [Azure Portal](http://portal.azure.com) > -**resource groepen**.
2. Zoek de resource groep die het virtuele bron netwerk bevat en klik erop.
3. Selecteer > **instellingen** > **sjabloon exporteren**.
4. Kies **implementeren** op de Blade **sjabloon exporteren** .
5. Klik op **sjabloon** > **bewerken para meters** om het bestand **para meters. json** in de online-editor te openen.
6. Als u de para meter van de naam van het virtuele netwerk wilt bewerken, wijzigt u de eigenschap **Value** onder **para meters**:
    
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
7. Wijzig de waarde van de naam van het virtuele netwerk van de bron in de editor in een naam van uw keuze voor het doel-VNET. Zorg ervoor dat u de naam tussen aanhalings tekens plaatst.

8.  Klik op **Opslaan** in de editor.

9.  Klik op sjabloon**bewerken sjabloon** om het bestand **Template. json** in de online-editor te openen. >  

10. Als u de doel regio wilt bewerken waar het VNET wordt verplaatst, wijzigt u de eigenschap **Location** onder **resources** in de online editor:

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
 
11. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van regio-locatie codes.  De code voor een regio is de naam van de regio zonder spaties, **Central VS** = -**Midden**.
 
12. U kunt ook andere para meters in de sjabloon wijzigen als u ervoor kiest en zijn optioneel, afhankelijk van uw vereisten:

    * **Adres ruimte** : de adres ruimte van het VNET kan vóór het opslaan worden gewijzigd in de sjabloon door de sectie **resources** > **addressSpace** te wijzigen en de eigenschap **addressPrefixes** in de sjabloon te wijzigen **. json** bestand:
    
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

    * **Subnet** : de naam van het subnet en de adres ruimte van het subnet kunnen worden gewijzigd of toegevoegd aan door de sectie **subnets** van het bestand **sjabloon. json** te wijzigen. De naam van het subnet kan worden gewijzigd door de eigenschap **name** in het bestand **Template. json** te wijzigen.  De adres ruimte van het subnet kan worden gewijzigd door de eigenschap **addressPrefix** in het bestand **Template. json** te wijzigen:
    
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
                    ],
    ```

    Als u het adres voorvoegsel wilt wijzigen in het bestand **Template. json** , moet dit worden bewerkt op twee plaatsen, de hierboven vermelde sectie en de sectie **type** die hieronder wordt weer gegeven.  Wijzig de eigenschap **addressPrefix** zodat deze overeenkomt met het bovenstaande:
                
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

13. Klik op **Opslaan** in de online editor.

14. Klik op **basis** > **abonnement** om het abonnement te kiezen waarin het doel-VNET wordt geïmplementeerd.

15. Klik op **basis** > **bronnen groep** om de resource groep te kiezen waarin het doel-VNET wordt geïmplementeerd.  U kunt op **Nieuw maken** klikken om een nieuwe resource groep te maken voor het doel-VNET.  Zorg ervoor dat de naam niet hetzelfde is als de bron resource groep van het bestaande VNET. 

16. De**locatie** van de **basis beginselen** > controleren is ingesteld op de doel locatie waar u het VNET wilt implementeren.

17. Controleer onder **instellingen** of de naam overeenkomt met de naam die u hebt ingevoerd in de bovenstaande para meters-editor.

18. Schakel het selectie vakje onder **voor waarden**in.

19. Klik op de knop **aanschaffen** om het virtuele doel netwerk te implementeren.

## <a name="discard"></a>Verwijderen 

Als u het virtuele doel netwerk wilt verwijderen, verwijdert u de resource groep die het virtuele doel netwerk bevat.  Hiervoor selecteert u de resource groep in het dash board in de portal en selecteert u **verwijderen** boven aan de pagina overzicht.

## <a name="clean-up"></a>Opruimen

Als u de wijzigingen wilt door voeren en de verplaatsing van het virtuele netwerk wilt volt ooien, verwijdert u het virtuele bron netwerk of de resource groep. Als u dit wilt doen, selecteert u het virtuele netwerk of de resource groep in het dash board in de portal en selecteert u **verwijderen** boven aan elke pagina.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure-Virtual Network verplaatst van de ene regio naar een andere en de bron resources opgeschoond.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
