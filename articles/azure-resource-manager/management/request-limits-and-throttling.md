---
title: Limieten en beperking aanvragen
description: Beschrijft hoe u beperking gebruiken met Azure Resource Manager-aanvragen wanneer de abonnementslimieten zijn bereikt.
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4d387749261747eb9ea1ea26629ade4fe8729856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239367"
---
# <a name="throttling-resource-manager-requests"></a>Resource Manager-aanvragen beperken

In dit artikel wordt beschreven hoe Azure Resource Manager aanvragen beperkt. Het laat zien hoe je het aantal aanvragen bijhouden dat overblijft voordat je de limiet hebt bereikt en hoe je reageren wanneer je de limiet hebt bereikt.

Throttling gebeurt op twee niveaus. Azure Resource Manager geeft aanvragen voor het abonnement en de tenant een gas. Als de aanvraag onder de beperkingslimieten voor het abonnement en de tenant valt, leidt Resource Manager de aanvraag door naar de resourceprovider. De resourceprovider past beperkingslimieten toe die zijn afgestemd op zijn activiteiten. In de volgende afbeelding ziet u hoe beperking wordt toegepast wanneer een aanvraag van de gebruiker naar Azure Resource Manager en de resourceprovider gaat.

![Beperking aanvragen](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Abonnements- en tenantlimieten

Voor elke bewerking op abonnements- en tenantniveau geldt beperkingslimieten. Abonnementsaanvragen zijn aanvragen die betrekking hebben op het doorgeven van uw abonnements-ID, zoals het ophalen van de brongroepen in uw abonnement. Tenantaanvragen bevatten geen abonnements-id, zoals het ophalen van geldige Azure-locaties.

De standaardbeperkingslimieten per uur worden weergegeven in de volgende tabel.

| Bereik | Bewerkingen | Limiet |
| ----- | ---------- | ------- |
| Abonnement | Leest | 12000 |
| Abonnement | Verwijderd | 15.000 |
| Abonnement | Schrijft | 1200 |
| Tenant | Leest | 12000 |
| Tenant | Schrijft | 1200 |

Deze limieten zijn van toepassing op de beveiligingsprincipal (gebruiker of toepassing) die de aanvragen doet en de abonnements-id of tenant-id. Als uw aanvragen afkomstig zijn van meerdere beveiligingsprincipals, is uw limiet voor het abonnement of de tenant groter dan 12.000 en 1200 per uur.

Deze limieten zijn van toepassing op elk Azure Resource Manager-exemplaar. Er zijn meerdere exemplaren in elke Azure-regio en Azure Resource Manager wordt geïmplementeerd in alle Azure-regio's.  In de praktijk zijn de limieten dus hoger dan deze limieten. De aanvragen van een gebruiker worden meestal verwerkt door verschillende exemplaren van Azure Resource Manager.

## <a name="resource-provider-limits"></a>Limieten voor resourceprovider

Resourceproviders passen hun eigen beperkingslimieten toe. Omdat Resource Manager wordt beperkt door hoofd-id en op instantie van Resource Manager, ontvangt de resourceprovider mogelijk meer aanvragen dan de standaardlimieten in de vorige sectie.

In deze sectie worden de beperkingslimieten van sommige veelgebruikte resourceproviders besproken.

### <a name="storage-throttling"></a>Opslagbeperking

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Netwerkbeperking

De microsoft.network-resourceprovider past de volgende gashendellimieten toe:

| Bewerking | Limiet |
| --------- | ----- |
| schrijven / verwijderen (PUT) | 1000 per 5 minuten |
| lezen (GET) | 10000 per 5 minuten |

### <a name="compute-throttling"></a>Beperking van gegevens

Zie [Api-beperkingsfouten oplossen voor](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md)informatie over beperkingslimieten voor rekenbewerkingen :

Gebruik de [bewerkingen Virtuele machineschaalsets](/rest/api/compute/virtualmachinescalesetvms)voor het controleren van virtuele machine-exemplaren binnen een virtuele machineschaalset. Gebruik bijvoorbeeld de [VM's voor virtuele machineschaalset - Lijst](/rest/api/compute/virtualmachinescalesetvms/list) met parameters om de energiestatus van virtuele machine-exemplaren te controleren. Deze API vermindert het aantal aanvragen.

### <a name="azure-resource-graph-throttling"></a>Beperking van Azure Resource Graph

[Azure Resource Graph](../../governance/resource-graph/overview.md) beperkt het aantal aanvragen voor de bewerkingen. De stappen in dit artikel om de resterende aanvragen te bepalen en hoe te reageren wanneer de limiet is bereikt, zijn ook van toepassing op Resource Graph. Resource Graph stelt echter zijn eigen limiet en resetsnelheid in. Zie [Kopteksten voor beperking van resourcegrafieken](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers)voor meer informatie .

## <a name="error-code"></a>Foutcode

Wanneer u de limiet bereikt, ontvangt u de HTTP-statuscode **429 Te veel aanvragen.** Het antwoord bevat een **waarde Opnieuw proberen-na,** die het aantal seconden aangeeft dat uw toepassing moet wachten (of slapen) voordat het volgende verzoek wordt verzonden. Als u een aanvraag verzendt voordat de waarde voor het opnieuw proberen is verstreken, wordt uw aanvraag niet verwerkt en wordt een nieuwe waarde voor nieuwe poging geretourneerd.

Nadat u hebt gewacht op een bepaalde tijd, u uw verbinding met Azure ook sluiten en opnieuw openen. Door de verbinding opnieuw in te stellen, u verbinding maken met een ander exemplaar van Azure Resource Manager.

Als u een Azure SDK gebruikt, heeft de SDK mogelijk een configuratie voor automatische opnieuw proberen. Zie [Richtlijnen opnieuw proberen voor Azure-services voor](/azure/architecture/best-practices/retry-service-specific)meer informatie.

Sommige resourceproviders retourneren 429 om een tijdelijk probleem te melden. Het probleem kan een overbelastingstoestand zijn die niet direct wordt veroorzaakt door uw aanvraag. Het kan ook een tijdelijke fout betekenen over de status van de doelbron of de afhankelijke resource. De netwerkbronprovider retourneert bijvoorbeeld 429 met de foutcode **RetryableErrorDueToAnotherOperation** wanneer de doelbron is vergrendeld door een andere bewerking. Als u wilt bepalen of de fout afkomstig is van beperking of een tijdelijke voorwaarde, bekijkt u de foutgegevens in het antwoord.

## <a name="remaining-requests"></a>Resterende aanvragen

U het aantal resterende aanvragen bepalen door antwoordkoppen te onderzoeken. Leesaanvragen geven een waarde in de koptekst terug voor het aantal resterende leesaanvragen. Schrijfaanvragen bevatten een waarde voor het aantal resterende schrijfaanvragen. In de volgende tabel worden de antwoordkoppen beschreven die u voor deze waarden onderzoeken:

| Reactiekop | Beschrijving |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Met een abonnement blijft de resterende leest. Deze waarde wordt geretourneerd bij leesbewerkingen. |
| x-ms-ratelimit-remaining-subscription-writes x-ms-ratelimit-remaining-subscription-writes x-ms-ratelimit-remaining-subscription-writes x- |Nog geschreven met abonnementscoped. Deze waarde wordt geretourneerd op schrijfbewerkingen. |
| x-ms-ratelimit-remaining-tenant-reads |Tenant scoped leest resterende |
| x-ms-ratelimit-remaining-tenant-schrijft |Tenant scoped schrijft resterende |
| x-ms-ratelimit-remaining-subscription-resource-requests x-ms-ratelimit-remaining-subscription-resource-requests x-ms-ratelimit-remaining-subscription-resource-requests x- |Aanvragen voor resourcetype met abonnementblijven.<br /><br />Deze kopwaarde wordt alleen geretourneerd als een service de standaardlimiet heeft overschreven. Resource Manager voegt deze waarde toe in plaats van het abonnement leest of schrijft. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Aanvragen voor het verzamelen van resourcetypen met een abonnement met bereik blijven over.<br /><br />Deze kopwaarde wordt alleen geretourneerd als een service de standaardlimiet heeft overschreven. Deze waarde geeft het aantal resterende incassoaanvragen (lijstbronnen). |
| x-ms-ratelimit-remaining-tenant-resource-requests x-ms-ratelimit-remaining-tenant-resource-requests x-ms-ratelimit-remaining-tenant-resource-requests x- |Tenant scoped resource type aanvragen resterende.<br /><br />Deze header wordt alleen toegevoegd voor aanvragen op tenantniveau en alleen als een service de standaardlimiet heeft overschreven. Resource Manager voegt deze waarde toe in plaats van de tenant leest of schrijft. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Er zijn nog resterende resourcetype-aanvragen voor tenantscoped.<br /><br />Deze header wordt alleen toegevoegd voor aanvragen op tenantniveau en alleen als een service de standaardlimiet heeft overschreven. |

De resourceprovider kan ook antwoordkoppen retourneren met informatie over de resterende aanvragen. Zie Informatiekoppen voor antwoordsnelheid voor informatie over antwoordkoppen die door de compute resourceprovider [zijn](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers)geretourneerd.

## <a name="retrieving-the-header-values"></a>De kopwaarden ophalen

Het ophalen van deze kopwaarden in uw code of script is niet anders dan het ophalen van een kopwaarde. 

In **C#** haalt u bijvoorbeeld de kopwaarde op uit een **httpwebresponse** met de naam **response** met de volgende code:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

In **PowerShell**haalt u de kopwaarde op uit een bewerking Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Zie [Resourcemanagerlimieten voor een abonnement controleren voor](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)een volledig PowerShell-voorbeeld.

Als u de resterende aanvragen voor foutopsporing wilt zien, u de parameter **Foutopsporing** op uw **PowerShell-cmdlet** opgeven.

```powershell
Get-AzResourceGroup -Debug
```

Hierdoor worden veel waarden geretourneerd, waaronder de volgende reactiewaarde:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Als u schrijflimieten wilt schrijven, gebruikt u een schrijfbewerking: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Hierdoor worden veel waarden geretourneerd, waaronder de volgende waarden:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

In **Azure CLI**haalt u de kopwaarde op met de optie meer verbose.

```azurecli
az group list --verbose --debug
```

Hierdoor worden veel waarden geretourneerd, waaronder de volgende waarden:

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

Als u schrijflimieten wilt schrijven, gebruikt u een schrijfbewerking: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Hierdoor worden veel waarden geretourneerd, waaronder de volgende waarden:

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

* Zie [Resourcemanagerlimieten voor een abonnement controleren voor](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)een volledig PowerShell-voorbeeld.
* Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over limieten en quota.
* Zie [Asynchrone Azure-bewerkingen bijhouden](async-operations.md)voor meer informatie over het afhandelen van asynchrone REST-aanvragen.
