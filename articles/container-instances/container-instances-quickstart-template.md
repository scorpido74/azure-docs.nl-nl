---
title: 'Quick Start: Geo-gerepliceerd REGI ster maken-Resource Manager-sjabloon'
description: In deze Quick Start gebruikt u een Azure Resource Manager sjabloon om snel een container web-app te implementeren die in een geïsoleerd Azure-container exemplaar wordt uitgevoerd.
services: azure-resource-manager
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/30/2020
ms.openlocfilehash: 92856f48cb915dd91f132a61e83a2da776db5170
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629908"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-a-resource-manager-template"></a>Snelstartgids: een container exemplaar in azure implementeren met behulp van een resource manager-sjabloon

Gebruik Azure Container Instances om serverloze docker-containers in azure uit te voeren met eenvoud en snelheid. Implementeer een toepassing op een container exemplaar op aanvraag wanneer u geen volledig container Orchestration-platform zoals Azure Kubernetes service nodig hebt.

In deze Snelstartgids gebruikt u een Azure Resource Manager sjabloon om een geïsoleerde docker-container te implementeren en de webtoepassing beschikbaar te maken met een openbaar IP-adres. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Geen.

## <a name="create-a-container-instance"></a>Een containerinstantie maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip)start-sjablonen.

[!code-json[<Azure Resource Manager template create geo-replicated registry>](~/quickstart-templates/101-aci-linuxcontainer-public-ip/azuredeploy.json)]

De volgende resource is gedefinieerd in de sjabloon:

* **[Micro soft. ContainerInstance/containerGroups](/azure/templates/microsoft.containerinstance/containergroups)**: een Azure-container groep maken. Met deze sjabloon wordt een groep gedefinieerd die bestaat uit één container exemplaar.

Meer Azure Container Instances sjabloon voorbeelden vindt u in de Quick Start- [sjabloon galerie](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerinstance&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>De sjabloon implementeren

 1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. De sjabloon maakt een REGI ster en een replica op een andere locatie.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

 2. Typ of selecteer de volgende waarden.

    * **Abonnement**: Selecteer een Azure-abonnement.
    * **Resource groep**: Selecteer **nieuwe maken**, voer een unieke naam in voor de resource groep en selecteer **OK**.
    * **Locatie**: selecteer een locatie voor de resourcegroep. Voor beeld: **VS-centraal**.
    * **Naam**: accepteer de gegenereerde naam voor het exemplaar of voer een naam in.
    * **Afbeelding**: accepteer de standaard naam van de installatie kopie. In deze voorbeeld installatie kopie van Linux wordt een kleine web-app beschreven die is geschreven in node. js, die een statische HTML-pagina vormt. 

    Accepteer de standaard waarden voor de overige eigenschappen.

    Bekijk de voor waarden. Als u akkoord gaat, selecteert u **Ik ga akkoord met de bovenstaande voor waarden**.

    ![Sjabloon eigenschappen](media/container-instances-quickstart-template/template-properties.png)

 3. Nadat het exemplaar is gemaakt, krijgt u een melding:

    ![Portal melding](media/container-instances-quickstart-template/deployment-notification.png)

 De Azure Portal wordt gebruikt voor het implementeren van de sjabloon. Naast de Azure Portal, kunt u de Azure PowerShell, Azure CLI en REST API gebruiken. Zie voor meer informatie over andere implementatie methoden [sjablonen implementeren](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Gebruik de Azure Portal of een hulp programma zoals de [Azure cli](container-instances-quickstart.md) om de eigenschappen van het container exemplaar te controleren.

1. Zoek in de portal naar Container Instances en selecteer het container exemplaar dat u hebt gemaakt.

1. Noteer op de pagina **overzicht** de **status** van het exemplaar en het bijbehorende **IP-adres**.

    ![Exemplaar overzicht](media/container-instances-quickstart-template/aci-overview.png)

2. Nadat de status is *uitgevoerd*, gaat u naar het IP-adres in uw browser. 

    ![App die is geïmplementeerd met Azure Container Instances, weergegeven in de browser](media/container-instances-quickstart-template/view-application-running-in-an-azure-container-instance.png)

### <a name="view-container-logs"></a>Containerlogboeken ophalen

Het weergeven van de logboeken voor een exemplaar van de container is handig bij het oplossen van problemen met de container of de toepassing die wordt uitgevoerd.

Als u de logboeken van de container wilt weer geven, selecteert u onder **instellingen**de optie **containers** > **Logboeken**. De HTTP GET-aanvraag wordt als het goed is gegenereerd wanneer u de toepassing in uw browser hebt bekeken.

![Container-logboeken in Azure Portal](media/container-instances-quickstart-template/aci-logs.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de container, selecteert u op de pagina **overzicht** voor het container exemplaar **verwijderen**. Bevestig de verwijdering als u daarom wordt gevraagd.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een Azure-container exemplaar gemaakt op basis van een open bare micro soft-installatie kopie. Als u zelf een containerinstallatiekopie wilt bouwen en deze wilt implementeren met behulp van een privé Azure Container-register, gaat u verder met de zelfstudie voor Azure Container Instances.

> [!div class="nextstepaction"]
> [Zelf studie: een container installatie kopie maken voor implementatie naar Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Voor een stapsgewijze zelf studie die u door het proces van het maken van een sjabloon leidt, raadpleegt u:

> [!div class="nextstepaction"]
> [Zelf studie: uw eerste Azure Resource Manager sjabloon maken en implementeren](/azure/azure-resource-manager/templates/template-tutorial-create-first-template.md)