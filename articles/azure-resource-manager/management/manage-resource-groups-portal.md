---
title: Resourcegroepen beheren - Azure-portal
description: Gebruik Azure portal om uw brongroepen te beheren via Azure Resource Manager. Hier ziet u hoe u resourcegroepen maakt, opdook en verwijdert.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 622c0cb8d58dddcdb13c7dc9412ada3f2e2d93e4
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632979"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Azure Resource Manager-brongroepen beheren met behulp van de Azure-portal

Meer informatie over het gebruik van de [Azure-portal](https://portal.azure.com) met [Azure Resource Manager](overview.md) om uw Azure-brongroepen te beheren. Zie [Azure-resources beheren met behulp van de Azure-portal](manage-resources-portal.md)voor het beheren van Azure-resources.

Andere artikelen over het beheren van resourcegroepen:

- [Azure-brongroepen beheren met Azure CLI](manage-resources-cli.md)
- [Azure-brongroepen beheren met Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Wat is een resourcegroep

Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. De resourcegroep kan alle resources voor de oplossing bevatten of enkel de resources die u als groep wilt beheren. U bepaalt hoe resources worden toegewezen aan resourcegroepen op basis van wat voor uw organisatie het meest zinvol is. Voeg over het algemeen resources toe die dezelfde levenscyclus delen aan dezelfde resourcegroep, zodat u ze eenvoudig implementeren, bijwerken en verwijderen als groep.

De resourcegroep slaat metagegevens op over de resources. Dat is de reden waarom u moet aangeven waar die metagegevens moeten worden opgeslagen als u een locatie voor de resourcegroep opgeeft. In verband met nalevingsvereisten moet u er mogelijk voor zorgen dat uw gegevens worden opgeslagen in een bepaalde regio.


## <a name="create-resource-groups"></a>Resourcegroepen maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. **Resourcegroepen selecteren**

    ![resourcegroep toevoegen](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Selecteer **Toevoegen**.
4. Voer de volgende waarden in:

   - **Abonnement**: selecteer uw Azure-abonnement. 
   - **Resourcegroep:** voer een nieuwe naam van de resourcegroep in. 
   - **Regio**: Selecteer een Azure-locatie, zoals **Central US**.

     ![resourcegroep maken](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Selecteer **Controleren + Maken**
6. Selecteer **Maken**. Het duurt een paar seconden om een resourcegroep te maken.
7. Selecteer **Vernieuwen** in het bovenste menu om de lijst met brongroepen te vernieuwen en selecteer vervolgens de nieuw gemaakte resourcegroep om deze te openen. Of selecteer **Melding**(het belpictogram) bovenaan en selecteer **Ga naar resourcegroep om** de nieuw gemaakte resourcegroep te openen

    ![naar resourcegroep gaan](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Resourcegroepen aanbieden

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Als u de resourcegroepen wilt weergeven, selecteert u **Resourcegroepen**

    ![bladeren door resourcegroepen](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Als u de weergegeven informatie voor de resourcegroepen wilt aanpassen, selecteert u **Kolommen bewerken**. In de volgende schermafbeelding ziet u de toevoegingskolommen die u aan het scherm toevoegen:

## <a name="open-resource-groups"></a>Brongroepen openen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Resourcegroepen**.
3. Selecteer de resourcegroep die u wilt openen.

## <a name="delete-resource-groups"></a>Resourcegroepen verwijderen

1. Open de resourcegroep die u wilt verwijderen.  Zie [Brongroepen openen](#open-resource-groups).
2. Selecteer **Resourcegroep verwijderen**.

    ![azure-brongroep verwijderen](./media/manage-resource-groups-portal/delete-group.png)

Zie het verwijderen van [azure resource manager-bronnen](delete-resource-group.md)voor meer informatie over hoe Azure Resource Manager de verwijdering van resources bestelt.

## <a name="deploy-resources-to-a-resource-group"></a>Resources implementeren voor een resourcegroep

Nadat u een Resource Manager-sjabloon hebt gemaakt, u de Azure-portal gebruiken om uw Azure-resources te implementeren. Zie [Snelstart: Azure Resource Manager-sjablonen maken en implementeren met behulp van de Azure-portal](../templates/quickstart-create-templates-use-the-portal.md)voor het maken van een sjabloon. Zie Resources implementeren met Resource [Manager-sjablonen en Azure-portal](../templates/deploy-portal.md)voor het implementeren van een sjabloon met de portal.

## <a name="move-to-another-resource-group-or-subscription"></a>Naar een andere resourcegroep of -abonnement gaan

U de resources in de groep verplaatsen naar een andere resourcegroep. Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Brongroepen vergrendelen

Vergrendeling voorkomt dat andere gebruikers in uw organisatie per ongeluk kritieke bronnen verwijderen of wijzigen, zoals Azure-abonnement, resourcegroep of resource. 

1. Open de resourcegroep die u wilt verwijderen.  Zie [Brongroepen openen](#open-resource-groups).
2. Selecteer **Sloten**in het linkerdeelvenster .
3. Als u een vergrendeling wilt toevoegen aan de resourcegroep, selecteert u **Toevoegen**.
4. Voer **de naam Vergrendelen,** **het type Vergrendelen**en Notities **in**. De vergrendelingstypen bevatten **alleen-lezen**en **Verwijderen**.

    ![Azure-brongroep vergrendelen](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Zie [Resources vergrendelen om onverwachte wijzigingen te voorkomen](lock-resources.md)voor meer informatie.

## <a name="tag-resource-groups"></a>Brongroepen taggen

U tags toepassen op resourcegroepen en resources om uw assets logisch te ordenen. Zie Tags [gebruiken om uw Azure-bronnen te ordenen](tag-resources.md#portal)voor meer informatie.

## <a name="export-resource-groups-to-templates"></a>Brongroepen exporteren naar sjablonen

Zie Exporteren naar meerdere [resources naar sjabloon - Portal](../templates/export-template-portal.md)voor informatie over het exporteren van sjablonen.

## <a name="manage-access-to-resource-groups"></a>Toegang tot resourcegroepen beheren

[RBAC (Role-based access control)](../../role-based-access-control/overview.md) is de manier waarop u de toegang tot bronnen in Azure beheert. Zie [Toegang beheren met op rollen gebaseerd toegangsbeheer en Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie Overzicht azure [resource manager](overview.md)voor azure resource manager.
- Zie [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen](../templates/template-syntax.md)voor de syntaxis van de resourcemanagersjabloon .
- Zie de [stapsgewijze zelfstudies](/azure/azure-resource-manager/)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloonverwijzing](/azure/templates/)voor het weergeven van de sjabloonschema's van Azure Resource Manager .
