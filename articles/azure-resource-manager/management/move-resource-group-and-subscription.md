---
title: Resources verplaatsen naar een nieuw abonnement of resourcegroep
description: Gebruik Azure Resource Manager om resources naar een nieuwe brongroep of -abonnement te verplaatsen.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 40432c55a7f7e289d2e5cbc8afe94847074e4ca8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248851"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement

In dit artikel ziet u hoe u Azure-resources verplaatst naar een ander Azure-abonnement of een andere brongroep onder hetzelfde abonnement. U kunt resources verplaatsen via de Azure-portal, Azure PowerShell, Azure CLI of de REST API.

Zowel de brongroep als de doelgroep zijn vergrendeld tijdens de verhuizing. Schrijf- en verwijderingsbewerkingen voor de resourcegroepen worden vergrendeld tot de bewerking is voltooid. Dit slot betekent dat u geen resources in de brongroepen toevoegen, bijwerken of verwijderen. Het betekent niet dat de middelen bevroren zijn. Als u bijvoorbeeld een SQL-server en de bijbehorende database naar een nieuwe resourcegroep verplaatst, heeft de toepassing die gebruikmaakt van de database geen last van downtime. De server kan nog steeds naar de database schrijven en deze lezen. Het slot kan maximaal vier uur duren, maar de meeste bewegingen worden in veel minder tijd voltooid.

Als u een resource verplaatst, wordt deze alleen verplaatst naar een nieuwe resourcegroep of nieuw abonnement. Hierdoor wordt de locatie van de resource niet gewijzigd.

## <a name="checklist-before-moving-resources"></a>Controlelijst voordat u de resource verplaatst

Voordat u een resource verplaatst, moeten er enkele belangrijke stappen worden uitgevoerd. U kunt fouten voorkomen door te controleren of aan de volgende voorwaarden is voldaan.

1. De resources die u wilt verplaatsen, moeten de verplaatsingsbewerking ondersteunen. Zie Ondersteuning voor de bewerking [verplaatsen voor resources voor](move-support-resources.md)een lijst met de ondersteuning van resources .

1. Sommige services hebben specifieke beperkingen of vereisten bij het verplaatsen van resources. Als u een van de volgende services verplaatst, controleert u die richtlijnen voordat u verhuist.

   * [Richtlijnen voor verplaatsen van App Services](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services verplaatsen richtlijnen](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Richtlijnen voor klassieke implementatiemodellen](./move-limitations/classic-model-move-limitations.md) - Klassieke compute-, klassieke opslag, klassieke virtuele netwerken en cloudservices
   * [Richtlijnen voor netwerkverplaatsingen](./move-limitations/networking-move-limitations.md)
   * [Herstelservices verplaatsen richtlijnen](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Virtuele machines verplaatsen richtlijnen](./move-limitations/virtual-machines-move-limitations.md)

1. De bron- en bestemmingsabonnementen moeten actief zijn. Als u problemen ondervindt bij het inschakelen van een account dat is uitgeschakeld, [maakt u een Azure-ondersteuningsaanvraag](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecteer **Abonnementsbeheer** voor het type probleem.

1. De bron- en doelabonnementen moeten bestaan binnen dezelfde [Azure Active Directory-tenant](../../active-directory/develop/quickstart-create-new-tenant.md). Als u wilt controleren of beide abonnementen dezelfde tenant-id hebben, gebruikt u Azure PowerShell of Azure CLI.

   Gebruik voor Azure PowerShell:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Gebruik voor Azure CLI:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Als de tenant-geïdentificeerde-geïdentificeerde gegevens voor de bron- en bestemmingsabonnementen niet hetzelfde zijn, gebruikt u de volgende methoden om de tenant-geïdentificeerde gegevens met elkaar te verzoenen:

   * [Eigendom van een Azure-abonnement naar een ander account overdragen](../../billing/billing-subscription-transfer.md)
   * [Een Azure-abonnement koppelen of toevoegen aan Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Het doelabonnement moet zijn geregistreerd voor de resourceprovider van de resource die wordt verplaatst. Als dit niet het zo is, ontvangt u een foutmelding dat het **abonnement niet is geregistreerd voor een resourcetype.** Deze fout wordt mogelijk weergegeven wanneer u een resource verplaatst naar een nieuw abonnement, maar dat abonnement is nooit gebruikt met dat resourcetype.

   Gebruik voor PowerShell de volgende opdrachten om de registratiestatus op te halen:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Als u een resourceprovider wilt registreren, gebruikt u het:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Gebruik voor Azure CLI de volgende opdrachten om de registratiestatus op te halen:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Als u een resourceprovider wilt registreren, gebruikt u het:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Het account dat de resources verplaatst, moet ten minste de volgende machtigingen hebben:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** op de bronbrongroep.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** on the destination resource group.

1. Controleer voordat u de resources verplaatst, de abonnementsquota voor het abonnement waarnaar u de resources verplaatst. Als het verplaatsen van de resources betekent dat het abonnement de limieten overschrijdt, moet u controleren of u een verhoging van het quotum aanvragen. Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor een lijst met limieten en hoe u een verhoging aanvragen.

1. **Voor een overstap over abonnementen moeten de resource en de afhankelijke resources zich in dezelfde resourcegroep bevinden en moeten ze samen worden verplaatst.** Een VM met beheerde schijven vereist bijvoorbeeld dat de VM en de beheerde schijven samen worden verplaatst, samen met andere afhankelijke resources.

   Als u een resource verplaatst naar een nieuw abonnement, controleert u of de resource afhankelijke resources heeft en of deze zich in dezelfde resourcegroep bevinden. Als de resources zich niet in dezelfde resourcegroep bevindt, controleert u of de resources kunnen worden samengevoegd tot dezelfde resourcegroep. Als dat het zo is, brengt u al deze resources naar dezelfde resourcegroep met behulp van een verplaatsingsbewerking tussen resourcegroepen.

   Zie [Scenario voor het verplaatsen van abonnementen voor](#scenario-for-move-across-subscriptions)meer informatie .

## <a name="scenario-for-move-across-subscriptions"></a>Scenario voor het verplaatsen tussen abonnementen

Het verplaatsen van resources van het ene abonnement naar het andere is een proces in drie stappen:

![scenario voor het verplaatsen van abonnementen](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Ter illustratie hebben we slechts één afhankelijke bron.

* Stap 1: Als afhankelijke resources over verschillende resourcegroepen zijn verdeeld, verplaatst u deze eerst naar één resourcegroep.
* Stap 2: Verplaats de resource en afhankelijke resources samen van het bronabonnement naar het doelabonnement.
* Stap 3: Distribueer de afhankelijke resources optioneel naar verschillende resourcegroepen binnen het doelabonnement. 

## <a name="validate-move"></a>Verplaatsen valideren

Met [de verplaatsingsbewerking](/rest/api/resources/resources/validatemoveresources) u uw verplaatsingsscenario testen zonder de resources daadwerkelijk te verplaatsen. Gebruik deze bewerking om te controleren of de verplaatsing slaagt. Validatie wordt automatisch aangeroepen wanneer u een verhuisverzoek verzendt. Gebruik deze bewerking alleen wanneer u de resultaten vooraf moet bepalen. Als u deze bewerking wilt uitvoeren, hebt u het:

* naam van de bronbrongroep
* resource-ID van de doelgroep
* resource-ID van elke resource om te verplaatsen
* het [toegangstoken](/rest/api/azure/#acquire-an-access-token) voor uw account

Stuur het volgende verzoek:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Met een aanvraagorgaan:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Als de aanvraag correct is opgemaakt, wordt de bewerking geretourneerd:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

De statuscode van 202 geeft aan dat de validatieaanvraag is geaccepteerd, maar het is nog niet bepaald of de verplaatsingsbewerking zal slagen. De `location` waarde bevat een URL die u gebruikt om de status van de langlopende bewerking te controleren.  

Als u de status wilt controleren, stuurt u het volgende verzoek:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Terwijl de bewerking nog steeds wordt uitgevoerd, blijft u de statuscode van 202 ontvangen. Wacht het aantal seconden `retry-after` dat in de waarde is aangegeven voordat u het opnieuw probeert. Als de verplaatsingsbewerking is gevalideerd, ontvangt u de statuscode van 204. Als de verplaatsingsvalidatie mislukt, ontvangt u een foutbericht, zoals:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Gebruik de portal

Als u resources wilt verplaatsen, selecteert u de resourcegroep met deze resources en selecteert u de knop **Verplaatsen.**

![resources verplaatsen](./media/move-resource-group-and-subscription/select-move.png)

Selecteer of u de resources verplaatst naar een nieuwe resourcegroep of een nieuw abonnement.

Selecteer de resources die u wilt verplaatsen en de brongroep van de bestemming. Erken dat u scripts voor deze bronnen moet bijwerken en selecteer **OK**. Als u het pictogram Abonnement bewerken in de vorige stap hebt geselecteerd, moet u ook het bestemmingsabonnement selecteren.

![bestemming selecteren](./media/move-resource-group-and-subscription/select-destination.png)

In **Meldingen**ziet u dat de verplaatsingsbewerking wordt uitgevoerd.

![verplaatsingsstatus weergeven](./media/move-resource-group-and-subscription/show-status.png)

Wanneer het is voltooid, wordt u op de hoogte gesteld van het resultaat.

![verhuisresultaat weergeven](./media/move-resource-group-and-subscription/show-result.png)

Als er een fout optreedt, [raadpleegt u Problemen met het verplaatsen van Azure-resources naar een nieuwe brongroep of -abonnement](troubleshoot-move.md)oplossen.

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

Als u bestaande resources wilt verplaatsen naar een andere resourcegroep of -abonnement, gebruikt u de opdracht [Move-AzResource.](/powershell/module/az.resources/move-azresource) In het volgende voorbeeld ziet u hoe u verschillende resources verplaatst naar een nieuwe resourcegroep.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Als u naar een nieuw abonnement `DestinationSubscriptionId` wilt gaan, moet u een waarde voor de parameter opnemen.

Als er een fout optreedt, [raadpleegt u Problemen met het verplaatsen van Azure-resources naar een nieuwe brongroep of -abonnement](troubleshoot-move.md)oplossen.

## <a name="use-azure-cli"></a>Azure CLI gebruiken

Als u bestaande resources wilt verplaatsen naar een andere resourcegroep of -abonnement, gebruikt u de opdracht [AZ-bronverplaatsing.](/cli/azure/resource?view=azure-cli-latest#az-resource-move) Geef de resource-id's van de resources om te verplaatsen. In het volgende voorbeeld ziet u hoe u verschillende resources verplaatst naar een nieuwe resourcegroep. Geef `--ids` in de parameter een lijst met gescheiden ruimtegegevens op met de bron-id's die u wilt verplaatsen.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Als u naar een nieuw `--destination-subscription-id` abonnement wilt gaan, geeft u de parameter op.

Als er een fout optreedt, [raadpleegt u Problemen met het verplaatsen van Azure-resources naar een nieuwe brongroep of -abonnement](troubleshoot-move.md)oplossen.

## <a name="use-rest-api"></a>REST API gebruiken

Als u bestaande resources wilt verplaatsen naar een andere resourcegroep of -abonnement, gebruikt u de bewerking [Resources verplaatsen.](/rest/api/resources/Resources/MoveResources)

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

In de aanvraaginstantie geeft u de doelgroep en de resources op die u wilt verplaatsen.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Als er een fout optreedt, [raadpleegt u Problemen met het verplaatsen van Azure-resources naar een nieuwe brongroep of -abonnement](troubleshoot-move.md)oplossen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Vraag: Mijn resource move operatie, die meestal duurt een paar minuten, is uitgevoerd voor bijna een uur. Is er iets mis?**

Het verplaatsen van een resource is een complexe bewerking met verschillende fasen. Het kan meer dan alleen de resource provider van de bron die u probeert te verplaatsen. Vanwege de afhankelijkheden tussen resourceproviders staat Azure Resource Manager 4 uur toe voordat de bewerking is voltooid. Deze periode geeft resourceproviders een kans om te herstellen van tijdelijke problemen. Als uw verhuisaanvraag binnen de periode van 4 uur is, blijft de bewerking proberen te voltooien en kan het nog steeds lukken. De bron- en doelbrongroepen zijn gedurende deze periode vergrendeld om consistentieproblemen te voorkomen.

**Vraag: Waarom is mijn resourcegroep 4 uur vergrendeld tijdens resourceverplaatsing?**

Het venster van 4 uur is de maximale tijd die is toegestaan voor resourceverplaatsing. Om te voorkomen dat wijzigingen in de resources worden verplaatst, worden zowel de bron- als doelbrongroepen vergrendeld voor de duur van de bronverplaatsing.

Er zijn twee fasen in een verhuisverzoek. In de eerste fase wordt de resource verplaatst. In de tweede fase worden meldingen verzonden naar andere resourceproviders die afhankelijk zijn van de bron die wordt verplaatst. Een resourcegroep kan worden vergrendeld voor het volledige venster van 4 uur wanneer een resourceprovider beide fasen uitvalt. Tijdens de toegestane tijd probeert Resource Manager de mislukte stap opnieuw in.

Als een resource niet kan worden verplaatst binnen het venster van 4 uur, ontgrendelt Resource Manager beide resourcegroepen. Resources die zijn verplaatst, bevinden zich in de resourcegroep van de bestemming. Resources die niet kunnen worden verplaatst, worden de bronbrongroep verlaten.

**Vraag: Wat zijn de implicaties van de bron- en bestemmingsbrongroepen die tijdens de resourceverplaatsing zijn vergrendeld?**

Het slot voorkomt dat u een van de resourcesgroep verwijderen, een nieuwe resource maken in een resourcegroep of een van de resources die bij de verhuizing betrokken zijn, verwijderen.

In de volgende afbeelding wordt een foutbericht van de Azure-portal weergegeven wanneer een gebruiker een brongroep probeert te verwijderen die deel uitmaakt van een doorlopende verplaatsing.

![Foutbericht verplaatsen dat probeert te verwijderen](./media/move-resource-group-and-subscription/move-error-delete.png)

**Vraag: Wat betekent de foutcode "MissingMoveDependentResources"?**

Bij het verplaatsen van een resource moeten de afhankelijke resources bestaan in de doelbrongroep of het abonnement of worden opgenomen in de verplaatsingsaanvraag. U krijgt de foutcode MissingMoveDependentResources wanneer een afhankelijke resource niet aan deze vereiste voldoet. Het foutbericht bevat details over de afhankelijke resource die moet worden opgenomen in de verplaatsingsaanvraag.

Voor het verplaatsen van een virtuele machine kunnen bijvoorbeeld zeven resourcetypen met drie verschillende resourceproviders worden verplaatst. Deze resourceproviders en -typen zijn:

* Microsoft.Compute
   * virtueleMachines
   * Schijven
* Microsoft.Network
  * netwerkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtuelenetwerken
* Microsoft.Storage
  * storageAccounts

Een ander veelvoorkomend voorbeeld is het verplaatsen van een virtueel netwerk. Mogelijk moet u verschillende andere bronnen verplaatsen die aan dat virtuele netwerk zijn gekoppeld. De verhuisaanvraag kan vereisen dat openbare IP-adressen, routetabellen, virtuele netwerkgateways, netwerkbeveiligingsgroepen en andere moeten worden verplaatst.

**Vraag: Waarom kan ik sommige resources niet verplaatsen in Azure?**

Momenteel worden niet alle resources in Azure-ondersteuning verplaatst. Zie Ondersteuning van de bewerking [verplaatsen voor resources voor](move-support-resources.md)een lijst met resources die move ondersteunen.

## <a name="next-steps"></a>Volgende stappen

Zie Ondersteuning voor de bewerking [verplaatsen voor resources voor](move-support-resources.md)een lijst met de ondersteuning van resources .
