---
title: Nalevingsgegevens voor beleid verzamelen
description: Evaluaties en effecten van Azure Policy bepalen de naleving. Meer informatie over het verzamelen van nalevingsgegevens van uw Azure-bronnen.
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280636"
---
# <a name="get-compliance-data-of-azure-resources"></a>Nalevingsgegevens van Azure-bronnen verzamelen

Een van de grootste voordelen van Azure Policy is het inzicht en de besturingselementen die het biedt over resources in een abonnement of [beheergroep](../../management-groups/overview.md) van abonnementen. Dit besturingselement kan op veel verschillende manieren worden uitgeoefend, zoals voorkomen dat resources op de verkeerde locatie worden gemaakt, het afdwingen van gemeenschappelijk en consistent taggebruik of het controleren van bestaande bronnen voor de juiste configuraties en instellingen. In alle gevallen worden gegevens gegenereerd door Azure Policy, zodat u inzicht krijgt in de nalevingsstatus van uw omgeving.

Er zijn verschillende manieren om toegang te krijgen tot de nalevingsgegevens die worden gegenereerd door uw beleids- en initiatieftoewijzingen:

- De [Azure-portal gebruiken](#portal)
- Door middel van [opdrachtregelscripting](#command-line)

Voordat we kijken naar de methoden om te rapporteren over naleving, laten we eens kijken wanneer nalevingsinformatie wordt bijgewerkt en naar de frequentie en gebeurtenissen die een evaluatiecyclus activeren.

> [!WARNING]
> Als nalevingsstatus wordt gerapporteerd als **Niet geregistreerd,** controleert u of de **Microsoft.PolicyInsights** Resource Provider is geregistreerd en of de gebruiker over de juiste RBAC-machtigingen (Role-based access control) beschikt, zoals beschreven in [RBAC in Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Evaluatietriggers

De resultaten van een voltooide evaluatiecyclus zijn beschikbaar in de `Microsoft.PolicyInsights` Resource Provider door `PolicyStates` en `PolicyEvents` operaties. Zie [Azure Policy Insights](/rest/api/policy-insights/)voor meer informatie over de bewerkingen van de AZURE Policy Insights REST API.

Evaluaties van toegewezen beleid en initiatieven vinden plaats als gevolg van verschillende gebeurtenissen:

- Een beleid of initiatief is nieuw toegewezen aan een scope. Het duurt ongeveer 30 minuten voordat de toewijzing wordt toegepast op het gedefinieerde bereik. Zodra deze is toegepast, begint de evaluatiecyclus voor resources binnen dat bereik ten opzichte van het nieuw toegewezen beleid of initiatief en afhankelijk van de effecten die door het beleid of initiatief worden gebruikt, worden resources gemarkeerd als compatibel of niet-compatibel. Een groot beleid of initiatief dat wordt geëvalueerd op basis van een groot scala aan middelen kan tijd vergen. Als zodanig is er geen vooraf gedefinieerde verwachting van wanneer de evaluatiecyclus zal voltooien. Zodra deze is voltooid, zijn bijgewerkte nalevingsresultaten beschikbaar in de portal en De SDK's.

- Een beleid of initiatief dat al aan een scope is toegewezen, wordt bijgewerkt. De evaluatiecyclus en -timing voor dit scenario is hetzelfde als voor een nieuwe toewijzing aan een scope.

- Een resource wordt geïmplementeerd in een scope met een toewijzing via Resource Manager, REST, Azure CLI of Azure PowerShell. In dit scenario wordt de effectgebeurtenis (toevoegen, controleren, weigeren, implementeren) en compatibele statusinformatie voor de afzonderlijke resource ongeveer 15 minuten later beschikbaar in de portal en SDKs. Deze gebeurtenis veroorzaakt geen evaluatie van andere bronnen.

- Standaard evaluatiecyclus voor naleving. Eens in de 24 uur worden opdrachten automatisch opnieuw geëvalueerd. Een groot beleid of initiatief van veel middelen kan tijd vergen, dus er is geen vooraf gedefinieerde verwachting van wanneer de evaluatiecyclus zal voltooien. Zodra deze is voltooid, zijn bijgewerkte nalevingsresultaten beschikbaar in de portal en De SDK's.

- De [resourceprovider gastconfiguratie](../concepts/guest-configuration.md) wordt bijgewerkt met nalevingsgegevens door een beheerde bron.

- On-demand scan

### <a name="on-demand-evaluation-scan"></a>Evaluatiescan op aanvraag

Een evaluatiescan voor een abonnement of een resourcegroep kan worden gestart met een aanroep naar de REST API. Deze scan is een asynchrone proces. Als zodanig wacht het REST-eindpunt om de scan te starten niet tot de scan is voltooid om te reageren. In plaats daarvan biedt het een URI om de status van de gevraagde evaluatie op te vragen.

In elke REST API-URI zijn er verschillende variabelen die worden gebruikt en die u moet vervangen door uw eigen waarden:

- `{YourRG}`- Vervangen door de naam van uw resourcegroep
- Vervang `{subscriptionId}` door uw abonnements-ID

De scan ondersteunt de evaluatie van resources in een abonnement of in een resourcegroep. Start een scan per bereik met de opdracht REST API **POST** met de volgende URI-structuren:

- Abonnement

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Resourcegroep

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

De oproep geeft een **status van 202 Geaccepteerd.** Inbegrepen in de antwoordkop is een eigenschap **Locatie** met de volgende indeling:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}`wordt statisch gegenereerd voor het gevraagde bereik. Als een scope al een on-demand scan uitvoert, wordt er geen nieuwe scan gestart. In plaats daarvan wordt de `{ResourceContainerGUID}` nieuwe aanvraag op dezelfde **locatie** URI voor status. Een opdracht REST API **GET** naar de **locatie** URI retourneert een **202 Geaccepteerd** terwijl de evaluatie aan de gang is. Wanneer de evaluatiescan is voltooid, geeft deze een **200 OK-status** terug. De hoofdtekst van een voltooide scan is een JSON-antwoord met de status:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Hoe compliance werkt

In een toewijzing is een resource **niet-compatibel** als deze geen beleids- of initiatiefregels volgt.
In de volgende tabel ziet u hoe verschillende beleidseffecten werken met de voorwaardebeoordeling voor de resulterende nalevingsstatus:

| Resourcestatus | Effect | Beleidsevaluatie | Nalevingsstatus |
| --- | --- | --- | --- |
| Bestaat | Weigeren, Controleren, Toevoegen\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Niet-compatibel |
| Bestaat | Weigeren, Controleren, Toevoegen\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Compatibel |
| Nieuw | Controleren, AuditIfNotExist\* | True | Niet-compatibel |
| Nieuw | Controleren, AuditIfNotExist\* | False | Compatibel |

\*Voor de acties Toevoegen, DeployIfNotExist en AuditIfNotExist moet de IF-instructie TRUE zijn.
De acties vereisen ook dat de bestaansvoorwaarde FALSE is om niet-compatibel te zijn. Indien TRUE, activeert de IF-voorwaarde de evaluatie van de bestaansvoorwaarde voor de gerelateerde resources.

Stel dat u een resourcegroep hebt : ContsoRG, met sommige opslagaccounts (rood gemarkeerd) die zijn blootgesteld aan openbare netwerken.

![Opslagaccounts die zijn blootgesteld aan openbare netwerken](../media/getting-compliance-data/resource-group01.png)

In dit voorbeeld moet u op uw hoede zijn voor beveiligingsrisico's. Nu u een beleidstoewijzing hebt gemaakt, wordt deze geëvalueerd voor alle opslagaccounts in de ContosoRG-brongroep. Het controleert de drie niet-conforme opslagaccounts en verandert daarom hun status in **niet-conform.**

![Gecontroleerde niet-conforme opslagaccounts](../media/getting-compliance-data/resource-group03.png)

Naast **compliant** en **niet-conform,** beleid en resources hebben drie andere staten:

- **Conflicterend**: Er bestaan twee of meer beleidsregels met conflicterende regels. Twee beleidsregels die dezelfde tag met verschillende waarden toevoegen.
- **Niet gestart**: de evaluatiecyclus is nog niet gestart voor het beleid of de resource.
- **Niet geregistreerd**: de Azure Policy Resource Provider is niet geregistreerd of het account dat is aangemeld heeft geen toestemming om nalevingsgegevens te lezen.

Azure Policy gebruikt de **tekst-** en **naamvelden** in de definitie om te bepalen of een resource een overeenkomst is. Wanneer de resource overeenkomt, wordt deze als toepasselijk beschouwd en heeft deze de status **van compliant** of **niet-conform.** Als **een type** of **naam** de enige eigenschap in de definitie is, worden alle resources als toepasselijk beschouwd en geëvalueerd.

Het nalevingspercentage wordt bepaald door **compliant** resources te delen door _totale resources_.
_De totale resources_ worden gedefinieerd als de som van de **resources compliant,** **niet-conform**en **conflicterend.** De algemene nalevingsnummers zijn de som van verschillende resources die **compatibel** zijn gedeeld door de som van alle afzonderlijke resources. In de afbeelding hieronder zijn er 20 verschillende bronnen die van toepassing zijn en slechts één is **niet-compatibel**. De totale naleving van de middelen is 95% (19 van de 20).

![Voorbeeld van naleving van het beleid vanaf de pagina Naleving](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portal

De Azure-portal toont een grafische ervaring van het visualiseren en begrijpen van de nalevingsstatus in uw omgeving. Op de pagina **Beleid** geeft de optie **Overzicht** informatie over beschikbare scopes over de naleving van zowel beleid als initiatieven. Samen met de nalevingsstatus en het aantal per toewijzing bevat deze een grafiek met naleving van de afgelopen zeven dagen. De **pagina Naleving** bevat veel van dezelfde informatie (behalve de grafiek), maar biedt extra filter- en sorteeropties.

![Voorbeeld van de pagina Azure Policy Compliance](../media/getting-compliance-data/compliance-page.png)

Aangezien een beleid of initiatief aan verschillende scopes kan worden toegewezen, bevat de tabel de ruimte voor elke toewijzing en het type definitie dat is toegewezen. Het aantal niet-conforme resources en niet-conformbeleid voor elke toewijzing wordt ook verstrekt. Als u op een beleid of initiatief in de tabel klikt, wordt dieper gekeken naar de naleving van die specifieke opdracht.

![Voorbeeld van de pagina Azure Policy Compliance Details](../media/getting-compliance-data/compliance-details.png)

In de lijst met resources op het tabblad **Resourcenaleving** wordt de evaluatiestatus van bestaande resources voor de huidige toewijzing weergegeven. Het tabblad wordt standaard ingesteld **op Niet-compatibel,** maar kan worden gefilterd.
Gebeurtenissen (toevoegen, controleren, weigeren, implementeren) die worden geactiveerd door het verzoek om een resource te maken, worden weergegeven onder het tabblad **Gebeurtenissen.**

> [!NOTE]
> Voor een AKS Engine-beleid is de weergegeven resource de resourcegroep.

![Voorbeeld van azure-beleidsnalevingsgebeurtenissen](../media/getting-compliance-data/compliance-events.png)

Als u resources [in de resourceprovidermodus](../concepts/definition-structure.md#resource-provider-modes) gebruikt, wordt op het tabblad **Resourcenaleving** de resource of met de rechtermuisknop op de rij geklikt en wordt **nalevingsdetails van** de component geopend. Op deze pagina worden ook tabbladen weergegeven om het beleid te bekijken dat is toegewezen aan deze resource, gebeurtenissen, componentgebeurtenissen en wijzigingsgeschiedenis.

![Voorbeeld van nalevingsgegevens van Azure Policy Component](../media/getting-compliance-data/compliance-components.png)

Terug op de pagina resourcecompliance klik je met de rechtermuisknop op de rij van de gebeurtenis waar je meer details over wilt verzamelen en selecteer **Activiteitslogboeken weergeven.** De pagina met activiteitenlogboeken wordt geopend en wordt vooraf gefilterd op de zoekopdracht met details voor de toewijzing en de gebeurtenissen. Het activiteitenlogboek biedt extra context en informatie over deze gebeurtenissen.

![Voorbeeld van azure Policy Compliance Activity Log](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Inzicht in niet-naleving

<a name="change-history-preview"></a>

Wanneer wordt vastgesteld dat een resources **niet-compatibel**zijn, zijn er veel mogelijke redenen. Zie [Niet-naleving](./determine-non-compliance.md)bepalen als u wilt bepalen waarom een resource **niet voldoet** of de verantwoordelijke wijziging wilt vinden.

## <a name="command-line"></a>Opdrachtregel

Dezelfde informatie die beschikbaar is in de portal kan worden opgehaald met de REST API (inclusief met [ARMClient),](https://github.com/projectkudu/ARMClient)Azure PowerShell en Azure CLI (preview).
Zie de naslaggids voor [Azure Policy Insights](/rest/api/policy-insights/) voor meer informatie over de REST API. De REST API-referentiepagina's hebben een groene knop 'Probeer het' op elke bewerking waarmee u het in de browser proberen.

Gebruik ARMClient of een soortgelijk hulpmiddel om verificatie naar Azure te verwerken voor de VOORBEELDEN van de REST API.

### <a name="summarize-results"></a>Resultaten samenvatten

Met de REST API kan summarisatie worden uitgevoerd per container, definitie of toewijzing. Hier is een voorbeeld van sommatie op abonnementsniveau met behulp van Azure Policy Insight's [Summarize For Subscription:](/rest/api/policy-insights/policystates/summarizeforsubscription)

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

De uitvoer vat het abonnement samen. In het onderstaande voorbeelduitvoer staat de samengevatte naleving onder **value.results.nonCompliantResources** en **value.results.nonCompliantPolicies**. Deze aanvraag bevat meer details, inclusief elke toewijzing die bestond uit de niet-conforme nummers en de definitie-informatie voor elke toewijzing. Elk beleidsobject in de hiërarchie biedt een **queryResultsUri** die kan worden gebruikt om extra details op dat niveau te krijgen.

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

### <a name="query-for-resources"></a>Query voor resources

In het bovenstaande voorbeeld biedt **value.policyAssignments.policyDefinitions.results.queryResultsUri** een voorbeeld van Uri voor alle niet-compatibele resources voor een specifieke beleidsdefinitie. Kijkend naar de **$filter** waarde is IsCompliant gelijk (eq) aan false, PolicyAssignmentId is opgegeven voor de beleidsdefinitie en vervolgens de PolicyDefinitionId zelf. De reden voor het opnemen van de PolicyAssignmentId in het filter is omdat de PolicyDefinitionId zou kunnen bestaan in verschillende beleids- of initiatieftoewijzingen met verschillende scopes. Door zowel de PolicyAssignmentId als de PolicyDefinitionId op te geven, kunnen we expliciet zijn in de resultaten die we zoeken. Voorheen gebruikten we voor PolicyStates **de laatste**, die automatisch een **van** en **naar** tijd venster van de laatste 24-uur.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Het voorbeeld antwoord hieronder is bijgesneden naar een niet-conforme bron voor beknoptheid. De gedetailleerde reactie heeft verschillende gegevens over de resource, het beleid of initiatief en de toewijzing. U ook zien welke toewijzingsparameters zijn doorgegeven aan de beleidsdefinitie.

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

Wanneer een resource wordt gemaakt of bijgewerkt, wordt een beleidsevaluatieresultaat gegenereerd. Resultaten worden _beleidsgebeurtenissen_genoemd. Gebruik de volgende Uri om recente beleidsgebeurtenissen te bekijken die aan het abonnement zijn gekoppeld.

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

Zie het naslagartikel [Azure Policy Events](/rest/api/policy-insights/policyevents) voor meer informatie over het opvragen van beleidsgebeurtenissen.

### <a name="azure-powershell"></a>Azure PowerShell

De Azure PowerShell-module voor Azure Policy is beschikbaar in de PowerShell-galerie als [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Met PowerShellGet u de `Install-Module -Name Az.PolicyInsights` module installeren met behulp van (zorg ervoor dat u de nieuwste [Azure PowerShell](/powershell/azure/install-az-ps) hebt geïnstalleerd):

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

Voorbeeld: het statusoverzicht voor het hoogste toegewezen beleid met het hoogste aantal niet-compatibele resources.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Voorbeeld: De statusrecord voor de meest recent geëvalueerde resource opvragen (standaard is op tijdstempel in aflopende volgorde).

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

Voorbeeld: De gegevens voor alle niet-conforme virtuele netwerkbronnen opvragen.

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

Voorbeeld: gebeurtenissen met betrekking tot niet-compatibele virtuele netwerkbronnen die zijn opgetreden na een specifieke datum.

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

Het veld **PrincipalOid** kan worden gebruikt om een specifieke gebruiker `Get-AzADUser`te krijgen met de Azure PowerShell-cmdlet. Vervang **{principalOid}** door het antwoord dat u krijgt van het vorige voorbeeld.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

Als u een Log `AzureActivity` [Analytics-werkruimte](../../../log-analytics/log-analytics-overview.md) hebt met de Oplossing [Activiteitslogboekanalyse](../../../azure-monitor/platform/activity-log-collect.md) die aan uw abonnement is gekoppeld, kunt `AzureActivity` u ook niet-nalevingsresultaten uit de evaluatiecyclus bekijken met behulp van eenvoudige Kusto-query's en de tabel. Met details in Azure Monitor-logboeken kunnen waarschuwingen worden geconfigureerd om te kijken op niet-naleving.


![Azure-beleidsnaleving met Azure Monitor-logboeken](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Volgende stappen

- Voorbeelden bekijken bij [Azure Policy-voorbeelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Begrijpen hoe [u programmatisch beleid maken.](programmatically-create.md)
- Meer informatie over het [herstellen van niet-conforme resources.](remediate-resources.md)
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)