---
title: 'Snelstartgids: nieuwe beleids toewijzing met sjablonen'
description: In deze Quick Start gebruikt u een resource manager-sjabloon om een beleids toewijzing te maken om niet-compatibele resources te identificeren.
ms.date: 03/16/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 646e2e064618e70fd9e9e747926f0886d85fde26
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82187780"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Snelstartgids: een beleids toewijzing maken om niet-compatibele resources te identificeren met behulp van een resource manager-sjabloon

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources.
In deze quickstart gaat u een beleidstoewijzing maken voor het identificeren van virtuele machines die geen beheerde schijven gebruiken. Als u dit proces helemaal hebt doorlopen, kunt u virtuele machines identificeren die geen beheerde schijven gebruiken. Ze zijn _niet-compatibel_ met de beleidstoewijzing.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze Quick Start maakt u een beleids toewijzing en wijst u een ingebouwde beleids definitie toe met de naam _audit vm's die geen beheerde schijven gebruiken_. Zie Azure Policy-voor [beelden](./samples/index.md)voor een gedeeltelijke lijst met beschik bare ingebouwde beleids regels.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/)start-sjablonen.

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-36" highlight="26-34":::

De resource die in de sjabloon is gedefinieerd, is:

- [Micro soft. Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

### <a name="deploy-the-template"></a>De sjabloon implementeren

> [!NOTE]
> Azure Policy-service is gratis. Zie [overzicht van Azure Policy](./overview.md)voor meer informatie.

1. Selecteer de volgende afbeelding om u aan te melden bij de Azure-portal en open de sjabloon:

   [![De beleids sjabloon implementeren in azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Selecteer of voer de volgende waarden in:

   | Naam | Waarde |
   |------|-------|
   | Abonnement | Selecteer uw Azure-abonnement. |
   | Resourcegroep | Selecteer **nieuwe maken**, geef een naam op en selecteer **OK**. In de scherm opname is de naam van de resource groep _mypolicyquickstart\<datum\>in MMDD RG_. |
   | Locatie | Selecteer een regio. Bijvoorbeeld **VS - centraal**. |
   | Naam van beleids toewijzing | Geef een naam op voor het toewijzings beleid. U kunt de beleids definitie weer geven als u wilt. U kunt bijvoorbeeld **Vm's controleren die geen beheerde schijven gebruiken**. |
   | RG naam | Geef een naam op voor de resource groep waaraan u het beleid wilt toewijzen. In deze Quick Start gebruikt u de standaard waarde **[resourceGroup (). naam]**. **[resourceGroup ()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** is een sjabloon functie die de resource groep ophaalt. |
   | Beleids definitie-ID | Geef **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**op. |
   | Ik ga akkoord met de bovenstaande voor waarden | Uitgeschakeld |

1. Selecteer **Aankoop**.

Enkele aanvullende bronnen:

- Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)start-sjabloon voor meer voor beelden van sjablonen.
- Voor een overzicht van de sjabloon verwijzing gaat u naar de [Naslag informatie voor Azure-sjablonen](/azure/templates/microsoft.authorization/allversions).
- Zie [Azure Resource Manager-documentatie](../../azure-resource-manager/management/overview.md)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.
- Zie [resource groepen en-resources op abonnements niveau maken](../../azure-resource-manager/templates/deploy-to-subscription.md)voor meer informatie over implementatie op abonnements niveau.

## <a name="validate-the-deployment"></a>De implementatie valideren

Selecteer **Naleving** links op de pagina. Zoek dan de beleidstoewijzing **Virtuele machines zonder beheerde schijven controleren** die u hebt gemaakt.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Overzichts pagina beleids naleving" border="false":::

Als er bestaande resources zijn die niet conform deze nieuwe toewijzing zijn, worden deze weergegeven bij **Niet-conforme resources**.

Zie [hoe naleving werkt](./how-to/get-compliance-data.md#how-compliance-works)voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gemaakte toewijzing wilt verwijderen, volgt u deze stappen:

1. Selecteer **Naleving** (of **Toewijzingen**) aan de linkerkant van de pagina Azure Policy en zoek de beleidstoewijzing **Controleren van virtuele machines die geen beheerde schijven gebruiken** die u hebt gemaakt.

1. Klik met de rechter muisknop op de **virtuele machines controleren die geen gebruikmaken** van beleids toewijzing voor beheerde schijven en selecteer **toewijzing verwijderen**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Een toewijzing verwijderen op de pagina nalevings overzicht" border="false":::

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een ingebouwde beleids definitie toegewezen aan een bereik en het nalevings rapport geëvalueerd. De beleidsdefinitie controleert of alle resources in het bereik conform zijn en identificeert welke dit niet zijn.

Ga voor meer informatie over het toewijzen van beleid om te controleren of nieuwe resources conform zijn verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)