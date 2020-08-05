---
title: 'Quickstart: een budget maken met een Azure Resource Manager-sjabloon'
description: Deze quickstart laat zien hoe u een budget kunt maken met een Azure Resource Manager-sjabloon.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 07/28/2020
ms.custom: subject-armqs
ms.openlocfilehash: 984f2d82e21344dd7e3bb8b7267e289832343e1b
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87385773"
---
# <a name="quickstart-create-a-budget-with-an-arm-template"></a>Quickstart: Een budget opstellen met een ARM-sjabloon

Met budgetten in Cost Management kunt u de verantwoordelijkheid in de organisatie beter plannen en stimuleren. Met budgetten kunt u de Azure-services die u gebruikt of waarop u zich abonneert voor een specifieke periode bijhouden. Ze helpen u anderen te informeren over hun uitgaven om kosten proactief te beheren en uitgaven in de loop van de tijd bij te houden. Wanneer de budgetdrempels die u hebt gemaakt, worden overschreden, worden er meldingen geactiveerd. Dit heeft geen gevolgen voor uw resources en uw gebruik wordt niet gestopt. U kunt budgetten gebruiken om bestedingen te vergelijken en bij te houden bij het analyseren van kosten. Deze quickstart laat zien hoe u een budget kunt maken met een Azure Resource Manager-sjabloon (ARM-sjabloon).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Als u een nieuw abonnement hebt, kunt u niet direct een budget maken of andere Cost Management-functies gebruiken. Het kan tot 48 uur duren voordat u alle Cost Management-functies kunt gebruiken.

Budgetten worden ondersteund voor de volgende Azure-accounttypen en -bereiken:

- Bereiken voor toegangsbeheer op basis van rollen in Azure
    - Beheergroepen
    - Abonnement
- Enterprise Agreement-bereiken
    - Factureringsaccount
    - Afdeling
    - Inschrijvingsaccount
- Afzonderlijke overeenkomsten
    - Factureringsaccount
- Microsoft-klantovereenkomst-bereiken
    - Factureringsaccount
    - Factureringsprofiel
    - Factuursectie
    - Klant
- AWS-bereiken
    - Extern account
    - Extern abonnement

Als u budgetten wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig.

Voor Azure EA-abonnementen moet u leestoegang hebben om budgetten weer te geven. Als u budgetten wilt maken en beheren, moet u de machtiging Inzender hebben.

In abonnementen worden de volgende Azure-machtigingen, of bereiken, ondersteund voor budgetten voor gebruikers en groepen. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.

- Eigenaar: eigenaren kunnen budgetten voor een abonnement instellen, wijzigen en verwijderen.
- Inzender en Inzender Cost Management: inzenders kunnen hun eigen budgetten instellen, wijzigen en verwijderen. Inzenders kunnen ook de budgetbedragen aanpassen voor budgetten die zijn gemaakt door anderen.
- Lezer en Lezer Cost Management: lezers kunnen budgetten inzien waarvoor ze zijn gemachtigd.

Zie [Toegang tot gegevens van Cost Management toewijzen](assign-access-acm-data.md) voor meer informatie over het toewijzen van toegang tot de gegevens van Cost Management.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" :::

Er is één Azure-resource gedefinieerd in de sjabloon:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Een Azure-budget maken.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon wordt een budget gemaakt.

   [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden.

   :::image type="content" source="./media/quick-create-budget-template/create-budget-using-template-portal.png" alt-text="Resource Manager-sjabloon, budget maken, portal implementeren]" lightbox="./media/quick-create-budget-template/create-budget-using-template-portal.png" :::
   
    * **Abonnement**: selecteer een Azure-abonnement.
    * **Resourcegroep**: selecteer indien mogelijk een bestaande resourcegroep of **maak een nieuwe resourcegroep**.
    * **Regio**: selecteer een Azure-regio. Bijvoorbeeld **VS - centraal**.
    * **Budgetnaam**: voer een naam in voor het budget. Deze moet uniek zijn binnen de resourcegroep. Alleen alfanumerieke tekens, onderstrepingstekens en streepjes zijn toegestaan.
    * **Hoeveelheid**: voer de totale kosten in die met behulp van het budget moet worden bijgehouden.
    * **Tijdsinterval**: voer de periode in die het budget dekt. Toegestane waarden zijn maandelijks, per kwartaal of per jaar. Het budget wordt aan het einde van de tijdsinterval opnieuw ingesteld.
    * **Begindatum**: voer de begindatum in, uitgaande van de eerste dag van de maand, in de notatie JJJJ-MM-DD. Een toekomstige begindatum mag niet meer dan drie maanden in de toekomstig liggen vanaf vandaag. U kunt een eerdere begindatum opgeven met de periode van de tijdsinterval.
    * **Einddatum**: Geef de einddatum voor het budget op in de notatie JJJJ-MM-DD. 
    * **Eerste drempelwaarde**: voer een drempelwaarde in voor de eerste melding. Er wordt een melding verzonden wanneer de kosten de drempelwaarde overschrijden. Dit is altijd een percentage van 0 tot en met 1000.
    * **Tweede drempelwaarde**: voer een drempelwaarde in voor de tweede melding. Er wordt een melding verzonden wanneer de kosten de drempelwaarde overschrijden. Dit is altijd een percentage van 0 tot en met 1000.
    * **Contactrollen**: voer een lijst met contactrollen in waarnaar de budgetmelding moet worden verzonden wanneer de drempelwaarde wordt overschreden. Standaardwaarden zijn Eigenaar, Inzender en Lezer. De verwachte indeling is `["Owner","Contributor","Reader"]`.
    * **Contact-e-mails** voer een lijst met e-mailadressen in waarnaar de budgetmelding moet worden verzonden wanneer een drempelwaarde wordt overschreden. De verwachte indeling is `["user1@domain.com","user2@domain.com"]`.
    * **Contactgroepen**: voer een lijst met resource-id's van actiegroepen in als volledige resource-URI's waarnaar de budgetmelding moet worden verzonden wanneer de drempelwaarde wordt overschreden. Dit veld accepteert een matrix van tekenreeksen. De verwachte indeling is `["action group resource ID1","action group resource ID2"]`. Als u geen actiegroepen wilt gebruiken, voert u `[]` in.
    * **Filterwaarden van resourcegroep:** voer een lijst met namen van resourcegroepen in om te filteren. De verwachte indeling is `["Resource Group Name1","Resource Group Name2"]`. Als u geen filter wilt toepassen, voert u `[]` in. 
    * **De waarden van de metercategoriefilter:** voer een lijst met Azure-servicemetercategorieën in. De verwachte indeling is `["Meter Category1","Meter Category2"]`. Als u geen filter wilt toepassen, voert u `[]` in.
   
3. Voer een van de volgende acties uit, afhankelijk van het type Azure-abonnement:
   - Selecteer **Controleren + maken**.
   - Controleer de voorwaarden en schakel het selectievakje **Ik ga akkoord met de bovenstaande voorwaarden** in en selecteer **Aankoop**.

4. Als u **Beoordelen en maken** hebt geselecteerd, wordt uw sjabloon gevalideerd. Selecteer **Maken**.  

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
