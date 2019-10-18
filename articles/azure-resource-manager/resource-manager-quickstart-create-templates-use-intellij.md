---
title: Azure Resource Manager-sjabloon implementeren-IntelliJ-idee
description: Meer informatie over het maken van uw eerste Azure Resource Manager-sjabloon met behulp van het IntelliJ-idee en hoe u deze implementeert.
services: azure-resource-manager
documentationcenter: ''
author: yucwan
manager: ''
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: java
ms.date: 08/01/2019
ms.topic: quickstart
ms.author: yucwan
ms.openlocfilehash: 137a87ad0314fad08f9af0eb567e1d31032ddcb9
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533655"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-intellij-idea"></a>Snelstartgids: Azure Resource Manager sjablonen maken en implementeren met behulp van het IntelliJ-idee

Informatie over het implementeren van een resource manager-sjabloon in azure met behulp van het IntelliJ-idee en het proces voor het bewerken en bijwerken van de sjabloon rechtstreeks vanuit de IDE. Resource Manager-sjablonen zijn JSON-bestanden die de resources definiëren die u voor uw oplossing moet implementeren. Zie [Overzicht van Azure Resource Manager](resource-group-overview.md) voor inzicht in de concepten die gerelateerd zijn aan het implementeren en beheren van uw Azure-oplossingen.

![Diagram van de Snelstartgids voor de Resource Manager-sjabloon](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Na het voltooien van de zelfstudie implementeert u een Azure Storage-account. Hetzelfde proces kan worden gebruikt voor het implementeren van andere Azure-resources.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Een [IntelliJ-idee](https://www.jetbrains.com/idea/download/) Ultimate-editie of Community-editie is geïnstalleerd
* De [Azure-Toolkit voor IntelliJ](https://plugins.jetbrains.com/plugin/8053) is geïnstalleerd, Raadpleeg de [IntelliJ-hand leiding voor het beheer van invoeg toepassingen](https://www.jetbrains.com/help/idea/managing-plugins.html) voor meer informatie
* [Meld](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) u aan bij uw Azure-account voor de Azure-Toolkit voor IntelliJ

## <a name="deploy-a-quickstart-template"></a>Een Quick Start-sjabloon implementeren

In plaats van een sjabloon helemaal opnieuw te maken, opent u een sjabloon in [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/). Azure-snelstartsjablonen is een opslagplaats voor Resource Manager-sjablonen. De in deze snelstart gebruikte sjabloon wordt [Create a standard storage account](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/) (Standaardopslagaccount maken) genoemd. Hiermee wordt een Azure Storage-account resource gedefinieerd.

1. Klik met de rechter muisknop en sla [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) en [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) op uw lokale computer op.

1. Als uw Azure-Toolkit correct is geïnstalleerd en aangemeld, ziet u Azure Explorer in de zijbalk van uw IntelliJ-idee. Klik met de rechter muisknop op het **resource beheer** en selecteer **implementatie maken**.

    ![Resource Manager-sjabloon met de rechter muisknop om implementatie te maken](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Configuratie van de **implementatie naam**, het **abonnement**, de **resource groep**en de **regio**. Hier implementeren we de sjabloon in een nieuwe resource groep `testRG`. Selecteer vervolgens pad voor **resource sjabloon** als `azuredeploy.json`-en **Resource parameters** als `azuredeploy.parameters.json` die u hebt gedownload.

    ![Resource Manager-sjabloon bestanden selecteren om implementatie te maken](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Nadat u op OK hebt geklikt, wordt de implementatie gestart. Totdat de implementatie is voltooid, kunt u de voortgang van de **status balk** van het INTELLIJ-idee aan de onderkant vinden.

    ![Implementatie status van Resource Manager-sjabloon](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Door een bestaande implementatie bladeren

1. Nadat de implementatie is voltooid, ziet u de nieuwe resource groep `testRG` en wordt er een nieuwe implementatie gemaakt. Klik met de rechter muisknop op de implementatie om een lijst met mogelijke acties weer te geven. Selecteer nu **Eigenschappen weer geven**.

    ![Resource Manager-sjabloon bladeren implementatie](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Er wordt een tabblad weergave geopend om enkele nuttige eigenschappen, zoals de implementatie status en sjabloon structuur, weer te geven.

    ![Resource Manager-sjabloon implementatie-eigenschappen weer geven](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Een bestaande implementatie bewerken en bijwerken

1. Selecteer **implementatie bewerken** in het menu met de rechter muisknop of de weer gave eigenschappen weer geven voor. Er wordt nog een andere tabblad weergave geopend, met daarin de sjabloon en de parameter bestanden voor de implementatie op Azure. Als u deze bestanden op een lokale locatie wilt opslaan, kunt u klikken op **sjabloon bestand exporteren** of **parameter bestanden exporteren**.

    ![Resource Manager-sjabloon implementatie bewerken](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment.png)

1. U kunt de twee bestanden op deze pagina bewerken en de wijzigingen in azure implementeren. Hier wijzigen we de waarde van **storageAccountType** in parameter bestanden, van `Standard_LRS` naar `Standard_GRS`. Klik op **Update-implementatie** onder en bevestig de update.

    ![Resource Manager-sjabloon implementatie bewerken](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Nadat de update-implementatie is voltooid, kunt u op de portal controleren of het gemaakte opslag account is gewijzigd in `Standard_GRS`.

## <a name="clean-up-resources"></a>Resources opschonen

1. Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. U kunt dit doen vanuit Azure Portal of Azure CLI. In azure Verkenner van IntelliJ idee met de rechter muisknop op de gemaakte **resource groep** en selecteer verwijderen.

    ![De resource groep in azure Verkenner verwijderen uit het IntelliJ-idee](./media/resource-manager-quickstart-create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> U ziet dat bij het verwijderen van een implementatie geen resources worden verwijderd die zijn gemaakt met de implementatie. Verwijder de bijbehorende resource groep of specifieke resources als u deze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

De belangrijkste focus van deze Snelstartgids is het gebruik van IntelliJ-idee voor het implementeren van een bestaande sjabloon vanuit Azure Quick Start-sjablonen. U hebt ook geleerd hoe u een bestaande implementatie op Azure weergeeft en bijwerkt. De sjablonen van de Azure-snelstartsjablonen voldoen mogelijk niet volledig aan uw behoeften. Zie voor meer informatie over het ontwikkelen van sjablonen onze nieuwe zelf studie reeks voor beginners:

> [!div class="nextstepaction"]
> [Zelf studies voor beginners](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Ga naar java in het ontwikkel centrum van Azure](https://docs.microsoft.com/azure/java)
