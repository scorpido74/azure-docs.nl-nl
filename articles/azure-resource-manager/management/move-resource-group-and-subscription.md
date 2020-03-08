---
title: Resources verplaatsen naar een nieuw abonnement of een nieuwe resource groep
description: Azure Resource Manager gebruiken voor resources verplaatsen naar een nieuwe resourcegroep of abonnement.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 40432c55a7f7e289d2e5cbc8afe94847074e4ca8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357721"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement

In dit artikel wordt beschreven hoe u Azure-resources verplaatst naar een ander Azure-abonnement of een andere resource groep onder hetzelfde abonnement. U kunt resources verplaatsen via de Azure-portal, Azure PowerShell, Azure CLI of de REST API.

Zowel de bron groep als de doel groep worden tijdens de verplaatsings bewerking vergrendeld. Schrijf- en verwijderingsbewerkingen voor de resourcegroepen worden vergrendeld tot de bewerking is voltooid. Deze vergren deling betekent dat u geen resources in de resource groepen kunt toevoegen, bijwerken of verwijderen. Dit betekent niet dat de resources zijn geblokkeerd. Als u bijvoorbeeld een SQL-server en de bijbehorende database naar een nieuwe resourcegroep verplaatst, heeft de toepassing die gebruikmaakt van de database geen last van downtime. De server kan nog steeds naar de database schrijven en deze lezen. De vergren deling kan Maxi maal vier uur duren, maar de meeste verplaatsingen worden veel minder tijd in beslag.

Als u een resource verplaatst, wordt deze alleen verplaatst naar een nieuwe resourcegroep of nieuw abonnement. Hierdoor wordt de locatie van de resource niet gewijzigd.

## <a name="checklist-before-moving-resources"></a>Controlelijst voordat u de resource verplaatst

Voordat u een resource verplaatst, moeten er enkele belangrijke stappen worden uitgevoerd. U kunt fouten voorkomen door te controleren of aan de volgende voorwaarden is voldaan.

1. De resources die u wilt verplaatsen, moeten de verplaatsings bewerking ondersteunen. Zie [ondersteuning voor het verplaatsen van resources voor bronnen](move-support-resources.md)voor een lijst met resources die kunnen worden verplaatst.

1. Sommige services hebben specifieke beperkingen of vereisten bij het verplaatsen van resources. Als u een van de volgende services wilt verplaatsen, controleert u of de richt lijnen voorafgaand aan het verplaatsen.

   * [Hulp App Services verplaatsen](./move-limitations/app-service-move-limitations.md)
   * [Richt lijnen voor het verplaatsen van Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Klassiek implementatie model richt lijnen](./move-limitations/classic-model-move-limitations.md) voor het verplaatsen van klassieke compute, klassieke opslag, klassieke virtuele netwerken en Cloud Services
   * [Richt lijnen voor netwerk verplaatsing](./move-limitations/networking-move-limitations.md)
   * [Hulp Recovery Services verplaatsen](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Hulp Virtual Machines verplaatsen](./move-limitations/virtual-machines-move-limitations.md)

1. De bron-en doel abonnementen moeten actief zijn. Als u problemen ondervindt bij het inschakelen van een uitgeschakeld account, [maakt u een ondersteunings aanvraag voor Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecteer **abonnements beheer** voor het type probleem.

1. De bron-en doel abonnementen moeten zich in dezelfde [Azure Active Directory Tenant](../../active-directory/develop/quickstart-create-new-tenant.md)bevinden. Om te controleren dat beide abonnementen dezelfde tenant-ID hebben, gebruikt u Azure PowerShell of Azure CLI.

   Voor Azure PowerShell gebruiken:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Gebruik voor Azure CLI:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Als de tenant-id's voor de bron- en doelabonnementen niet hetzelfde zijn, moet u de volgende methoden gebruiken om af te stemmen van de tenant-id's:

   * [Eigendom van een Azure-abonnement naar een ander account overdragen](../../billing/billing-subscription-transfer.md)
   * [Een Azure-abonnement koppelen of toevoegen aan Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Het doelabonnement moet zijn geregistreerd voor de resourceprovider van de resource die wordt verplaatst. Als dat niet het geval is, wordt er een fout bericht weer gegeven met de mede deling dat het **abonnement niet is geregistreerd voor een resource type**. Mogelijk ziet u deze fout wanneer u een resource verplaatst naar een nieuw abonnement, maar dat abonnement nooit is gebruikt voor dat resource type.

   Gebruik de volgende opdrachten om op te halen van de registratiestatus voor PowerShell:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Gebruik het volgende voor het registreren van een resourceprovider:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Gebruik de volgende opdrachten om op te halen van de status van de registratie voor Azure CLI:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Gebruik het volgende voor het registreren van een resourceprovider:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Het verplaatsen van de resources account moet ten minste de volgende machtigingen hebben:

   * **Micro soft. resources/abonnementen/resourceGroups/moveResources/actie** voor de bron resource groep.
   * **Micro soft. resources/abonnementen/resourceGroups/schrijven** voor de doel resource groep.

1. Controleer voordat u de resources verplaatst, de abonnementquota voor het abonnement dat u de resources wilt verplaatsen. Als het verplaatsen van de resources betekent dat het abonnement overschrijdt de grenzen, moet u om te controleren of u een verhoging van de quota kan opvragen. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor een lijst met limieten en het aanvragen van een verhoging.

1. **Voor een verplaatsing tussen abonnementen moeten de resource en de afhankelijke resources zich in dezelfde resource groep bevinden en moeten ze samen worden verplaatst.** Een virtuele machine met Managed disks vereist bijvoorbeeld dat de virtuele machine en de beheerde schijven samen worden verplaatst, samen met andere afhankelijke bronnen.

   Als u een resource naar een nieuw abonnement verplaatst, controleert u of de resource afhankelijke resources heeft en of ze zich in dezelfde resource groep bevinden. Als de resources zich niet in dezelfde resource groep bevinden, controleert u of de resources kunnen worden geconsolideerd in dezelfde resource groep. Als dit het geval is, kunt u al deze resources in dezelfde resource groep plaatsen met behulp van een Verplaats bewerking tussen resource groepen.

   Zie [scenario voor verplaatsen tussen abonnementen](#scenario-for-move-across-subscriptions)voor meer informatie.

## <a name="scenario-for-move-across-subscriptions"></a>Scenario voor het verplaatsen van meerdere abonnementen

Het verplaatsen van resources van het ene naar het andere abonnement is een proces dat uit drie stappen bestaat:

![scenario voor het verplaatsen van meerdere abonnementen](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Voor illustratie doeleinden hebben we slechts één afhankelijke resource.

* Stap 1: als afhankelijke resources worden gedistribueerd over verschillende resource groepen, moet u deze eerst verplaatsen naar één resource groep.
* Stap 2: Verplaats de resource en afhankelijke resources samen van het bron abonnement naar het doel abonnement.
* Stap 3: de afhankelijke resources eventueel opnieuw distribueren naar verschillende resource groepen binnen het doel abonnement. 

## <a name="validate-move"></a>Verplaatsen valideren

Met de [bewerking verplaatsing valideren](/rest/api/resources/resources/validatemoveresources) kunt u het verplaatsings scenario testen zonder de resources daad werkelijk te verplaatsen. Gebruik deze bewerking om te controleren of de verplaatsing slaagt. Validatie wordt automatisch aangeroepen wanneer u een verplaatsings aanvraag verzendt. Gebruik deze bewerking alleen als u de resultaten vooraf moet bepalen. Als u wilt deze bewerking uitvoeren, moet u de:

* naam van de resourcegroep
* Resource-ID van de doelresourcegroep
* Resource-ID van elke resource verplaatsen
* het [toegangs token](/rest/api/azure/#acquire-an-access-token) voor uw account

De volgende aanvraag verzenden:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Met een aanvraagtekst:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Als de aanvraag is ingedeeld, retourneert de bewerking:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

De statuscode 202 geeft aan dat de validatieaanvraag is geaccepteerd, maar deze nog niet nog bepaald als de bewerking voor verplaatsen slaagt. De `location` waarde bevat een URL die u gebruikt om de status van de langlopende bewerking te controleren.  

Om te controleren of de status, de volgende aanvraag te verzenden:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Terwijl de bewerking wordt nog steeds uitgevoerd, wordt u voor het ontvangen van de statuscode 202 blijven. Wacht het aantal seconden dat is opgegeven in de `retry-after` waarde voordat u het opnieuw probeert. Als de verplaatsing is gevalideerd, ontvangt u de statuscode 204. Als de validatie voor het verplaatsen is mislukt, ontvangt u een foutbericht weergegeven, zoals:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Gebruik de portal

Als u resources wilt verplaatsen, selecteert u de resource groep met deze resources en selecteert u vervolgens de knop **verplaatsen** .

![resources verplaatsen](./media/move-resource-group-and-subscription/select-move.png)

Selecteer of u de resources naar een nieuwe resourcegroep of een nieuw abonnement verplaatsen wilt.

Selecteer de resources wilt verplaatsen en de resourcegroep van de bestemming. Bevestig dat u scripts voor deze resources moet bijwerken en selecteer **OK**. Als u het pictogram bewerken abonnement in de vorige stap hebt geselecteerd, moet u ook het doelabonnement.

![doel selecteren](./media/move-resource-group-and-subscription/select-destination.png)

In **meldingen**ziet u dat de verplaatsings bewerking wordt uitgevoerd.

![move-status weergeven](./media/move-resource-group-and-subscription/show-status.png)

Wanneer deze is voltooid, krijgt u een melding van het resultaat.

![Zet resultaten weergeven](./media/move-resource-group-and-subscription/show-result.png)

Als er een fout optreedt, raadpleegt u [problemen met het verplaatsen van Azure-resources naar een nieuwe resource groep of een nieuw abonnement](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

Als u bestaande resources wilt verplaatsen naar een andere resource groep of een ander abonnement, gebruikt u de opdracht [Move-AzResource](/powershell/module/az.resources/move-azresource) . In het volgende voor beeld ziet u hoe u verschillende resources kunt verplaatsen naar een nieuwe resource groep.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Als u wilt overstappen op een nieuw abonnement, neemt u een waarde op voor de para meter `DestinationSubscriptionId`.

Als er een fout optreedt, raadpleegt u [problemen met het verplaatsen van Azure-resources naar een nieuwe resource groep of een nieuw abonnement](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Azure CLI gebruiken

Als u bestaande resources wilt verplaatsen naar een andere resource groep of een ander abonnement, gebruikt u de opdracht [AZ resource Move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) . Geef de resource-id's van de resources te verplaatsen. In het volgende voor beeld ziet u hoe u verschillende resources kunt verplaatsen naar een nieuwe resource groep. Geef in de para meter `--ids` een door spaties gescheiden lijst op met de resource-Id's die u wilt verplaatsen.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Als u wilt overstappen op een nieuw abonnement, geeft u de para meter `--destination-subscription-id` op.

Als er een fout optreedt, raadpleegt u [problemen met het verplaatsen van Azure-resources naar een nieuwe resource groep of een nieuw abonnement](troubleshoot-move.md).

## <a name="use-rest-api"></a>REST API gebruiken

Als u bestaande resources wilt verplaatsen naar een andere resource groep of een ander abonnement, gebruikt u de bewerking [resources verplaatsen](/rest/api/resources/Resources/MoveResources) .

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

In de hoofdtekst van de aanvraag geeft u de doelresourcegroep die is en de resources te verplaatsen.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Als er een fout optreedt, raadpleegt u [problemen met het verplaatsen van Azure-resources naar een nieuwe resource groep of een nieuw abonnement](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Vraag: de bewerking voor het verplaatsen van resources, die meestal een paar minuten duurt, is bijna een uur actief. Is er iets mis?**

Het verplaatsen van een resource is een complexe bewerking met verschillende fasen. Dit kan meer inhouden dan alleen de resource provider van de resource die u wilt verplaatsen. Vanwege de afhankelijkheden tussen resource providers, Azure Resource Manager 4 uur toegestaan om de bewerking te volt ooien. Deze tijds periode biedt resource providers de mogelijkheid om te herstellen van tijdelijke problemen. Als uw verplaatsings aanvraag binnen de periode van vier uur duurt, blijft de bewerking het volt ooien en kan het nog wel slagen. De bron-en doel resource groepen zijn tijdens deze periode vergrendeld om consistentie problemen te voor komen.

**Vraag: Waarom is mijn resource groep gedurende vier uur vergrendeld tijdens het verplaatsen van de resource?**

Het venster van vier uur is de maximale toegestane tijd voor het verplaatsen van resources. Om te voor komen dat de resources worden verplaatst, worden de bron-en doel resource groepen vergrendeld voor de duur van het verplaatsen van de resource.

Er zijn twee fasen in een verplaatsings aanvraag. In de eerste fase wordt de resource verplaatst. In de tweede fase worden meldingen verzonden naar andere resource providers die afhankelijk zijn van de resource die wordt verplaatst. Een resource groep kan worden vergrendeld voor het hele venster van vier uur wanneer een resource provider niet in een fase werkt. Tijdens de toegestane tijd probeert Resource Manager de mislukte stap opnieuw.

Als een resource niet binnen het venster van vier uur kan worden verplaatst, worden beide resource groepen ontgrendeld met Resource Manager. Resources die zijn verplaatst, bevinden zich in de doel resource groep. Resources die niet kunnen worden verplaatst, blijven de bron resource groep.

**Vraag: wat zijn de gevolgen van de bron-en doel resource groepen die worden vergrendeld tijdens het verplaatsen van de resource?**

Met de vergren deling wordt voor komen dat u een resource groep verwijdert, een nieuwe resource maakt in een van de resource groepen of een van de resources verwijdert die bij de verplaatsing betrokken zijn.

In de volgende afbeelding ziet u een fout bericht van de Azure Portal wanneer een gebruiker een resource groep probeert te verwijderen die deel uitmaakt van een doorlopende verplaatsing.

![Fout bericht bij poging tot verwijderen verplaatsen](./media/move-resource-group-and-subscription/move-error-delete.png)

**Vraag: wat betekent de fout code ' MissingMoveDependentResources '?**

Bij het verplaatsen van een resource moeten de afhankelijke resources bestaan in de doel resource groep of het abonnement of worden opgenomen in de verplaatsings aanvraag. U krijgt de fout code MissingMoveDependentResources wanneer een afhankelijke resource niet aan deze vereiste voldoet. Het fout bericht bevat details over de afhankelijke resource die moet worden opgenomen in de verplaatsings aanvraag.

Als u bijvoorbeeld een virtuele machine verplaatst, moet u zeven resource typen verplaatsen met drie verschillende resource providers. De resource providers en-typen zijn:

* Microsoft.Compute
   * Informatie
   * cd's
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Een ander algemeen voor beeld is het verplaatsen van een virtueel netwerk. Mogelijk moet u enkele andere bronnen verplaatsen die aan het virtuele netwerk zijn gekoppeld. De verplaatsings aanvraag kan het verplaatsen van open bare IP-adressen, route tabellen, virtuele netwerk gateways, netwerk beveiligings groepen en andere vereisen.

**Vraag: Waarom kan ik sommige resources niet verplaatsen in azure?**

Momenteel kunnen niet alle resources in azure-ondersteuning worden verplaatst. Zie [ondersteuning voor het verplaatsen van resources voor bronnen](move-support-resources.md)voor een lijst met resources die kunnen worden verplaatst.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteuning voor het verplaatsen van resources voor bronnen](move-support-resources.md)voor een lijst met resources die kunnen worden verplaatst.
