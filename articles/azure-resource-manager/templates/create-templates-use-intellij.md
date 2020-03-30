---
title: Sjabloon implementeren - IntelliJ IDEA
description: Meer informatie over het maken van uw eerste Azure Resource Manager-sjabloon met behulp van het IDEE van IntelliJ en hoe u deze implementeert.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.openlocfilehash: a5015a018f905b4353afd6bf25a48a2d942b3b97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153349"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>ARM-sjablonen maken en implementeren met behulp van het IDEE van IntelliJ

Meer informatie over het implementeren van een Azure Resource Manager-sjabloon (ARM) naar Azure met behulp van het INtelliJ IDEA en het proces van het bewerken en bijwerken van de sjabloon rechtstreeks vanuit de IDE. ARM-sjablonen zijn JSON-bestanden die de resources definiëren die u voor uw oplossing moet implementeren. Zie overzicht van de implementatie van sjablonen voor het implementeren en beheren van de concepten die zijn gekoppeld aan het implementeren en beheren van uw [Azure-oplossingen.](overview.md)

![Resource Manager-sjabloonportaldiagram](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Na het voltooien van de zelfstudie implementeert u een Azure Storage-account. Hetzelfde proces kan worden gebruikt voor het implementeren van andere Azure-resources.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Een [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition of Community Edition geïnstalleerd
* [Azure-toolkit voor IntelliJ](https://plugins.jetbrains.com/plugin/8053) moet zijn geïnstalleerd. Raadpleeg de [beheerhandleiding voor IntelliJ-invoegtoepassingen](https://www.jetbrains.com/help/idea/managing-plugins.html) voor meer informatie
* U moet ook [zijn aangemeld](/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) bij uw Azure-account voor de Azure-toolkit voor IntelliJ

## <a name="deploy-a-quickstart-template"></a>Een Quickstart-sjabloon implementeren

In plaats van een sjabloon helemaal opnieuw te maken, opent u een sjabloon in [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/). Azure Quickstart-sjablonen is een opslagplaats voor ARM-sjablonen. De sjabloon die in dit artikel wordt gebruikt, heet [Een standaardopslagaccount maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Het definieert een Azure Storage-accountbron.

1. Klik met de [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) rechtermuisknop [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) en sla de en op uw lokale computer.

1. Als uw Azure Toolkit correct is geïnstalleerd en aangemeld, ziet u Azure Explorer in de zijbalk van uw IntelliJ IDEA. Klik met de rechtermuisknop op **resourcebeheer** en selecteer **Implementatie maken**.

    ![Klik met de rechtermuisknop op de sjabloon ResourceManager om implementatie te maken](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Config uw **implementatienaam,** **abonnement,** **resourcegroep**en **regio**. Hier implementeren we de sjabloon `testRG`in een nieuwe resourcegroep. Selecteer vervolgens pad voor `azuredeploy.json` **Resourcesjabloon** als `azuredeploy.parameters.json` en **Resourceparameters** terwijl u hebt gedownload.

    ![Resourcebeheersjabloon selecteert bestanden om implementatie te maken](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Nadat u op OK hebt geklikt, wordt de implementatie gestart. Totdat de implementatie is voltooid, u de voortgang vinden van de **statusbalk** van IntelliJ IDEA onderaan.

    ![Status van de implementatiestatus van resourcebeheer-sjabloon](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Bladeren door een bestaande implementatie

1. Nadat de implementatie is uitgevoerd, `testRG` u de nieuwe resourcegroep en een nieuwe implementatie zien die is gemaakt. Klik met de rechtermuisknop op de implementatie en u een lijst met mogelijke acties zien. Selecteer nu **Eigenschappen weergeven**.

    ![Resourcebeheersjabloon bladeren door implementatie](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Er wordt een tabweergave geopend om een aantal nuttige eigenschappen weer te geven, zoals implementatiestatus en sjabloonstructuur.

    ![Resourcemanager-sjabloon toont implementatie-eigenschappen](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Een bestaande implementatie bewerken en bijwerken

1. Selecteer **Implementatie bewerken** in het menu met de rechtermuisknop of de weergave Weergave-eigenschappen weergeven. Een andere tabweergave wordt geopend, met de sjabloon- en parameterbestanden voor de implementatie op Azure. Als u deze bestanden wilt opslaan in lokaal, klikt u op **Sjabloonbestand exporteren** of **Parameterbestanden exporteren**.

    ![Resourcemanager-sjabloon bewerken implementatie](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. U de twee bestanden op deze pagina bewerken en de wijzigingen implementeren in Azure. Hier wijzigen we de waarde van **storageAccountType** in parameterbestanden, van `Standard_LRS` . `Standard_GRS` Klik vervolgens op **Implementatie bijwerken** onderaan en bevestig de update.

    ![Resourcemanager-sjabloon bewerken implementatie](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Nadat de implementatie van de update is voltooid, `Standard_GRS`u op de portal controleren of het gemaakte opslagaccount is gewijzigd in.

## <a name="clean-up-resources"></a>Resources opschonen

1. Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. U dit doen vanuit Azure portal of Azure CLI. Klik in Azure Explorer vanuit IntelliJ IDEA met de rechtermuisknop op de gemaakte **brongroep** en selecteer verwijderen.

    ![Brongroep verwijderen in Azure Explorer vanuit IntelliJ IDEA](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Merk op dat het verwijderen van een implementatie geen bronnen verwijdert die door de implementatie zijn gemaakt. Verwijder de bijbehorende resourcegroep of specifieke bronnen als u deze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

De belangrijkste focus van dit artikel is het gebruik van IntelliJ IDEA om een bestaande sjabloon te implementeren van Azure Quickstart-sjablonen. U hebt ook geleerd hoe u een bestaande implementatie op Azure weergeven en bijwerken. De sjablonen van de Azure-snelstartsjablonen voldoen mogelijk niet volledig aan uw behoeften. Zie onze nieuwe beginnerszelfstudiereeks voor meer informatie over sjabloonontwikkeling:

> [!div class="nextstepaction"]
> [Zelfstudies voor beginners](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Java bezoeken op Azure Dev center](https://docs.microsoft.com/azure/java)
