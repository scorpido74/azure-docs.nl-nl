---
title: Resources vergrendelen om wijzigingen te voorkomen
description: Voorkomen dat gebruikers kritieke Azure-bronnen bijwerken of verwijderen door een vergrendeling toe te passen voor alle gebruikers en rollen.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 70fb189adb634b7ac24afe7cc8b94738117da5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274006"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Resources vergrendelen om onverwachte wijzigingen te voorkomen

Als beheerder moet u een ​​abonnement, resourcegroep of resource mogelijk vergrendelen om te voorkomen dat andere gebruikers in uw organisatie per ongeluk kritieke resources verwijderen of wijzigen. U kunt de vergrendeling instellen op **CanNotDelete** of **ReadOnly**. In de portal worden de sloten respectievelijk **Delete** en **Read-only** genoemd.

* **CanNotDelete** betekent dat geautoriseerde gebruikers nog steeds een bron kunnen lezen en wijzigen, maar dat ze de bron niet kunnen verwijderen. 
* **ReadOnly** betekent alleen dat geautoriseerde gebruikers een bron kunnen lezen, maar ze kunnen de bron niet verwijderen of bijwerken. Het toepassen van dit slot is vergelijkbaar met het beperken van alle geautoriseerde gebruikers tot de machtigingen die worden verleend door **de** leesrol.

## <a name="how-locks-are-applied"></a>Hoe sloten worden toegepast

Wanneer u een vergrendeling toepast op een bovenliggend bereik, erven alle bronnen binnen dat bereik hetzelfde slot. Zelfs resources die u later toevoegt, erven het slot van de bovenliggende. Het meest beperkende slot in de overerving heeft voorrang.

In tegenstelling tot op rollen gebaseerd toegangsbeheer wordt met beheervergrendelingen een beperking toegepast op alle gebruikers en rollen. Zie [Azure Role-based Access Control](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het instellen van machtigingen voor gebruikers en rollen.

Vergrendelingen van Resource Manager gelden alleen voor bewerkingen die zich op beheerniveau voordoen, wat bewerkingen zijn die worden verzonden naar `https://management.azure.com`. De vergrendelingen hebben geen invloed op hoe resources hun eigen functies uitvoeren. Resourcewijzigingen worden beperkt, maar resourcebewerkingen worden niet beperkt. Een ReadOnly-vergrendeling op een SQL-database voorkomt bijvoorbeeld dat u de database verwijderen of wijzigen. U kunt echter wel gegevens in de database maken, bijwerken of verwijderen. Gegevenstransacties zijn toegestaan omdat deze bewerkingen niet naar `https://management.azure.com` worden verzonden.

Het toepassen van **ReadOnly** kan leiden tot onverwachte resultaten omdat sommige bewerkingen die de resource niet lijken te wijzigen, acties vereisen die door het slot zijn geblokkeerd. Het **readonly-slot** kan worden toegepast op de resource of op de resourcegroep die de resource bevat. Enkele veelvoorkomende voorbeelden van bewerkingen die worden geblokkeerd door een **ReadOnly-vergrendeling** zijn:

* Een **ReadOnly-vergrendeling** op een opslagaccount voorkomt dat alle gebruikers de sleutels kunnen vermelden. De bewerking voor het weergeven van de lijst met sleutels wordt verwerkt via een POST-aanvraag, omdat de geretourneerde sleutels beschikbaar zijn voor schrijfbewerkingen.

* Een **ReadOnly-vergrendeling** op een App Service-bron voorkomt dat Visual Studio Server Explorer bestanden voor de bron weergeeft, omdat die interactie schrijftoegang vereist.

* Een **ReadOnly-vergrendeling** op een resourcegroep die een virtuele machine bevat, voorkomt dat alle gebruikers de virtuele machine starten of opnieuw opstarten. Voor deze bewerkingen is een POST-verzoek vereist.

## <a name="who-can-create-or-delete-locks"></a>Wie kan sloten maken of verwijderen

Als u beheervergrendelingen wilt maken `Microsoft.Authorization/*` of `Microsoft.Authorization/locks/*` verwijderen, moet u toegang hebben tot of acties. Van de ingebouwde rollen worden deze acties alleen toegekend aan **Eigenaar** en **Administrator voor gebruikerstoegang**.

## <a name="managed-applications-and-locks"></a>Beheerde toepassingen en vergrendelingen

Sommige Azure-services, zoals Azure Databricks, gebruiken [beheerde toepassingen](../managed-applications/overview.md) om de service te implementeren. In dat geval maakt de service twee resourcegroepen. Eén resourcegroep bevat een overzicht van de service en is niet vergrendeld. De andere resourcegroep bevat de infrastructuur voor de service en is vergrendeld.

Als u de infrastructuurbrongroep probeert te verwijderen, wordt er een foutmelding weergegeven dat de brongroep is vergrendeld. Als u het slot voor de infrastructuurbrongroep probeert te verwijderen, krijgt u een foutmelding dat het slot niet kan worden verwijderd omdat het eigendom is van een systeemtoepassing.

Verwijder in plaats daarvan de service, die ook de infrastructuurbrongroep verwijdert.

Selecteer voor beheerde toepassingen de service die u hebt geïmplementeerd.

![Service selecteren](./media/lock-resources/select-service.png)

Let op: de service bevat een koppeling voor een **beheerde resourcegroep**. Die resourcegroep heeft de infrastructuur en is vergrendeld. Het kan niet direct worden verwijderd.

![Beheerde groep weergeven](./media/lock-resources/show-managed-group.png)

Als u alles voor de service wilt verwijderen, inclusief de brongroep voor vergrendelde infrastructuur, selecteert u **Verwijderen** voor de service.

![Service verwijderen](./media/lock-resources/delete-service.png)

## <a name="azure-backups-and-locks"></a>Azure-back-ups en -vergrendelingen

Als u de brongroep vergrendelt die is gemaakt door Azure Backup Service, worden back-ups mislukt. De service ondersteunt maximaal 18 herstelpunten. Met een **CanNotDelete-vergrendeling** kan de back-upservice herstelpunten niet opschonen. Zie [Veelgestelde vragen back-ups van Azure VM's voor](../../backup/backup-azure-vm-backup-faq.md)meer informatie.

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Template

Wanneer u een resourcemanagersjabloon gebruikt om een vergrendeling te implementeren, gebruikt u verschillende waarden voor de naam en het type, afhankelijk van het bereik van het slot.

Wanneer u een vergrendeling toepast op een **resource,** gebruikt u de volgende indelingen:

* naam -`{resourceName}/Microsoft.Authorization/{lockName}`
* type -`{resourceProviderNamespace}/{resourceType}/providers/locks`

Wanneer u een vergrendeling toepast op een **resourcegroep** of **-abonnement,** gebruikt u de volgende indelingen:

* naam -`{lockName}`
* type -`Microsoft.Authorization/locks`

In het volgende voorbeeld wordt een sjabloon weergegeven waarmee een app-serviceplan, een website en een vergrendeling op de website worden gemaakt. Het resourcetype van de vergrendeling is het resourcetype van de resource die moet worden vergrendeld **en/of providers/vergrendelingen**. De naam van het slot wordt gemaakt door de bronnaam samen te stellen met **/Microsoft.Authorization/** en de naam van het slot.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Zie [Een resourcegroep maken en vergrendelen](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)voor een voorbeeld van het instellen van een vergrendeling voor een resourcegroep.

## <a name="powershell"></a>PowerShell
U vergrendelt geïmplementeerde resources met Azure PowerShell met de opdracht [Nieuw-AzResourceLock.](/powershell/module/az.resources/new-azresourcelock)

Als u een resource wilt vergrendelen, geeft u de naam op van de resource, het resourcetype en de naam van de resourcegroep.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Als u een resourcegroep wilt vergrendelen, geeft u de naam van de resourcegroep op.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Als u informatie wilt krijgen over een vergrendeling, gebruikt u [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Gebruik het als:

```azurepowershell-interactive
Get-AzResourceLock
```

Gebruik het als u alle vergrendelingen voor een resource wilt gebruiken:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Gebruik het als u alle vergrendelingen voor een resourcegroep wilt gebruiken:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Gebruik het als u een vergrendeling wilt verwijderen:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure-CLI

U vergrendelt geïmplementeerde resources met Azure CLI met de opdracht [AZ Lock Create.](/cli/azure/lock#az-lock-create)

Als u een resource wilt vergrendelen, geeft u de naam op van de resource, het resourcetype en de naam van de resourcegroep.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Als u een resourcegroep wilt vergrendelen, geeft u de naam van de resourcegroep op.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Gebruik [de AZ-vergrendelingslijst](/cli/azure/lock#az-lock-list)om informatie over een slot te krijgen. Gebruik het als:

```azurecli
az lock list
```

Gebruik het als u alle vergrendelingen voor een resource wilt gebruiken:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Gebruik het als u alle vergrendelingen voor een resourcegroep wilt gebruiken:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Gebruik het als u een vergrendeling wilt verwijderen:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
U geïmplementeerde resources vergrendelen met de [REST API voor beheervergrendelingen.](https://docs.microsoft.com/rest/api/resources/managementlocks) Met de REST API u vergrendelingen maken en verwijderen en informatie over bestaande vergrendelingen ophalen.

Als u een vergrendeling wilt maken, voert u het als:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Het bereik kan een abonnement, resourcegroep of resource zijn. De lock-naam is hoe je het slot wilt noemen. Voor api-versie, gebruik **2016-09-01**.

Voeg in de aanvraag een JSON-object op dat de eigenschappen voor de vergrendeling opgeeft.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Volgende stappen
* Zie Labels gebruiken om uw [resources te ordenen](tag-resources.md) voor meer informatie over het logisch organiseren van uw resources
* U beperkingen en conventies toepassen op uw abonnement met aangepaste beleidsregels. Zie [Wat is Azure Policy?](../../governance/policy/overview.md) voor meer informatie.
* Voor begeleiding bij de manier waarop ondernemingen Resource Manager effectief kunnen gebruiken voor het beheer van abonnementen, gaat u naar [Azure enterprise-platform - Prescriptieve abonnementsgovernance](/azure/architecture/cloud-adoption-guide/subscription-governance).

