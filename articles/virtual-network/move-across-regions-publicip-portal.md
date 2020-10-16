---
title: De open bare IP-configuratie van Azure verplaatsen naar een andere Azure-regio Azure Portal
description: Gebruik een sjabloon om de open bare IP-configuratie van Azure van de ene Azure-regio naar de andere te verplaatsen met behulp van de Azure Portal.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 23fe515ddfdecb9ef168dd662e3fa2d91ece688f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711473"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>De open bare IP-configuratie van Azure naar een andere regio verplaatsen met behulp van de Azure Portal

Er zijn verschillende scenario's waarin u uw bestaande open bare IP-configuraties van Azure naar een andere regio wilt verplaatsen. U kunt bijvoorbeeld een openbaar IP-adres maken met dezelfde configuratie en SKU voor testen. Het is ook mogelijk dat u een open bare IP-configuratie wilt verplaatsen naar een andere regio als onderdeel van de planning voor nood herstel.

**Open bare Azure-Ip's zijn regio specifiek en kunnen niet worden verplaatst van de ene regio naar de andere.** U kunt echter een Azure Resource Manager sjabloon gebruiken om de bestaande configuratie van een openbaar IP-adres te exporteren.  U kunt de resource vervolgens in een andere regio zetten door het open bare IP-adres naar een sjabloon te exporteren, de para meters te wijzigen zodat deze overeenkomen met de doel regio en vervolgens de sjabloon te implementeren in de nieuwe regio.  Voor meer informatie over Resource Manager en sjablonen raadpleegt [u Quick Start: Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat het open bare IP-adres van Azure zich bevindt in de Azure-regio van waaruit u wilt verplaatsen.

- Open bare Azure-Ip's kunnen niet tussen regio's worden verplaatst.  U moet het nieuwe open bare IP-adres koppelen aan resources in de doel regio.

- Als u een open bare IP-configuratie wilt exporteren en een sjabloon wilt implementeren voor het maken van een openbaar IP-adres in een andere regio, hebt u de rol netwerk bijdrager of hoger nodig.

- Identificeer de bronnetwerkindeling en alle resources die u momenteel gebruikt. Deze indeling bevat, maar is niet beperkt tot load balancers, netwerk beveiligings groepen (Nsg's) en virtuele netwerken.

- Controleer of u met uw Azure-abonnement open bare Ip's kunt maken in de doel regio die wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft ter ondersteuning van het toevoegen van open bare IP-adressen voor dit proces.  Raadpleeg [Azure-abonnement en -servicelimieten, quotums en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
De volgende stappen laten zien hoe u het open bare IP-adres voor de configuratie verplaatsing kunt voorbereiden met een resource manager-sjabloon en de open bare IP-configuratie naar de doel regio kunt verplaatsen met behulp van de Azure Portal.

### <a name="export-the-template-and-deploy-from-a-script"></a>De sjabloon exporteren en implementeren vanuit een script

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)-  >  **resource groepen**.
2. Zoek de resource groep die de open bare bron-IP bevat en klik erop.
3. Selecteer > **instellingen**  >  **sjabloon exporteren**.
4. Kies **implementeren** op de Blade **sjabloon exporteren** .
5. Klik op **sjabloon**  >  **bewerken para meters** om de **parameters.jsin** het bestand in de online-editor te openen.
8. Als u de para meter van de open bare IP-naam wilt bewerken, wijzigt u de eigenschap onder **parameter**  >  **waarde** van de naam van het open bare IP-bron adres in de naam van het open bare IP-adres. Controleer of de naam tussen aanhalings tekens is:

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
8.  Klik op **Opslaan** in de editor.

9.  Klik **op sjabloon**  >  **bewerken sjabloon** om de **template.js** te openen in het bestand in de online-editor.

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

11. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van regio-locatie codes.  De code voor een regio is de naam van de regio zonder spaties, **Central VS**-  =  **Midden**.

12. U kunt ook andere para meters in de sjabloon wijzigen als u ervoor kiest en zijn optioneel, afhankelijk van uw vereisten:

    * **SKU** : u kunt de SKU van het open bare IP-adres in de configuratie wijzigen van standaard in Basic of Basic naar Standard door de eigenschap **SKU**  >  **name** te wijzigen in de **template.jsin** het bestand:

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

    * **Toewijzings methode voor openbaar IP-adres** en **time-out voor inactiviteit** : u kunt beide opties in de sjabloon wijzigen door de eigenschap **publicIPAllocationMethod** van **dynamisch** naar **statisch** of **statisch** naar **dynamisch**te veranderen. U kunt de time-out voor inactiviteit wijzigen door de eigenschap **idleTimeoutInMinutes** te wijzigen in de gewenste hoeveelheid.  De standaard waarde is **4**:

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

14. Klik op **basis**  >  **abonnement** om het abonnement te kiezen waarin het open bare doel-IP-adres wordt geïmplementeerd.

15. Klik op **basis**  >  **bronnen groep** om de resource groep te kiezen waarin het open bare doel-IP-adres wordt geïmplementeerd.  U kunt op **Nieuw maken** klikken om een nieuwe resource groep te maken voor het open bare doel-IP-adres.  Zorg ervoor dat de naam niet hetzelfde is als de bron resource groep van de bestaande open bare bron-IP.

16. De **BASICS**  >  **locatie** van de basis beginselen controleren is ingesteld op de doel locatie waar u het open bare IP-adres wilt implementeren.

17. Controleer onder **instellingen** of de naam overeenkomt met de naam die u hebt ingevoerd in de bovenstaande para meters-editor.

18. Schakel het selectie vakje onder **voor waarden**in.

19. Klik op de knop **aanschaffen** om het doel-open bare IP-adres te implementeren.

## <a name="discard"></a>Verwijderen

Als u het doel bare IP-adres wilt verwijderen, verwijdert u de resource groep die het open bare doel-IP-adres bevat.  Hiervoor selecteert u de resource groep in het dash board in de portal en selecteert u **verwijderen** boven aan de pagina overzicht.

## <a name="clean-up"></a>Opschonen

Als u de wijzigingen wilt door voeren en de open bare IP wilt verplaatsen, verwijdert u de open bare IP-bron of resource groep. Hiertoe selecteert u het open bare IP-adres of de resource groep in het dash board in de portal en selecteert u **verwijderen** boven aan elke pagina.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een open bare Azure-IP van de ene regio naar de andere verplaatst en worden de bron bronnen opgeruimd.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Virtuele Azure-machines verplaatsen naar een andere regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
