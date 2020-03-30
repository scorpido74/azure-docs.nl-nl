---
title: Azure-netwerkbeveiligingsgroep (NSG) verplaatsen naar een andere Azure-regio met behulp van de Azure-portal
description: Gebruik de sjabloon Azure Resource Manager om azure-netwerkbeveiligingsgroep van de ene Azure-regio naar de andere te verplaatsen met behulp van de Azure-portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: dce267178c3caf813ccdcac4bba86ccfde3f3421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647183"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Azure-netwerkbeveiligingsgroep (NSG) verplaatsen naar een andere regio met behulp van de Azure-portal

Er zijn verschillende scenario's waarin u uw bestaande NSGs van de ene regio naar de andere wilt verplaatsen. U bijvoorbeeld een NSG maken met dezelfde configuratie- en beveiligingsregels voor het testen. U ook een NSG verplaatsen naar een andere regio als onderdeel van de planning voor noodherstel.

Azure-beveiligingsgroepen kunnen niet van de ene regio naar de andere worden verplaatst. U echter wel een Azure Resource Manager-sjabloon gebruiken om de bestaande configuratie- en beveiligingsregels van een NSG te exporteren.  U de resource vervolgens in een andere regio fasen door de NSG naar een sjabloon te exporteren, de parameters te wijzigen die overeenkomen met het doelgebied en de sjabloon vervolgens te implementeren in de nieuwe regio.  Zie [Snelaande: Azure Resource Manager-sjablonen maken en implementeren met behulp van de Azure-portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)voor meer informatie over ResourceBeheer en sjablonen.


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de Azure-netwerkbeveiligingsgroep zich in het Azure-gebied bevindt van waaruit u wilt verplaatsen.

- Azure-netwerkbeveiligingsgroepen kunnen niet tussen regio's worden verplaatst.  U moet de nieuwe NSG koppelen aan resources in de doelregio.

- Als u een NSG-configuratie wilt exporteren en een sjabloon wilt implementeren om een NSG in een andere regio te maken, hebt u de rol Netwerkbijdrager of hoger nodig.

- Identificeer de indeling van de bronnetwerkindeling en alle bronnen die u momenteel gebruikt. Deze lay-out omvat, maar is niet beperkt tot load balancers, openbare IP's en virtuele netwerken.

- Controleer of u met uw Azure-abonnement NSG's maken in het doelgebied dat wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende middelen heeft om de toevoeging van NSG's voor dit proces te ondersteunen.  Zie [Azure-abonnement en -servicelimieten, quotums en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
In de volgende stappen wordt uitgelegd hoe u de netwerkbeveiligingsgroep voorbereidt op de verplaatsing van de configuratie- en beveiligingsregel met behulp van een resourcebeheersjabloon en de NSG-configuratie- en beveiligingsregels via de portal naar het doelgebied verplaatsen.


### <a name="export-the-template-and-deploy-from-the-portal"></a>De sjabloon exporteren en implementeren vanuit de portal

1. Log in bij de [Azure-portal](https://portal.azure.com) > **Resourcegroepen**.
2. Zoek de resourcegroep die de bron NSG bevat en klik erop.
3. Selecteer > **>-instellingen-exportsjabloon**. **Settings**
4. Kies **Implementeren** in het **sjabloonblad Exporteren.**
5. Klik **op PARAMETERS SJABLOON** > **bewerken** om het bestand **parameters.json** in de online editor te openen.
6. Als u de parameter van de NSG-naam wilt bewerken, wijzigt u de **eigenschap waarde** onder **parameters:**

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Wijzig de bron-NSG-waarde in de editor in een naam naar keuze voor de doel-NSG. Zorg ervoor dat u de naam in aanhalingstekens bijsluit.

8.  Klik **op Opslaan** in de editor.

9.  Klik **op sjabloon** > **Bewerken sjabloon** om het **bestand template.json** in de online editor te openen.

10. Als u het doelgebied wilt bewerken waar de NSG-configuratie- en beveiligingsregels worden verplaatst, wijzigt u de **locatieeigenschap** onder **resources** in de onlineeditor:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. Zie Azure-locaties voor het verkrijgen van [locatiecodes voor regio's.](https://azure.microsoft.com/global-infrastructure/locations/)  De code voor een regio is de regionaam zonder spaties, **Central US** = **centralus**.

12. U desgevraagd ook andere parameters in de sjabloon wijzigen en zijn optioneel, afhankelijk van uw vereisten:

    * **Beveiligingsregels** : welke regels worden geïmplementeerd in de beoogde NSG, u bewerken door regels toe te voegen of te verwijderen aan de sectie **securityRules** in het bestand **template.json:**

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      Als u de toevoeging of verwijdering van de regels in de doel-NSG wilt voltooien, moet u ook de aangepaste regeltypen aan het einde van het **bestand template.json** bewerken in de indeling van het onderstaande voorbeeld:

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. Klik **op Opslaan** in de online editor.

14. Klik op > **BASICS-abonnement** om het abonnement te kiezen waarbij het doel NSG wordt geïmplementeerd. **BASICS**

15. Klik op **de** > **groep BASICS Resource** om de resourcegroep te kiezen waar de doel-NSG wordt geïmplementeerd.  U op **Nieuw maken** klikken om een nieuwe resourcegroep voor de doel-NSG te maken.  Zorg ervoor dat de naam niet hetzelfde is als de bronbrongroep van de bestaande NSG.

16. Controleer **basics** > **locatie** is ingesteld op de doellocatie waar u wilt dat de NSG wordt geïmplementeerd.

17. Controleer onder **INSTELLINGEN** of de naam overeenkomt met de naam die u hebt ingevoerd in de bovenstaande parameterseditor.

18. Schakel het selectievakje in onder **ALGEMENE VOORWAARDEN**.

19. Klik **op** de knop Aankoop om de beveiligingsgroep van het doelnetwerk te implementeren.

## <a name="discard"></a>Verwijderen

Als u de doelNSG wilt verwijderen, verwijdert u de brongroep die de beoogde NSG bevat.  Selecteer hiervoor de brongroep in uw dashboard in de portal en selecteer **Verwijderen** boven aan de overzichtspagina.

## <a name="clean-up"></a>Opruimen

Als u de wijzigingen wilt vastleggen en de verplaatsing van de NSG wilt voltooien, verwijdert u de bron NSG of resourcegroep. Selecteer hiervoor de netwerkbeveiligingsgroep of -brongroep in uw dashboard in de portal en selecteer **Verwijderen** boven aan elke pagina.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure-netwerkbeveiligingsgroep van de ene regio naar de andere verplaatst en de bronbronnen opgeschoond.  Zie voor meer informatie over het verplaatsen van resources tussen regio's en disaster recovery in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
