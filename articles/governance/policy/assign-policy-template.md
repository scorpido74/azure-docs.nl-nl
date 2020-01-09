---
title: 'Snelstartgids: nieuwe beleids toewijzing met sjablonen'
description: In deze Quick Start gebruikt u een resource manager-sjabloon om een beleids toewijzing te maken om niet-compatibele resources te identificeren.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: e22c14768622408fb3afb0e491d4179b6113e4ca
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436456"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Snelstartgids: een beleids toewijzing maken om niet-compatibele resources te identificeren met behulp van een resource manager-sjabloon

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources.
In deze quickstart gaat u een beleidstoewijzing maken voor het identificeren van virtuele machines die geen beheerde schijven gebruiken.

Als u dit proces helemaal hebt doorlopen, kunt u virtuele machines identificeren die geen beheerde schijven gebruiken. Ze zijn _niet-compatibel_ met de beleidstoewijzing.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze Quick Start maakt u een beleids toewijzing en wijst u een ingebouwde beleids definitie toe met de naam _audit vm's die geen beheerde schijven gebruiken_. Zie Azure Policy-voor [beelden](./samples/index.md)voor een gedeeltelijke lijst met beschik bare ingebouwde beleids regels.

Er zijn verschillende methoden voor het maken van beleids toewijzingen. In deze Snelstartgids gebruikt u een Quick Start- [sjabloon](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Hier volgt een kopie van de sjabloon:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Azure Policy-service is gratis. Zie [overzicht van Azure Policy](./overview.md)voor meer informatie.

1. Selecteer de volgende afbeelding om u aan te melden bij de Azure Portal en open de sjabloon:

   [de beleids sjabloon ![implementeren in azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Selecteer of voer de volgende waarden in:

   | Name | Waarde |
   |------|-------|
   | Abonnement | Selecteer uw Azure-abonnement. |
   | Resourcegroep | Selecteer **nieuwe maken**, geef een naam op en selecteer **OK**. In de scherm opname is de naam van de resource groep _mypolicyquickstart\<datum in MMDD\>RG_. |
   | Locatie | Selecteer een regio. Bijvoorbeeld **US - centraal**. |
   | Naam van beleids toewijzing | Geef een naam op voor het toewijzings beleid. U kunt de beleids definitie weer geven als u wilt. U kunt bijvoorbeeld **Vm's controleren die geen beheerde schijven gebruiken**. |
   | RG naam | Geef een naam op voor de resource groep waaraan u het beleid wilt toewijzen. In deze Quick Start gebruikt u de standaard waarde **[resourceGroup (). naam]** . **[resourceGroup ()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** is een sjabloon functie die de resource groep ophaalt. |
   | Beleids definitie-ID | Geef **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**op. |
   | Ik ga akkoord met de bovenstaande voor waarden | Uitgeschakeld |

1. Selecteer **Aankoop**.

Enkele aanvullende bronnen:

- Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)start-sjabloon voor meer voor beelden van sjablonen.
- Voor een overzicht van de sjabloon verwijzing gaat u naar de [Naslag informatie voor Azure-sjablonen](/azure/templates/microsoft.authorization/allversions).
- Zie [Azure Resource Manager-documentatie](../../azure-resource-manager/management/overview.md)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.
- Zie [resource groepen en-resources op abonnements niveau maken](../../azure-resource-manager/deploy-to-subscription.md)voor meer informatie over implementatie op abonnements niveau.

## <a name="identify-non-compliant-resources"></a>Niet-compatibele resources identificeren

Selecteer **Naleving** links op de pagina. Zoek dan de beleidstoewijzing **Virtuele machines zonder beheerde schijven controleren** die u hebt gemaakt.

![Overzichts pagina beleids naleving](./media/assign-policy-template/policy-compliance.png)

Als er bestaande resources zijn die niet conform deze nieuwe toewijzing zijn, worden deze weergegeven bij **Niet-conforme resources**.

Zie [hoe naleving werkt](./how-to/get-compliance-data.md#how-compliance-works)voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gemaakte toewijzing wilt verwijderen, volgt u deze stappen:

1. Selecteer **Naleving** (of **Toewijzingen**) aan de linkerkant van de pagina Azure Policy en zoek de beleidstoewijzing **Controleren van virtuele machines die geen beheerde schijven gebruiken** die u hebt gemaakt.

1. Klik met de rechter muisknop op de **virtuele machines controleren die geen gebruikmaken** van beleids toewijzing voor beheerde schijven en selecteer **toewijzing verwijderen**.

   ![Een toewijzing verwijderen op de pagina nalevings overzicht](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een ingebouwde beleids definitie toegewezen aan een bereik en het nalevings rapport geëvalueerd. De beleidsdefinitie controleert of alle resources in het bereik conform zijn en identificeert welke dit niet zijn.

Ga voor meer informatie over het toewijzen van beleid om te controleren of nieuwe resources conform zijn verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)