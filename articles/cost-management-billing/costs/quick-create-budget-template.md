---
title: 'Quickstart: een budget maken met een Azure Resource Manager-sjabloon'
description: Deze quickstart laat zien hoe u een budget kunt maken met een Azure Resource Manager-sjabloon.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: subject-armqs
ms.openlocfilehash: 88dd1dc99f32d0539718c0f71206176cbfc16eec
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830307"
---
# <a name="quickstart-create-a-budget-with-an-azure-resource-manager-template"></a>Quickstart: Een budget maken met een Azure Resource Manager-sjabloon

Met budgetten in Cost Management kunt u de verantwoordelijkheid in de organisatie beter plannen en stimuleren. Met budgetten kunt u de Azure-services die u gebruikt of waarop u zich abonneert voor een specifieke periode bijhouden. Ze helpen u anderen te informeren over hun uitgaven om kosten proactief te beheren en uitgaven in de loop van de tijd bij te houden. Wanneer de budgetdrempels die u hebt gemaakt, worden overschreden, worden er meldingen geactiveerd. Dit heeft geen gevolgen voor uw resources en uw gebruik wordt niet gestopt. U kunt budgetten gebruiken om bestedingen te vergelijken en bij te houden bij het analyseren van kosten. Deze quickstart laat zien hoe u een budget kunt maken met een Azure Resource Manager-sjabloon.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

De Azure Resource Manager-sjabloon ondersteunt alleen Azure-abonnementen voor Enterprise Agreements (EA). Andere typen abonnementen worden niet ondersteund door de sjabloon.

Als u budgetten wilt maken en beheren, moet u de machtiging Inzender hebben. U kunt afzonderlijke budgetten voor EA-abonnementen en resourcegroepen maken. U kunt echter geen budgetten voor EA-factureringsrekeningen maken. Voor Azure EA-abonnementen moet u leestoegang hebben om budgetten weer te geven.

Nadat een budget is gemaakt, hebt u minimaal leestoegang voor uw Azure-account nodig als u budgetten wilt weergeven.

Als u een nieuw abonnement hebt, kunt u niet direct een budget maken of andere Cost Management-functies gebruiken. Het kan tot 48 uur duren voordat u alle Cost Management-functies kunt gebruiken.

In abonnementen worden de volgende Azure-machtigingen, of bereiken, ondersteund voor budgetten voor gebruikers en groepen. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.

- Eigenaar: eigenaren kunnen budgetten voor een abonnement instellen, wijzigen en verwijderen.
- Inzender en Inzender Cost Management: inzenders kunnen hun eigen budgetten instellen, wijzigen en verwijderen. Inzenders kunnen ook de budgetbedragen aanpassen voor budgetten die zijn gemaakt door anderen.
- Lezer en Lezer Cost Management: lezers kunnen budgetten inzien waarvoor ze zijn gemachtigd.

Zie [Toegang tot gegevens van Cost Management toewijzen](assign-access-acm-data.md) voor meer informatie over het toewijzen van toegang tot de gegevens van Cost Management.

## <a name="create-a-budget"></a>Een budget maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" range="1-146" highlight="110-139":::

Er is één Azure-resource gedefinieerd in de sjabloon:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Een Azure-budget maken.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon wordt een budget gemaakt.

   [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden.

   [![Resource Manager-sjabloon, budget maken, portal implementeren](./media/quick-create-budget-template/create-budget-using-template-portal.png)](./media/quick-create-budget-template/create-budget-using-template-portal.png#lightbox)

    * **Abonnement**: selecteer een Azure-abonnement.
    * **Resourcegroep**: selecteer **Nieuwe maken**, geef een unieke naam op voor de nieuwe resourcegroep en klik vervolgens op **OK**, of selecteer een bestaande resourcegroep.
    * **Locatie**: selecteer een locatie. Bijvoorbeeld **VS - centraal**.
    * **Budgetnaam**: voer een naam in voor het budget. Deze moet uniek zijn binnen de resourcegroep. Alleen alfanumerieke tekens, onderstrepingstekens en streepjes zijn toegestaan.
    * **Hoeveelheid**: voer de totale kosten of het gebruik in dat met behulp van het budget moet worden bijgehouden.
    * **Budgetcategorie**: selecteer de categorie van het budget (of het budget **Kosten** of **Gebruik** bijhoudt).
    * **Tijdsinterval**: voer de periode in die het budget dekt. Toegestane waarden zijn maandelijks, per kwartaal of per jaar. Het budget wordt aan het einde van de tijdsinterval opnieuw ingesteld.
    * **Begindatum**: voer de begindatum in, uitgaande van de eerste dag van de maand, in de notatie JJJJ-MM-DD. Een toekomstige begindatum mag niet meer dan drie maanden in de toekomstig liggen vanaf vandaag. U kunt een eerdere begindatum opgeven met de periode van de tijdsinterval.
    * **Einddatum**: Geef de einddatum voor het budget op in de notatie JJJJ-MM-DD. Als dat niet is opgegeven, wordt dit standaard ingesteld op 10 jaar na de begindatum.
    * **Operator**: selecteer een vergelijkingsoperator. Mogelijke waarden zijn EqualTo, GreaterThan en GreaterThanOrEqualTo.
    * **Drempelwaarde**: voer een drempelwaarde in voor de melding. Er wordt een melding verzonden wanneer de kosten de drempelwaarde overschrijden. Dit is altijd een percentage van 0 tot en met 1000.
    * **Contact-e-mails** voer een lijst met e-mailadressen in waarnaar de budgetmelding moet worden verzonden wanneer de drempelwaarde wordt overschreden. De verwachte indeling is `["user1@domain.com","user2@domain.com"]`.
    * **Contactrollen**: voer een lijst met contactrollen in waarnaar de budgetmelding moet worden verzonden wanneer de drempelwaarde wordt overschreden. Standaardwaarden zijn Eigenaar, Inzender en Lezer. De verwachte indeling is `["Owner","Contributor","Reader"]`.
    * **Contactgroepen**: voer een lijst met actiegroepen in waarnaar de budgetmelding moet worden verzonden wanneer de drempelwaarde wordt overschreden. Dit veld accepteert een matrix van tekenreeksen. De verwachte indeling is `["Action Group Name1","Action Group Name2"]`. Als u geen actiegroepen wilt gebruiken, voert u `[]` in.
    * **Resourcefilter**: voer een lijst met filters voor resources in. De verwachte indeling is `["Resource Filter Name1","Resource Filter Name2"]`. Als u geen filter wilt toepassen, voert u `[]` in. Als u een resourcefilter invoert, moet u ook waarden voor **meterfilters** invoeren.
    * **Meterfilter**: voer een lijst met filters voor meters in. Dit is verplicht voor budgetten met de budgetcategorie **Gebruik**. De verwachte indeling is `["Meter Filter Name1","Meter Filter Name2"]`. Als u geen **resourcefilter** hebt ingevoerd, voert u `[]` in.
    * **Ik ga akkoord met de bovenstaande voorwaarden**: Selecteren.

3. Selecteer **Aankoop**. Wanneer het budget correct is geïmplementeerd, ontvangt u een melding:

   ![Resource Manager-sjabloon, budget, portalmelding implementeren](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

Voor het implementeren van de sjabloon wordt de Azure-portal gebruikt. Naast de Azure-portal kunt u ook Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiesjablonen.

## <a name="validate-the-deployment"></a>De implementatie valideren

U kunt de Azure-portal gebruiken om te controleren of het budget is gemaakt door te navigeren naar **Cost Management + facturering** > een bereik selecteren > **Budgetten**. U kunt ook de volgende Azure CLI- of Azure PowerShell-scripts gebruiken om het budget weer te geven.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een budget niet meer nodig hebt, verwijdert u het met behulp van een van de volgende methoden:

### <a name="azure-portal"></a>Azure Portal

Ga naar **Kostenbeheer en facturering** > selecteer een factureringsbereik > **Budgetten** > selecteer een budget > selecteer vervolgens **Budget verwijderen**.

### <a name="command-line"></a>Opdrachtregel

U kunt het budget verwijderen met behulp van Azure CLI of Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the budget name:" &&
read budgetName &&
az consumption budget delete --budget-name $budgetName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$budgetName = Read-Host -Prompt "Enter the budget name"
Remove-AzConsumptionBudget -Name $budgetName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure-budget gemaakt voor de implementatie. Ga verder met de onderstaande artikelen voor meer informatie over Azure Cost Management en facturering en Azure Resource Manager.

- Lees het overzicht [Cost Management en facturering](../cost-management-billing-overview.md)
- [Budgetten maken](tutorial-acm-create-budgets.md) in de Azure-portal
- Meer informatie over [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
