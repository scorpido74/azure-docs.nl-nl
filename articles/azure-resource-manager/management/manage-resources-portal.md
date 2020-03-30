---
title: Resources beheren - Azure-portal
description: Gebruik de Azure-portal en Azure Resource Manager om uw resources te beheren. Hier ziet u hoe u resources implementeert en verwijdert.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 00d260a74807774d5bf226c3ec00a6b84f93b8d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248344"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Azure-resources beheren met behulp van de Azure-portal

Meer informatie over het gebruik van de [Azure-portal](https://portal.azure.com) met [Azure Resource Manager](overview.md) om uw Azure-bronnen te beheren. Zie [Azure-brongroepen beheren door de Azure-portal](manage-resource-groups-portal.md)te beheren voor het beheren van resourcegroepen.

Andere artikelen over het beheren van resources:

- [Azure-resources beheren met Azure CLI](manage-resources-cli.md)
- [Azure-resources beheren met Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Resources implementeren voor een resourcegroep

Nadat u een Resource Manager-sjabloon hebt gemaakt, u de Azure-portal gebruiken om uw Azure-resources te implementeren. Zie [Snelstart: Azure Resource Manager-sjablonen maken en implementeren met behulp van de Azure-portal](../templates/quickstart-create-templates-use-the-portal.md)voor het maken van een sjabloon. Zie Resources implementeren met Resource [Manager-sjablonen en Azure-portal](../templates/deploy-portal.md)voor het implementeren van een sjabloon met de portal.

## <a name="open-resources"></a>Bronnen openen

Azure-resources worden georganiseerd door Azure-services en door resourcegroepen. De volgende procedures laten zien hoe u een opslagaccount opent met de naam **mystorage0207**. De virtuele machine bevindt zich in een resourcegroep genaamd **mystorage0207rg**.

Ga als lid van het servicetype als u een resource opent op basis van het servicetype:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het linkerdeelvenster de Azure-service. In dit geval **opslagaccounts**.  Als u de service niet ziet, selecteert u **Alle services**en selecteert u het servicetype.

    ![azure-bron openen in de portal](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Selecteer de resource die u wilt openen.

    ![azure-bron openen in de portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Een opslagaccount ziet eruit als volgt:

    ![azure-bron openen in de portal](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Een resource openen op resourcegroep:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **resourcegroepen** in het linkerdeelvenster om de resource in de groep weer te geven.
3. Selecteer de resource die u wilt openen. 

## <a name="manage-resources"></a>Resources beheren

Wanneer u een resource in de portal bekijkt, ziet u de opties voor het beheren van die specifieke bron.

![Azure-bronnen beheren](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

De schermafbeelding toont de beheeropties voor een virtuele Azure-machine. U bewerkingen uitvoeren, zoals het starten, opnieuw starten en stoppen van een virtuele machine.

## <a name="delete-resources"></a>Resources verwijderen

1. Open de bron in de portal. Zie [Resources openen voor](#open-resources)de stappen .
2. Selecteer **Verwijderen**. De volgende schermafbeelding toont de beheeropties voor een virtuele machine.

    ![azure-bron verwijderen](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Typ de naam van de bron om de verwijdering te bevestigen en selecteer **Verwijderen**.

Zie het verwijderen van [azure resource manager-bronnen](delete-resource-group.md)voor meer informatie over hoe Azure Resource Manager de verwijdering van resources bestelt.

## <a name="move-resources"></a>Resources verplaatsen

1. Open de bron in de portal. Zie [Resources openen voor](#open-resources)de stappen .
2. Selecteer **Verplaatsen**selecteren . In de volgende schermafbeelding worden de beheeropties voor een opslagaccount weergegeven.

    ![Azure-bron verplaatsen](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Selecteer **Verplaatsen naar een andere resourcegroep** of Ga naar een ander **abonnement,** afhankelijk van uw behoeften.

Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Resources vergrendelen

Vergrendeling voorkomt dat andere gebruikers in uw organisatie per ongeluk kritieke bronnen verwijderen of wijzigen, zoals Azure-abonnement, resourcegroep of resource. 

1. Open de bron in de portal. Zie [Resources openen voor](#open-resources)de stappen .
2. Selecteer **Sloten**. In de volgende schermafbeelding worden de beheeropties voor een opslagaccount weergegeven.

    ![azure-bron vergrendelen](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Selecteer **Toevoegen**en geef vervolgens de vergrendelingseigenschappen op.

Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Resources taggen

Tagging helpt bij het logisch organiseren van uw resourcegroep en resources. 

1. Open de bron in de portal. Zie [Resources openen voor](#open-resources)de stappen .
2. Selecteer **Tags**. In de volgende schermafbeelding worden de beheeropties voor een opslagaccount weergegeven.

    ![azure-bron taggen](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Geef de tageigenschappen op en selecteer **Opslaan**.

Zie Tags [gebruiken om uw Azure-bronnen te ordenen](tag-resources.md#portal)voor meer informatie.

## <a name="monitor-resources"></a>Resources controleren

Wanneer u een resource opent, worden in de portal standaardgrafieken en tabellen weergegeven voor het bewaken van dat resourcetype. De volgende screenshot toont de grafieken voor een virtuele machine:

![azure-bron controleren](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

U het pictogram van de pin in de rechterbovenhoek van de grafieken selecteren om de grafiek aan het dashboard vast te maken. Zie [Dashboards maken en delen in de Azure-portal](../../azure-portal/azure-portal-dashboards.md)voor meer informatie over het werken met dashboards.

## <a name="manage-access-to-resources"></a>Toegang tot bronnen beheren

[RBAC (Role-based access control)](../../role-based-access-control/overview.md) is de manier waarop u de toegang tot bronnen in Azure beheert. Zie [Toegang beheren met op rollen gebaseerd toegangsbeheer en Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie Overzicht azure [resource manager](overview.md)voor azure resource manager.
- Zie [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen](../templates/template-syntax.md)voor de syntaxis van de resourcemanagersjabloon .
- Zie de [stapsgewijze zelfstudies](/azure/azure-resource-manager/)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloonverwijzing](/azure/templates/)voor het weergeven van de sjabloonschema's van Azure Resource Manager .
