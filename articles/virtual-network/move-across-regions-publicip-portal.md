---
title: Azure Public IP verplaatsen naar een andere Azure-regio met behulp van de Azure-portal
description: Gebruik azure resource manager-sjabloon om Azure Public IP van de ene Azure-regio naar de andere te verplaatsen met behulp van de Azure-portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6d10265e8383b68ebe13c95d8b2a9632668e85da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641395"
---
# <a name="move-azure-public-ip-to-another-region-using-the-azure-portal"></a>Azure Public IP verplaatsen naar een andere regio met de Azure-portal

Er zijn verschillende scenario's waarin u uw bestaande Azure Public IP's van de ene regio naar de andere wilt verplaatsen. U bijvoorbeeld een openbaar IP maken met dezelfde configuratie en sku voor het testen. U ook een openbaar IP-adres verplaatsen naar een andere regio als onderdeel van de planning voor noodherstel.

Azure Public IP's zijn regiospecifiek en kunnen niet van de ene regio naar de andere worden verplaatst. U echter wel een Azure Resource Manager-sjabloon gebruiken om de bestaande configuratie van een openbaar IP-adres te exporteren.  U de resource vervolgens in een andere regio faseren door het openbare IP-adres naar een sjabloon te exporteren, de parameters te wijzigen die overeenkomen met het doelgebied en de sjabloon vervolgens te implementeren in de nieuwe regio.  Zie [Snelaande: Azure Resource Manager-sjablonen maken en implementeren met behulp van de Azure-portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)voor meer informatie over ResourceBeheer en sjablonen.


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat het Azure Public IP zich in de Azure-regio bevindt van waaruit u wilt verplaatsen.

- Azure Public IP's kunnen niet tussen regio's worden verplaatst.  U moet het nieuwe openbare ip koppelen aan resources in de doelregio.

- Als u een openbare IP-configuratie wilt exporteren en een sjabloon wilt implementeren om een openbaar IP-adres in een andere regio te maken, hebt u de rol Netwerkinzender of hoger nodig.

- Identificeer de indeling van de bronnetwerkindeling en alle bronnen die u momenteel gebruikt. Deze lay-out omvat, maar is niet beperkt tot load balancers, network security groups (NSGs) en virtuele netwerken.

- Controleer of u met uw Azure-abonnement openbare IP's maken in het doelgebied dat wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende middelen heeft om de toevoeging van openbare IP's voor dit proces te ondersteunen.  Zie [Azure-abonnement en -servicelimieten, quotums en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
In de volgende stappen wordt uitgelegd hoe u het openbare IP-adres voorbereiden op de configuratieverplaatsing met behulp van een Resource Manager-sjabloon en de openbare IP-configuratie naar het doelgebied verplaatsen met behulp van de Azure-portal.

### <a name="export-the-template-and-deploy-from-a-script"></a>De sjabloon exporteren en implementeren vanuit een script

1. Log in bij de [Azure-portal](https://portal.azure.com) > **Resourcegroepen**.
2. Zoek de resourcegroep die het openbare bron-IP-adres bevat en klik erop.
3. Selecteer > **>-instellingen-exportsjabloon**. **Settings**
4. Kies **Implementeren** in het **sjabloonblad Exporteren.**
5. Klik **op PARAMETERS SJABLOON** > **bewerken** om het bestand **parameters.json** in de online editor te openen.
8. Als u de parameter van de openbare IP-naam wilt bewerken, wijzigt u de eigenschap onder > **parameterswaarde** van de ip-naam van de bronafbeelding in de naam van uw doel-openbaar IP-adres, moet u ervoor zorgen dat de naam tussen aanhalingstekens staat: **parameters**

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
8.  Klik **op Opslaan** in de editor.

9.  Klik **op sjabloon** > **Bewerken sjabloon** om het **bestand template.json** in de online editor te openen.

10. Als u het doelgebied wilt bewerken waar het openbare IP-adres wordt verplaatst, wijzigt u de **locatieeigenschap** onder **resources:**

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

11. Zie Azure-locaties voor het verkrijgen van [locatiecodes voor regio's.](https://azure.microsoft.com/global-infrastructure/locations/)  De code voor een regio is de regionaam zonder spaties, **Central US** = **centralus**.

12. U desgevraagd ook andere parameters in de sjabloon wijzigen en zijn optioneel, afhankelijk van uw vereisten:

    * **Sku** - U de sku van het openbare IP-adres in de configuratie wijzigen van standaard naar basis- of standaard door de eigenschap **sku-naam** > **name** in het **bestand template.json** te wijzigen:

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

        Zie [Een openbaar IP-adres maken, wijzigen of verwijderen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)voor meer informatie over de verschillen tussen basis- en standaardsku public ips:

    * **Openbare IP-toewijzingsmethode** en **niet-actieve time-out** - U beide opties in de sjabloon wijzigen door de eigenschap **publicIPAllocationMethod** te wijzigen van **Dynamisch** naar **Statisch** of **Statisch** naar **Dynamisch**. De niet-actieve time-out kan worden gewijzigd door de eigenschap **idleTimeoutInMinutes** te wijzigen in het gewenste bedrag.  De standaardinstelling is **4**:

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

        Zie [Een openbaar IP-adres maken, wijzigen of verwijderen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)voor meer informatie over de toewijzingsmethoden en de inactieve time-outwaarden.


13. Klik **op Opslaan** in de online editor.

14. Klik op > **BASICS-abonnement** om het abonnement te kiezen waarbij het doel-openbaar IP wordt geïmplementeerd. **BASICS**

15. Klik op **de** > **groep BASICS Resource** om de resourcegroep te kiezen waar het openbare doel-IP wordt geïmplementeerd.  U op **Nieuw maken** klikken om een nieuwe brongroep voor het openbare doel-IP te maken.  Zorg ervoor dat de naam niet hetzelfde is als de bronbrongroep van het bestaande openbare bron-IP.Ensure the name is niet the same as the source resource group of the existing source public IP.

16. Controleer **basics** > **locatie** is ingesteld op de doellocatie waar u wilt dat de openbare IP worden ingezet.

17. Controleer onder **INSTELLINGEN** of de naam overeenkomt met de naam die u hebt ingevoerd in de bovenstaande parameterseditor.

18. Schakel het selectievakje in onder **ALGEMENE VOORWAARDEN**.

19. Klik **op de** knop Aankoop om het openbare doel-IP te implementeren.

## <a name="discard"></a>Verwijderen

Als u het openbare doel-IP wilt verwijderen, verwijdert u de brongroep die het openbare doel-IP bevat.  Selecteer hiervoor de brongroep in uw dashboard in de portal en selecteer **Verwijderen** boven aan de overzichtspagina.

## <a name="clean-up"></a>Opruimen

Als u de wijzigingen wilt vastleggen en de verplaatsing van het openbare IP-adres wilt voltooien, verwijdert u het openbare ip- of brongroep van de bron. Selecteer hiervoor de openbare IP- of resourcegroep in uw dashboard in de portal en selecteer **Verwijderen** boven aan elke pagina.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure Public IP verplaatst van de ene regio naar de andere en de bronbronnen opgeschoond.  Zie voor meer informatie over het verplaatsen van resources tussen regio's en disaster recovery in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
