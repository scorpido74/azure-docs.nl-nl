---
title: Nalevings gegevens voor beleid ophalen
description: Azure Policy-evaluaties en effecten bepaalt de naleving. Meer informatie over hoe u de compatibiliteits Details van uw Azure-resources kunt ophalen.
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384414"
---
# <a name="get-compliance-data-of-azure-resources"></a>Compatibiliteits gegevens van Azure-resources ophalen

Een van de grootste voor delen van Azure Policy is het inzicht en de bedienings elementen biedt meer informatie over resources in een abonnement of [beheer groep](../../management-groups/overview.md) abonnementen. Dit besturingselement kan worden uitgeoefend op veel verschillende manieren, zoals het voorkomen van resources die worden gemaakt in de verkeerde locatie afdwingen van het gebruik van de gemeenschappelijke en consistente tag, of bestaande resources van de controle voor configuraties en instellingen van toepassing. In alle gevallen worden gegevens gegenereerd door Azure Policy zodat u de compatibiliteits status van uw omgeving kunt begrijpen.

Er zijn verschillende manieren toegang krijgen tot de informatie over naleving die zijn gegenereerd door uw beleid en initiatieftoewijzingen:

- De [Azure Portal](#portal) gebruiken
- Via script [opdracht regel](#command-line)

Voordat u de methoden voor het rapporteren van naleving bekijkt, laten we kijken wanneer de compatibiliteitsinformatie wordt bijgewerkt en de frequentie en gebeurtenissen die een evaluatiecyclus van een te activeren.

> [!WARNING]
> Als de nalevings status wordt gerapporteerd als **niet geregistreerd**, controleert u of de resource provider **micro soft. PolicyInsights** is geregistreerd en of de gebruiker beschikt over de juiste RBAC-machtigingen (op rollen gebaseerde toegangs beheer), zoals beschreven in [RBAC in azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Evaluatie-triggers

De resultaten van een voltooide evaluatie cyclus zijn beschikbaar in de `Microsoft.PolicyInsights` resource provider via `PolicyStates` en `PolicyEvents` bewerkingen. Zie [Azure Policy Insights](/rest/api/policy-insights/)voor meer informatie over de bewerkingen van de Azure Policy Insights-rest API.

Evaluatie van toegewezen beleid en de initiatieven gebeuren als gevolg van diverse gebeurtenissen:

- Een beleid of initiatief is zojuist toegewezen aan een bereik. Het duurt ongeveer 30 minuten voor de toewijzing moet worden toegepast op het bereik zijn gedefinieerd. Zodra deze toegepast, de evaluatiecyclus voor installatie wordt gestart voor resources binnen dat bereik op basis van de zojuist toegewezen beleid of initiatief en afhankelijk van de effecten die worden gebruikt door het beleid of initiatief, resources gemarkeerd als compatibel of niet-compatibel. Een grote beleid of initiatief geëvalueerd op basis van een grote resourcesbereik kan even duren. Er is daarom geen vooraf gedefinieerde verwachting van wanneer u de evaluatiecyclus voor installatie wordt voltooid. Zodra deze is voltooid, is bijgewerkt nalevingsresultaten zijn beschikbaar in de portal en SDK's.

- Een beleid of initiatief is al toegewezen aan een bereik wordt bijgewerkt. De evaluatiefase computerbeleid en het tijdstip voor dit scenario is hetzelfde als voor een nieuwe toewijzing aan een bereik.

- Een resource wordt geïmplementeerd op een scope met een toewijzing via Resource Manager, REST, Azure CLI of Azure PowerShell. In dit scenario wordt de gebeurtenis effect (toevoegen, controleren, weigeren, implementeren) en informatie van de nalevingsstatus voor de afzonderlijke resource beschikbaar is in de portal en SDK's ongeveer 15 minuten later. Deze gebeurtenis niet leidt tot een evaluatie van andere bronnen.

- Standard naleving evaluatiecyclus voor installatie. Elke 24 uur, toewijzingen worden automatisch opnieuw geëvalueerd. Een grote beleid of initiatief van veel bronnen kan tijd duren, zodat er geen vooraf gedefinieerde verwachting van wanneer de evaluatiecyclus voor de wordt voltooid. Zodra deze is voltooid, is bijgewerkt nalevingsresultaten zijn beschikbaar in de portal en SDK's.

- De provider van de [gast configuratie](../concepts/guest-configuration.md) resource is bijgewerkt met compatibiliteits Details door een beheerde resource.

- Scan op aanvraag

### <a name="on-demand-evaluation-scan"></a>Evaluatie op aanvraag scannen

Een evaluatiescan voor een abonnement of resourcegroep kan worden gestart met een aanroep naar de REST-API. Deze scan is een asynchroon proces. De REST-eindpunt om te beginnen de scan wacht niet als zodanig totdat de scan is voltooid om te reageren. In plaats daarvan biedt geen URI voor de status van de aangevraagde evaluatie opvragen.

In elke REST API-URI zijn er verschillende variabelen die worden gebruikt en die u moet vervangen door uw eigen waarden:

- `{YourRG}`-vervangen door de naam van de resource groep
- Vervang `{subscriptionId}` door uw abonnements-ID

De scan biedt ondersteuning voor evaluatie van de resources in een abonnement of in een resourcegroep. Start een scan op bereik met een REST API opdracht **post** met de volgende URI-structuren:

- Abonnement

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Resourcegroep

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

De aanroep retourneert een **geaccepteerde** status van 202. Opgenomen in de antwoord header is een **locatie** -eigenschap met de volgende indeling:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` is statisch gegenereerd voor het aangevraagde bereik. Als een bereik wordt al uitgevoerd voor een scan op aanvraag, wordt een nieuwe scan is niet gestart. In plaats daarvan krijgt de nieuwe aanvraag dezelfde `{ResourceContainerGUID}` **locatie** -URI voor status. Een REST API **Get** -opdracht naar de **locatie** -URI retourneert een **202 geaccepteerd** tijdens de evaluatie. Wanneer de evaluatie scan is voltooid, wordt de status **200 OK** geretourneerd. De hoofdtekst van een voltooide scan is een JSON-antwoord met de status:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>De werking van naleving

In een toewijzing is een resource **niet-compatibel** als deze niet voldoet aan het beleid of initiatief regels.
De volgende tabel toont hoe verschillende beleid effecten met de evaluatie van de voorwaarden voor de resulterende nalevingsstatus werken:

| De status van resource | Effect | Evaluatie van het beleid | Nalevingsstatus |
| --- | --- | --- | --- |
| Bestaat | Weigeren, Controleren, Toevoegen\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Niet-compatibel |
| Bestaat | Weigeren, Controleren, Toevoegen\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Compatibel |
| Nieuw | Controleren, AuditIfNotExist\* | True | Niet-compatibel |
| Nieuw | Controleren, AuditIfNotExist\* | False | Compatibel |

\*Voor de acties Toevoegen, DeployIfNotExist en AuditIfNotExist moet de IF-instructie TRUE zijn.
De acties vereisen ook dat de bestaansvoorwaarde FALSE is om niet-compatibel te zijn. Indien TRUE, activeert de IF-voorwaarde de evaluatie van de bestaansvoorwaarde voor de gerelateerde resources.

Bijvoorbeeld, wordt ervan uitgegaan dat u een resourcegroep – ContsoRG, sommige opslagaccounts hebt (rood gemarkeerd) die worden weergegeven met een openbaar netwerk.

![Storage-accounts beschikbaar gemaakt met een openbaar netwerk](../media/getting-compliance-data/resource-group01.png)

In dit voorbeeld moet u het gebruik van beveiligingsrisico's. Nu dat u een beleidstoewijzing gemaakt hebt, wordt deze voor alle opslagaccounts in de resourcegroep ContosoRG geëvalueerd. De drie niet-compatibele opslag accounts worden gecontroleerd, waardoor de status ervan wordt gewijzigd in **niet-compatibel.**

![Niet-compatibele storage-accounts gecontroleerd](../media/getting-compliance-data/resource-group03.png)

Naast het **conform** -en **niet-nalevings**beleid en de bronnen zijn er drie andere statussen:

- **Conflicterende**: er zijn twee of meer beleids regels met conflicterende regels. Bijvoorbeeld: twee beleidsregels die dezelfde tag met verschillende waarden toe te voegen.
- **Niet gestart**: de evaluatie cyclus is niet gestart voor het beleid of de resource.
- **Niet geregistreerd**: de Azure Policy resource provider is niet geregistreerd of het account dat is aangemeld, heeft geen machtiging voor het lezen van de compatibiliteits gegevens.

Azure Policy gebruikt de velden **type** en **naam** in de definitie om te bepalen of een resource een overeenkomst is. Wanneer de bron overeenkomt, wordt deze beschouwd als toepasselijk en heeft deze de status **compatibel** of **niet-compatibel**. Als een van beide **typen** of **namen** de enige eigenschap in de definitie is, worden alle resources gezien als toepasselijk en geëvalueerd.

Het nalevings percentage wordt bepaald door het verdelen van de **compatibele** resources op basis van het _totale aantal resources_.
Het _totale aantal resources_ wordt gedefinieerd als de som van de **compatibele**, **niet-compatibele**en **conflicterende** resources. De algemene compatibiliteits aantallen zijn de som van afzonderlijke resources die **compatibel** zijn gedeeld door de som van alle afzonderlijke resources. In de onderstaande afbeelding zijn er 20 afzonderlijke resources die van toepassing zijn en slechts één **niet-compatibel**is. De algemene compatibiliteit van de resource is 95% (19 van 20).

![Voor beeld van naleving van beleids regels op nalevings pagina](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portal

De Azure portal brengt een grafische interface van visualiseren en inzicht krijgen in de status van naleving in uw omgeving. Op de pagina **beleid** bevat de optie **overzicht** Details over de beschik bare bereiken over de naleving van beide beleids regels en initiatieven. Samen met de compatibiliteitsstatus en het aantal per toewijzing bevat een grafiek met naleving in de afgelopen zeven dagen. De pagina **naleving** bevat veel dezelfde informatie (met uitzonde ring van de grafiek), maar biedt extra filters en sorteer opties.

![Voor beeld van Azure Policy nalevings pagina](../media/getting-compliance-data/compliance-page.png)

Omdat een beleid of initiatief kan worden toegewezen aan verschillende bereiken, bevat de tabel in het bereik voor elke toewijzing en het type van de definitie die is toegewezen. Het aantal niet-compatibele resources en niet-conforme beleidsregels voor elke toewijzing worden ook gegeven. Te klikken op een beleid of initiatief in de tabel bevat een stil bij de naleving voor die bepaalde toewijzing.

![Voor beeld van Azure Policy pagina nalevings Details](../media/getting-compliance-data/compliance-details.png)

De lijst met resources op het tabblad **resource compatibiliteit** bevat de evaluatie status van bestaande resources voor de huidige toewijzing. Het tabblad wordt standaard ingesteld op **niet-compatibel**, maar kan worden gefilterd.
Gebeurtenissen (toevoegen, controleren, weigeren, implementeren) die zijn geactiveerd door de aanvraag om een resource te maken, worden weer gegeven op het tabblad **gebeurtenissen** .

> [!NOTE]
> Voor een AKS-engine beleid is de weer gegeven resource de resource groep.

![Voor beeld van Azure Policy nalevings gebeurtenissen](../media/getting-compliance-data/compliance-events.png)

Voor resources van de [resource provider modus](../concepts/definition-structure.md#resource-provider-modes) selecteert u op het tabblad **resource naleving** de resource, klikt u met de rechter muisknop op de rij en selecteert u **compatibiliteits details weer geven** de details van de onderdeel naleving. Deze pagina bevat ook tabbladen voor een overzicht van de beleids regels die zijn toegewezen aan deze resource, gebeurtenissen, onderdeel gebeurtenissen en wijzigings geschiedenis.

![Voor beeld van de compatibiliteits Details van Azure Policy onderdelen](../media/getting-compliance-data/compliance-components.png)

Klik op de pagina Resource naleving met de rechter muisknop op de rij van de gebeurtenis waarvoor u meer informatie wilt verzamelen en selecteer **activiteiten logboeken weer geven**. De pagina activiteitenlogboek wordt geopend en wordt vooraf gefilterd voor de zoekopdracht met de details voor de toewijzing en de gebeurtenissen. Het activiteitenlogboek bevat aanvullende context en informatie over deze gebeurtenissen.

![Voor beeld van Azure Policy nalevings activiteiten logboek](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Meer informatie over niet-naleving

<a name="change-history-preview"></a>

Wanneer een resource als **niet-compatibel**wordt beschouwd, zijn er veel mogelijke redenen. Zie [niet-naleving bepalen](./determine-non-compliance.md)om de reden vast te stellen waarom een resource **niet-compatibel** is of om de wijziging te vinden.

## <a name="command-line"></a>Opdrachtregel

Dezelfde informatie die beschikbaar is in de portal kan worden opgehaald met de REST API (inclusief [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell en Azure cli (preview).
Zie de naslag informatie voor [Azure Policy Insights](/rest/api/policy-insights/) voor volledige details over de rest API. De naslaginformatie over REST-API's hebben een groene 'Try It' knop op elke bewerking die kunt u nu uitproberen rechts in de browser.

Gebruik ARMClient of een soortgelijk hulp programma voor het afhandelen van verificatie voor Azure voor de REST API-voor beelden.

### <a name="summarize-results"></a>Resultaten samenvatten

Met de REST-API kan samenvatting worden uitgevoerd door de container, de definitie of de toewijzing. Hier volgt een voor beeld van een samen vatting op het abonnements niveau met behulp van Azure Policy Insight- [samen vatting voor het abonnement](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

De uitvoer bevat een overzicht van het abonnement. In de voorbeeld uitvoer hieronder vindt u een overzicht van de naleving onder **waarde. results. nonCompliantResources** en **Value. results. nonCompliantPolicies**. Deze aanvraag biedt meer details, met inbegrip van elke toewijzing die uit de niet-compatibele cijfers en de sitedefinitie-informatie voor elke toewijzing bestaat. Elk beleids object in de hiërarchie bevat een **queryResultsUri** die kan worden gebruikt om aanvullende details op dat niveau op te halen.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Query voor bronnen

In het bovenstaande voor beeld bevat **Value. policyAssignments. policyDefinitions. results. queryResultsUri** een voor beeld-URI voor alle niet-compatibele resources voor een specifieke beleids definitie. Op de **$filter** waarde is IsCompliant gelijk aan False, PolicyAssignmentId is opgegeven voor de beleids definitie en vervolgens de PolicyDefinitionId zelf. De reden voor het opnemen van de PolicyAssignmentId in het filter is omdat de PolicyDefinitionId kan bestaan in verschillende beleid of initiatieftoewijzingen met verschillende bereiken. Zowel de PolicyAssignmentId en de PolicyDefinitionId opgeeft, kunnen we in de resultaten die we op zoek bent naar expliciete zijn. Voorheen werd voor PolicyStates de laatste **versie**gebruikt, waarbij automatisch een **van** - **en-** tijd venster van de laatste 24 uur wordt ingesteld.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Het onderstaande voorbeeldantwoord is bijgesneden tot een enkele niet-compatibele resource kort te houden. Het gedetailleerde antwoord heeft verschillende soorten gegevens over de resource, het beleid of initiatief en de toewijzing. U ziet dat u kunt ook zien welke toewijzing-parameters zijn doorgegeven aan de beleidsdefinitie.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Gebeurtenissen weergeven

Wanneer een resource wordt gemaakt of bijgewerkt, wordt een resultaat van evaluatie van beleid wordt gegenereerd. Resultaten worden _beleids gebeurtenissen_genoemd. Gebruik de volgende Uri om recente voor Beleidsgebeurtenissen die zijn gekoppeld aan het abonnement weer te geven.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

De resultaten zien er ongeveer als volgt uit:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Zie voor meer informatie over het uitvoeren van query's op beleids gebeurtenissen het artikel over [Azure Policy Events](/rest/api/policy-insights/policyevents) .

### <a name="azure-powershell"></a>Azure PowerShell

De module Azure PowerShell voor Azure Policy is beschikbaar op de PowerShell Gallery als [AZ. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Met PowerShellGet kunt u de module installeren met behulp van `Install-Module -Name Az.PolicyInsights` (Controleer of u de meest recente [Azure PowerShell](/powershell/azure/install-az-ps) hebt geïnstalleerd):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

De module heeft de volgende cmdlets:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Voorbeeld: Ophalen van de status van de samenvatting voor het bovenste toegewezen beleid met het hoogste aantal niet-compatibele resources.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Voorbeeld: Ophalen van de record van de status voor de meest recent geëvalueerd bron (standaard is door de timestamp in aflopende volgorde).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Voorbeeld: Ophalen van de details voor alle niet-compatibele virtuele-netwerkbronnen.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Voorbeeld: Ophalen van gebeurtenissen met betrekking tot niet-compatibele virtuele-netwerkbronnen die zijn opgetreden na een bepaalde datum.

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Het veld **PrincipalOid** kan worden gebruikt om een specifieke gebruiker te verkrijgen met de Azure PowerShell cmdlet `Get-AzADUser`. Vervang **{principalOid}** door de reactie die u uit het vorige voor beeld hebt ontvangen.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

Als u een [log Analytics-werk ruimte](../../../log-analytics/log-analytics-overview.md) hebt met `AzureActivity` van de [analyse van activiteitenlogboek oplossing](../../../azure-monitor/platform/activity-log-collect.md) die aan uw abonnement is gekoppeld, kunt u de resultaten van de niet-naleving van de evaluatie cyclus ook bekijken met behulp van eenvoudige Kusto-query's en de `AzureActivity` tabel. Met details in Azure Monitor-logboeken kunnen waarschuwingen worden geconfigureerd om niet-naleving te controleren.


![Naleving Azure Policy met behulp van Azure Monitor-logboeken](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](programmatically-create.md).
- Meer informatie over het [oplossen van niet-compatibele resources](remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).