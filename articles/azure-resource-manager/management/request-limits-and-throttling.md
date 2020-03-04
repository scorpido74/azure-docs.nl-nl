---
title: Limieten en beperking aanvragen
description: Beschrijft hoe u gebruik van beperking met Azure Resource Manager-aanvragen wanneer de limieten voor een abonnement is bereikt.
ms.topic: conceptual
ms.date: 10/26/2019
ms.custom: seodec18
ms.openlocfilehash: 43ccf4f2e8098f6577f18943c4ab4132884b66f2
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251348"
---
# <a name="throttling-resource-manager-requests"></a>Beperking van Resource Manager-aanvragen

In dit artikel wordt beschreven hoe Azure Resource Manager vertragings aanvragen opstuurt. U ziet hoe u het aantal aanvragen kunt bijhouden dat achterblijft voordat de limiet wordt bereikt en hoe u kunt reageren wanneer u de limiet hebt bereikt.

Beperking gebeurt op twee niveaus. Azure Resource Manager beperkt aanvragen voor het abonnement en de Tenant. Als de aanvraag wordt beperkt door de beperkings limieten voor het abonnement en de Tenant, stuurt Resource Manager de aanvraag door naar de resource provider. De resource provider past beperkings limieten toe die zijn afgestemd op de bewerkingen. In de volgende afbeelding ziet u hoe beperking wordt toegepast wanneer een aanvraag van de gebruiker naar Azure Resource Manager en de resource provider wordt verplaatst.

![Aanvraag beperking](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Abonnement-en Tenant limieten

Voor elke bewerking op abonnements niveau en op Tenant niveau gelden beperkingen voor het beperken van limieten. Er zijn abonnements aanvragen die moeten worden door gegeven aan uw abonnement-ID, zoals het ophalen van de resource groepen in uw abonnement. Tenant-aanvragen zijn niet opgenomen uw abonnements-ID, zoals het ophalen van geldige Azure-locaties.

De standaard limieten voor beperking per uur worden weer gegeven in de volgende tabel.

| Bereik | Bewerkingen | Limiet |
| ----- | ---------- | ------- |
| Abonnement | Titel | 12000 |
| Abonnement | Delete | 15.000 |
| Abonnement | Schrijfopdrachten | 1200 |
| Tenant | Titel | 12000 |
| Tenant | Schrijfopdrachten | 1200 |

Deze limieten zijn van toepassing op de beveiligingsprincipal (gebruiker of toepassing) die de aanvragen doet en de abonnements-id of tenant-id. Als uw aanvragen afkomstig zijn van meerdere beveiligingsprincipals, is uw limiet voor het abonnement of de tenant groter dan 12.000 en 1200 per uur.

Deze limieten gelden voor elk exemplaar van Azure Resource Manager. Er zijn meerdere exemplaren in elke Azure-regio en Azure Resource Manager wordt geïmplementeerd voor alle Azure-regio's.  In de praktijk zijn de limieten dus hoger dan deze limieten. De aanvragen van een gebruiker worden meestal verwerkt door verschillende exemplaren van Azure Resource Manager.

## <a name="resource-provider-limits"></a>Limieten van resource provider

Resource providers passen hun eigen beperkings limieten toe. Omdat Resource Manager wordt beperkt door de principal-ID en het exemplaar van Resource Manager, kan de resource provider mogelijk meer aanvragen ontvangen dan de standaard limieten in de vorige sectie.

In deze sectie worden de beperkings limieten voor sommige veelgebruikte resource providers beschreven.

### <a name="storage-throttling"></a>Opslag beperking

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Netwerk beperking

De resource provider micro soft. Network heeft de volgende beperkings limieten:

| Bewerking | Limiet |
| --------- | ----- |
| schrijven/verwijderen (PUT) | 1000 per 5 minuten |
| lezen (GET) | 10000 per 5 minuten |

### <a name="compute-throttling"></a>Reken beperking

Zie [Troubleshooting API Throttle Errors-Compute](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md)(Engelstalig) voor meer informatie over het beperken van limieten voor reken bewerkingen.

Voor het controleren van exemplaren van virtuele machines in een schaalset voor virtuele machines gebruikt u de [Virtual Machine Scale sets bewerkingen](/rest/api/compute/virtualmachinescalesetvms). Gebruik bijvoorbeeld de [virtuele machine Scale set vm's: lijst](/rest/api/compute/virtualmachinescalesetvms/list) met para meters om de energie status van exemplaren van virtuele machines te controleren. Deze API vermindert het aantal aanvragen.

### <a name="azure-resource-graph-throttling"></a>Beperking van Azure-resource grafiek

[Azure-resource grafiek](../../governance/resource-graph/overview.md) beperkt het aantal aanvragen voor de bewerkingen. De stappen in dit artikel om de resterende aanvragen te bepalen en te reageren wanneer de limiet is bereikt, geldt ook voor de resource grafiek. Resource grafiek stelt echter een eigen limiet en opnieuw ingestelde frequentie in. Zie [resource Graph Throttle headers](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers)(Engelstalig) voor meer informatie.

## <a name="request-increase"></a>Toename aanvragen

Soms kunnen beperkings limieten worden verhoogd. Als u wilt weten of de beperkings limieten voor uw scenario kunnen worden verhoogd, maakt u een ondersteunings aanvraag. De details van het aanroepende patroon worden geëvalueerd.

## <a name="error-code"></a>Foutcode

Wanneer u de limiet bereikt, ontvangt u de HTTP-status code **429 te veel aanvragen**. Het antwoord bevat een **nieuwe waarde voor opnieuw proberen** , waarmee het aantal seconden wordt aangegeven dat de toepassing moet wachten (of slaap stand) voordat de volgende aanvraag wordt verzonden. Als u een aanvraag verzendt voordat de waarde voor opnieuw proberen is verstreken, wordt uw aanvraag is niet verwerkt en wordt een nieuwe waarde voor opnieuw proberen wordt geretourneerd.

Nadat u het opgegeven tijdstip hebt gewacht, kunt u de verbinding met Azure ook sluiten en opnieuw openen. Door de verbinding opnieuw in te stellen, kunt u verbinding maken met een ander exemplaar van Azure Resource Manager.

Als u een Azure SDK gebruikt, kan de SDK een automatische configuratie voor opnieuw proberen hebben. Zie [richt lijnen voor opnieuw proberen voor Azure-Services](/azure/architecture/best-practices/retry-service-specific)voor meer informatie.

Sommige resource providers retour neren 429 om een tijdelijk probleem te melden. Het probleem kan een overbelasting voor waarde zijn die niet rechtstreeks wordt veroorzaakt door uw aanvraag. Het kan ook duiden op een tijdelijke fout over de status van de doel resource of afhankelijke resource. De netwerk resource provider retourneert bijvoorbeeld 429 met de fout code **RetryableErrorDueToAnotherOperation** wanneer de doel resource is vergrendeld door een andere bewerking. Bekijk de fout details in het antwoord om te bepalen of de fout afkomstig is van beperking of een tijdelijke voor waarde.

## <a name="remaining-requests"></a>Overige aanvragen

U kunt het aantal resterende aanvragen bepalen door onderzoeken antwoordheaders. Lees aanvragen retour neren een waarde in de koptekst voor het aantal resterende Lees aanvragen. Schrijf aanvragen bevatten een waarde voor het aantal resterende schrijf aanvragen. De volgende tabel beschrijft de reactieheaders die u voor deze waarden kunt bekijken:

| Reactieheader | Beschrijving |
| --- | --- |
| x-MS-ratelimit-Remaining-Subscription-reads |Abonnement binnen het bereik van leest de resterende. Deze waarde wordt geretourneerd voor leesbewerkingen. |
| x-MS-ratelimit-Remaining-Subscription-Writes |Abonnement binnen het bereik van schrijft resterende. Deze waarde wordt geretourneerd op schrijfbewerkingen. |
| x-MS-ratelimit-Remaining-tenant-reads |Tenant binnen het bereik van leest resterend |
| x-MS-ratelimit-Remaining-tenant-Writes |Tenant binnen het bereik van schrijft resterend |
| x-MS-ratelimit-Remaining-Subscription-resource-Requests |Abonnement binnen het bereik van aanvragen van het type resource resterende.<br /><br />Deze headerwaarde wordt alleen geretourneerd als een service de standaardlimiet is opgeheven. Resource Manager wordt deze waarde in plaats van het abonnement lees- of schrijfbewerkingen toegevoegd. |
| x-MS-ratelimit-Remaining-Subscription-resource-ENTITIES-Read |Abonnement binnen het bereik van type verzameling resourceaanvragen resterende.<br /><br />Deze headerwaarde wordt alleen geretourneerd als een service de standaardlimiet is opgeheven. Deze waarde geeft het aantal resterende verzameling aanvragen (lijst met resources). |
| x-MS-ratelimit-Remaining-tenant-resource-Requests |Tenant binnen het bereik van aanvragen van het type resource resterende.<br /><br />Deze header is alleen voor aanvragen op tenantniveau toegevoegd en alleen als een service heeft de standaardlimiet overschreven. Resource Manager wordt deze waarde in plaats van de tenant lees- of schrijfbewerkingen toegevoegd. |
| x-MS-ratelimit-Remaining-tenant-resource-ENTITIES-Read |Type verzameling resourceaanvragen resterende binnen het bereik van tenant.<br /><br />Deze header is alleen voor aanvragen op tenantniveau toegevoegd en alleen als een service heeft de standaardlimiet overschreven. |

De resource provider kan ook antwoord headers retour neren met informatie over de resterende aanvragen. Voor informatie over reactie headers die door de compute resource provider worden geretourneerd, Zie [aanroep frequentie informatie-antwoord headers](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Bij het ophalen van de waarden van de koptekst

Bij het ophalen van deze headerwaarden in uw code of het script is niet anders dan bij het ophalen van een headerwaarde. 

In **C#** kunt u bijvoorbeeld de header waarde van een **HttpWebResponse** -object met de naam **Response** ophalen met de volgende code:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

In **Power shell**haalt u de waarde van de header op uit een bewerking invoke-webaanvraag.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Zie [limieten voor Resource Manager voor een abonnement controleren](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)voor een volledig Power shell-voor beeld.

Als u de resterende aanvragen voor fout opsporing wilt zien, kunt u de para meter **-debug** opgeven voor uw **Power shell** -cmdlet.

```powershell
Get-AzResourceGroup -Debug
```

Die veel waarden, met inbegrip van de waarde van het volgende antwoord geretourneerd:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Als u limieten voor schrijven, gebruikt u een schrijfbewerking: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Die veel waarden, met inbegrip van de volgende waarden geretourneerd:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

In **Azure cli**haalt u de waarde van de header op met behulp van de uitgebreidere optie.

```azurecli
az group list --verbose --debug
```

Die veel waarden, met inbegrip van de volgende waarden geretourneerd:

```output
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Als u limieten voor schrijven, gebruikt u een schrijfbewerking: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Die veel waarden, met inbegrip van de volgende waarden geretourneerd:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Volgende stappen

* Zie [limieten voor Resource Manager voor een abonnement controleren](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)voor een volledig Power shell-voor beeld.
* Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over limieten en quota's.
* Zie [asynchrone Azure-bewerkingen volgen](async-operations.md)voor meer informatie over het verwerken van asynchrone rest-aanvragen.
