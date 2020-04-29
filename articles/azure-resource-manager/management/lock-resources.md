---
title: Resources vergren delen om wijzigingen te voor komen
description: Voor komen dat gebruikers essentiële Azure-resources bijwerken of verwijderen door een vergren deling toe te passen op alle gebruikers en rollen.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 70fb189adb634b7ac24afe7cc8b94738117da5ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79274006"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Resources vergrendelen om onverwachte wijzigingen te voorkomen

Als beheerder moet u een ​​abonnement, resourcegroep of resource mogelijk vergrendelen om te voorkomen dat andere gebruikers in uw organisatie per ongeluk kritieke resources verwijderen of wijzigen. U kunt de vergrendeling instellen op **CanNotDelete** of **ReadOnly**. In de portal worden de vergren delingen respectievelijk **verwijderen** en **alleen-lezen** genoemd.

* **CanNotDelete** betekent dat geautoriseerde gebruikers nog steeds een resource kunnen lezen en wijzigen, maar de resource niet kan verwijderen. 
* **Alleen-lezen** houdt in dat gemachtigde gebruikers een resource kunnen lezen, maar de resource niet kunnen verwijderen of bijwerken. Het Toep assen van deze vergren deling is vergelijkbaar met het beperken van alle gemachtigde gebruikers tot de machtigingen die door de rol van **lezer** worden verleend.

## <a name="how-locks-are-applied"></a>Hoe vergrendelingen worden toegepast

Wanneer u een vergren deling toepast op een bovenliggend bereik, nemen alle resources binnen die scope dezelfde vergren deling. Zelfs resources die u later toevoegt, nemen de vergren deling van het bovenliggende knoop punt over. De meest beperkende vergren deling in de overname heeft prioriteit.

In tegenstelling tot op rollen gebaseerd toegangsbeheer wordt met beheervergrendelingen een beperking toegepast op alle gebruikers en rollen. Zie [Access Control op basis van rollen](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het instellen van machtigingen voor gebruikers en rollen.

Vergrendelingen van Resource Manager gelden alleen voor bewerkingen die zich op beheerniveau voordoen, wat bewerkingen zijn die worden verzonden naar `https://management.azure.com`. De vergrendelingen hebben geen invloed op hoe resources hun eigen functies uitvoeren. Resourcewijzigingen worden beperkt, maar resourcebewerkingen worden niet beperkt. Een alleen-lezen vergrendeling op een SQL Database voor komt dat u de Data Base kunt verwijderen of wijzigen. U kunt echter wel gegevens in de database maken, bijwerken of verwijderen. Gegevenstransacties zijn toegestaan omdat deze bewerkingen niet naar `https://management.azure.com` worden verzonden.

Toep assen van **alleen-lezen** kan leiden tot onverwachte resultaten omdat sommige bewerkingen die de resource niet hoeven te wijzigen, acties vereisen die door de vergren deling worden geblokkeerd. De **alleen-lezen** vergrendeling kan worden toegepast op de resource of aan de resource groep met de resource. Enkele algemene voor beelden van bewerkingen die worden geblokkeerd door een **alleen-lezen** vergrendeling zijn:

* Als u een **alleen-lezen** vergrendeling op een opslag account instelt, kunnen alle gebruikers de sleutels niet weer geven. De bewerking voor het weergeven van de lijst met sleutels wordt verwerkt via een POST-aanvraag, omdat de geretourneerde sleutels beschikbaar zijn voor schrijfbewerkingen.

* Met een **alleen-lezen** vergrendeling voor een app service resource kan Visual Studio Server Explorer bestanden voor de resource niet weer geven omdat die interactie schrijf toegang vereist.

* Een **alleen-lezen** vergrendeling voor een resource groep die een virtuele machine bevat, voor komt dat alle gebruikers de virtuele machine starten of opnieuw starten. Deze bewerkingen vereisen een POST-aanvraag.

## <a name="who-can-create-or-delete-locks"></a>Wie kan vergren delingen maken of verwijderen?

Als u beheer vergrendelingen wilt maken of verwijderen, moet u `Microsoft.Authorization/*` toegang `Microsoft.Authorization/locks/*` tot of acties hebben. Van de ingebouwde rollen worden deze acties alleen toegekend aan **Eigenaar** en **Administrator voor gebruikerstoegang**.

## <a name="managed-applications-and-locks"></a>Beheerde toepassingen en vergren delingen

Sommige Azure-Services, zoals Azure Databricks, gebruiken [beheerde toepassingen](../managed-applications/overview.md) voor het implementeren van de service. In dat geval maakt de service twee resource groepen. Een resource groep bevat een overzicht van de service en is niet vergrendeld. De andere resource groep bevat de infra structuur voor de service en is vergrendeld.

Als u de infrastructuur resource groep probeert te verwijderen, wordt er een fout bericht weer gegeven met de mede deling dat de resource groep is vergrendeld. Als u de vergren deling voor de infrastructuur resource groep probeert te verwijderen, wordt er een fout bericht weer gegeven dat de vergren deling niet kan worden verwijderd omdat deze het eigendom is van een systeem toepassing.

Verwijder in plaats daarvan de service, waardoor ook de infrastructuur resource groep wordt verwijderd.

Selecteer voor beheerde toepassingen de service die u hebt geïmplementeerd.

![Service selecteren](./media/lock-resources/select-service.png)

U ziet dat de service een koppeling voor een **beheerde resource groep**bevat. Deze resource groep bevat de infra structuur en is vergrendeld. Het kan niet rechtstreeks worden verwijderd.

![Beheerde groep weer geven](./media/lock-resources/show-managed-group.png)

Als u alles wilt verwijderen voor de service, inclusief de resource groep vergrendelde infra structuur, selecteert u **verwijderen** voor de service.

![Service verwijderen](./media/lock-resources/delete-service.png)

## <a name="azure-backups-and-locks"></a>Back-ups en vergren delingen van Azure

Als u de resource groep die is gemaakt met Azure Backup-Service vergrendelt, mislukken de back-ups. De service ondersteunt Maxi maal 18 herstel punten. Met een **CanNotDelete** -vergren deling kan de back-upservice geen herstel punten opschonen. Zie [Veelgestelde vragen-back-ups maken van Azure vm's](../../backup/backup-azure-vm-backup-faq.md)voor meer informatie.

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Template

Wanneer u een resource manager-sjabloon gebruikt om een vergren deling te implementeren, gebruikt u verschillende waarden voor de naam en het type, afhankelijk van het bereik van de vergren deling.

Bij het Toep assen van een vergren deling op een **resource**gebruikt u de volgende indelingen:

* naam`{resourceName}/Microsoft.Authorization/{lockName}`
* voert`{resourceProviderNamespace}/{resourceType}/providers/locks`

Bij het Toep assen van een vergren deling op een **resource groep** of- **abonnement**, gebruikt u de volgende indelingen:

* naam`{lockName}`
* voert`Microsoft.Authorization/locks`

In het volgende voor beeld ziet u een sjabloon waarmee u een app service-plan, een website en een vergren deling op de website maakt. Het bron type van de vergren deling is het resource type van de resource die u wilt vergren delen en **/providers/Locks**. De naam van de vergren deling wordt gemaakt door de resource naam met **/Microsoft.Authorization/** en de naam van de vergren deling samen te voegen.

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

Zie [een resource groep maken en deze vergren delen](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)voor een voor beeld van het instellen van een vergren deling voor een resource groep.

## <a name="powershell"></a>PowerShell
U vergrendelt geïmplementeerde resources met Azure PowerShell met behulp van de opdracht [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) .

Als u een resource wilt vergren delen, geeft u de naam van de resource, het resource type en de naam van de resource groep op.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Als u een resource groep wilt vergren delen, geeft u de naam van de resource groep op.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Gebruik [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock)om informatie over een vergren deling op te halen. Als u alle vergren delingen in uw abonnement wilt ophalen, gebruikt u:

```azurepowershell-interactive
Get-AzResourceLock
```

Als u alle vergren delingen voor een resource wilt ophalen, gebruikt u:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Als u alle vergren delingen voor een resource groep wilt ophalen, gebruikt u:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Als u een vergren deling wilt verwijderen, gebruikt u:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

U vergrendelt geïmplementeerde resources met Azure CLI met behulp van de opdracht [AZ Lock Create](/cli/azure/lock#az-lock-create) .

Als u een resource wilt vergren delen, geeft u de naam van de resource, het resource type en de naam van de resource groep op.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Als u een resource groep wilt vergren delen, geeft u de naam van de resource groep op.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Gebruik [AZ Lock List](/cli/azure/lock#az-lock-list)om informatie over een vergren deling op te halen. Als u alle vergren delingen in uw abonnement wilt ophalen, gebruikt u:

```azurecli
az lock list
```

Als u alle vergren delingen voor een resource wilt ophalen, gebruikt u:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Als u alle vergren delingen voor een resource groep wilt ophalen, gebruikt u:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Als u een vergren deling wilt verwijderen, gebruikt u:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST-API
U kunt geïmplementeerde resources vergren delen met de [rest API voor beheer vergrendelingen](https://docs.microsoft.com/rest/api/resources/managementlocks). Met de REST API kunt u vergren delingen maken en verwijderen, en informatie over bestaande vergren delingen ophalen.

Voer de volgende handelingen uit om een vergren deling te maken:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Het bereik kan een abonnement, een resource groep of een resource zijn. De naam van de vergren deling is datgene wat u aan de vergren deling wilt aanroepen. Gebruik **2016-09-01**voor de API-versie.

Neem in de aanvraag een JSON-object op waarmee de eigenschappen voor de vergren deling worden opgegeven.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Volgende stappen
* Zie [Tags gebruiken om uw resources te organiseren](tag-resources.md) voor meer informatie over het logisch ordenen van uw resources
* U kunt beperkingen en conventies Toep assen op uw abonnement met aangepast beleid. Zie [Wat is Azure Policy?](../../governance/policy/overview.md) voor meer informatie.
* Voor begeleiding bij de manier waarop ondernemingen Resource Manager effectief kunnen gebruiken voor het beheer van abonnementen, gaat u naar [Azure enterprise-platform - Prescriptieve abonnementsgovernance](/azure/architecture/cloud-adoption-guide/subscription-governance).

