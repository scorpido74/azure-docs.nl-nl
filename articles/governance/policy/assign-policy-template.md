---
title: 'Quickstart: Nieuwe beleidstoewijzing met sjablonen'
description: In deze quickstart gebruikt u een Resource Manager-sjabloon om een beleidstoewijzing te maken om niet-compatibele resources te identificeren.
ms.date: 05/21/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 7b9e372e8b86e1ac5b0a99d0aba77982896b4d11
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83757446"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Quickstart: Een beleidstoewijzing maken met behulp van een Resource Manager-sjabloon om niet-compatibele resources te identificeren

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources.
In deze quickstart gaat u een beleidstoewijzing maken voor het identificeren van virtuele machines die geen beheerde schijven gebruiken. Als u dit proces helemaal hebt doorlopen, kunt u virtuele machines identificeren die geen beheerde schijven gebruiken. Ze zijn _niet-compatibel_ met de beleidstoewijzing.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze quickstart maakt u een beleidstoewijzing en wijst u een ingebouwde beleidsdefinitie toe met de naam _Controleer virtuele machines die niet gebruikmaken van beheerde schijven_. Zie [Azure Policy-voorbeelden](./samples/index.md) voor een gedeeltelijke lijst met beschikbare ingebouwde beleidsregels.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-30" highlight="20-28":::

De resource die is gedefinieerd in de sjabloon:

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

### <a name="deploy-the-template"></a>De sjabloon implementeren

> [!NOTE]
> Azure Policy-service is gratis. Zie [Overzicht van Azure Policy](./overview.md) voor meer informatie.

1. Selecteer de volgende afbeelding om u aan te melden bij de Azure-portal en open de sjabloon:

   [![De Policy-sjabloon implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Typ of selecteer de volgende waarden:

   | Naam | Waarde |
   |------|-------|
   | Abonnement | Selecteer uw Azure-abonnement. |
   | Resourcegroep | Selecteer **Nieuwe maken**, geef een naam op en selecteer vervolgens **OK**. In de schermopname is de naam van de resource groep _mypolicyquickstart\< en de datum in MMDD \>rg_. |
   | Locatie | Selecteer een regio. Bijvoorbeeld **VS - centraal**. |
   | Naam voor beleidstoewijzing | Geef een naam op voor de beleidstoewijzing. U kunt de weergave voor de beleidsdefinitie gebruiken, als u wilt. Bijvoorbeeld **Controleer virtuele machines die niet gebruikmaken van beheerde schijven**. |
   | Rg-naam | Geef een naam op voor de resourcegroep waaraan u het beleid wilt toewijzen. In deze quickstart gebruikt u de standaardwaarde **[resourceGroup ().name]** . **[resourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** is een sjabloonfunctie waarmee de resourcegroep wordt opgehaald. |
   | Beleidsdefinitie-id | Geef **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a** op. |
   | Ik ga akkoord met de bovenstaande voorwaarden | (Selecteren) |

1. Selecteer **Aankoop**.

Een aantal aanvullende bronnen:

- Zie [Azure Snel Starten-sjabloon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular) voor meer voorbeelden van sjablonen.
- Ga naar [Azure-sjabloonverwijzing](/azure/templates/microsoft.authorization/allversions) om de sjabloonverwijzing te zien.
- Raadpleeg [Azure Resource Manager-documentatie](../../azure-resource-manager/management/overview.md) voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.
- Raadpleeg [Resourcegroepen en resources maken op abonnementsniveau](../../azure-resource-manager/templates/deploy-to-subscription.md) voor informatie over implementatie op abonnementsniveau.

## <a name="validate-the-deployment"></a>De implementatie valideren

Selecteer **Naleving** links op de pagina. Zoek dan de beleidstoewijzing **Virtuele machines zonder beheerde schijven controleren** die u hebt gemaakt.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Overzichtspagina van Policy-naleving" border="false":::

Als er bestaande resources zijn die niet conform deze nieuwe toewijzing zijn, worden deze weergegeven bij **Niet-conforme resources**.

Zie [Hoe naleving werkt](./how-to/get-compliance-data.md#how-compliance-works) voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gemaakte toewijzing wilt verwijderen, volgt u deze stappen:

1. Selecteer **Naleving** (of **Toewijzingen**) aan de linkerkant van de pagina Azure Policy en zoek de beleidstoewijzing **Controleren van virtuele machines die geen beheerde schijven gebruiken** die u hebt gemaakt.

1. Klik met de rechtermuisknop op de beleidstoewijzing **Controleer virtuele machines die niet gebruikmaken van beheerde schijven** en selecteer **Toewijzing verwijderen**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Een toewijzing verwijderen van de overzichtspagina voor naleving" border="false":::

## <a name="next-steps"></a>Volgende stappen

In deze quickstart wijst u een ingebouwde beleidsdefinitie toe aan een bereik en evalueert u het rapport voor naleving. De beleidsdefinitie controleert of alle resources in het bereik conform zijn en identificeert welke dit niet zijn.

Ga voor meer informatie over het toewijzen van beleid om te controleren of nieuwe resources conform zijn verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)