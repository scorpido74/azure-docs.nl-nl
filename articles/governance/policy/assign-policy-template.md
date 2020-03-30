---
title: 'Snelstart: nieuwe beleidstoewijzing met sjablonen'
description: In deze snelstart gebruikt u een resourcemanagersjabloon om een beleidstoewijzing te maken om niet-conforme resources te identificeren.
ms.date: 03/16/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 9f9998c407c39d11615a5997549a363a276b9e10
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79471401"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Snelstart: een beleidstoewijzing maken om niet-compatibele resources te identificeren met behulp van een resourcemanagersjabloon

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources.
In deze quickstart gaat u een beleidstoewijzing maken voor het identificeren van virtuele machines die geen beheerde schijven gebruiken. Als u dit proces helemaal hebt doorlopen, kunt u virtuele machines identificeren die geen beheerde schijven gebruiken. Ze zijn _niet-compatibel_ met de beleidstoewijzing.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze snelstart maakt u een beleidstoewijzing en wijst u een ingebouwde beleidsdefinitie toe, _de zogenaamde Audit VM's, die geen beheerde schijven gebruiken._ Zie [Azure Policy-voorbeelden](./samples/index.md)voor een gedeeltelijke lijst met beschikbare ingebouwde beleidsregels .

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/)

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-36" highlight="26-34":::

De resource die in de sjabloon is gedefinieerd, is:

- [Microsoft.Autorisatie/policyToewijzingen](/azure/templates/microsoft.authorization/policyassignments)

### <a name="deploy-the-template"></a>De sjabloon implementeren

> [!NOTE]
> Azure Policy-service is gratis. Zie [Overzicht van Azure-beleid](./overview.md)voor meer informatie .

1. Selecteer de volgende afbeelding om u aan te melden bij de Azure-portal en open de sjabloon:

   [![De beleidssjabloon implementeren in Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Selecteer of voer de volgende waarden in:

   | Name | Waarde |
   |------|-------|
   | Abonnement | Selecteer uw Azure-abonnement. |
   | Resourcegroep | Selecteer **Nieuw maken,** geef een naam op en selecteer **OK**. In de schermafbeelding is de naam van de resourcegroep _mypolicyquickstartdatum\<in\>MMDD rg_. |
   | Locatie | Selecteer een regio. Bijvoorbeeld **VS - centraal**. |
   | Naam beleidstoewijzing | Geef een naam van een beleidstoewijzing op. U desgevraagd desgevraagd de scan weergeven van de beleidsdefinitie gebruiken. Controleer bijvoorbeeld **VM's die geen beheerde schijven gebruiken.** |
   | Rg Naam | Geef een naam van de resourcegroep op waaraan u het beleid wilt toewijzen. Gebruik in deze snelstart de standaardwaarde **[resourceGroup().name]**. **[resourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** is een sjabloonfunctie waarmee de resourcegroep wordt opgehaald. |
   | Beleidsdefinitie-id | Geef **/providers/Microsoft.Authorization/policyDefinities/0a914e76-4921-4c19-b460-a2d36003525a**op . |
   | Ik ga akkoord met de hierboven genoemde algemene voorwaarden | (Selecteren) |

1. Selecteer **Aankoop**.

Enkele extra bronnen:

- Zie [Azure Quickstart-sjabloon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)voor meer voorbeeldsjablonen.
- Als u de verwijzing naar de sjabloon wilt bekijken, gaat u naar [Azure-sjabloonverwijzing](/azure/templates/microsoft.authorization/allversions).
- Zie [Azure Resource Manager-documentatie](../../azure-resource-manager/management/overview.md)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.
- Zie [Resourcegroepen en resources maken op abonnementsniveau](../../azure-resource-manager/templates/deploy-to-subscription.md)voor meer informatie over implementatie op abonnementsniveau.

## <a name="validate-the-deployment"></a>De implementatie valideren

Selecteer **Naleving** links op de pagina. Zoek dan de beleidstoewijzing **Virtuele machines zonder beheerde schijven controleren** die u hebt gemaakt.

![Overzichtspagina voor naleving van beleid](./media/assign-policy-template/policy-compliance.png)

Als er bestaande resources zijn die niet conform deze nieuwe toewijzing zijn, worden deze weergegeven bij **Niet-conforme resources**.

Zie [Hoe compliance werkt](./how-to/get-compliance-data.md#how-compliance-works)voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gemaakte toewijzing wilt verwijderen, volgt u deze stappen:

1. Selecteer **Naleving** (of **Toewijzingen**) aan de linkerkant van de pagina Azure Policy en zoek de beleidstoewijzing **Controleren van virtuele machines die geen beheerde schijven gebruiken** die u hebt gemaakt.

1. Klik met de rechtermuisknop op **de beleidstoewijzing voor beheerde schijven en** selecteer Toewijzing **verwijderen**.

   ![Een toewijzing verwijderen van de pagina nalevingsoverzicht](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelle start hebt u een ingebouwde beleidsdefinitie toegewezen aan een scope en het nalevingsrapport geëvalueerd. De beleidsdefinitie controleert of alle resources in het bereik conform zijn en identificeert welke dit niet zijn.

Ga voor meer informatie over het toewijzen van beleid om te controleren of nieuwe resources conform zijn verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)