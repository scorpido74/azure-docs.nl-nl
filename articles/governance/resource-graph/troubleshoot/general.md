---
title: Veelvoorkomende problemen oplossen
description: Meer informatie over het oplossen van problemen met het uitvoeren van query's op Azure-resources met Azure resource Graph.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: e8f42650265b1ca400731365203408eeb22a4e4c
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958524"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Problemen oplossen met Azure resource Graph

Er kunnen fouten optreden bij het uitvoeren van query's op Azure-resources met Azure resource Graph. In dit artikel worden verschillende fouten beschreven die zich kunnen voordoen en hoe u deze kunt oplossen.

## <a name="finding-error-details"></a>Fout details zoeken

De meeste fouten zijn het resultaat van een probleem bij het uitvoeren van een query met Azure resource Graph. Wanneer een query mislukt, biedt de SDK Details over de mislukte query. Deze informatie geeft het probleem aan zodat het kan worden hersteld en een latere query kan worden uitgevoerd.

## <a name="general-errors"></a>Algemene fouten

### <a name="toomanysubscription"></a>Scenario: te veel abonnementen

#### <a name="issue"></a>Probleem

Klanten met toegang tot meer dan 1000 abonnementen, waaronder cross-Tenant abonnementen met [Azure Lighthouse](../../../lighthouse/overview.md), kunnen geen gegevens ophalen over alle abonnementen in één aanroep naar een Azure-resource grafiek.

#### <a name="cause"></a>Oorzaak

Alleen de eerste 1000 abonnementen op Azure resource Graph worden door Azure CLI en Power shell doorgestuurd. De REST API voor de resource grafiek van Azure accepteert een maximum aantal abonnementen voor het uitvoeren van de query.

#### <a name="resolution"></a>Oplossing

Batch-aanvragen voor de query met een subset van abonnementen die blijven onder de limiet van 1000-abonnementen. De oplossing maakt gebruik van de **abonnements** parameter in Power shell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="rest-contenttype"></a>Scenario: niet-ondersteunde content-type REST header

#### <a name="issue"></a>Probleem

Klanten die een query uitvoeren op de Azure-resource grafiek REST API een _500_ -reactie ontvangen (interne server fout).

#### <a name="cause"></a>Oorzaak

De Azure resource Graph-REST API ondersteunt alleen een `Content-Type` van **Application/JSON**. Sommige REST-hulpprogram ma's of-agents worden standaard ingesteld op **tekst/normaal**, wat niet wordt ondersteund door de rest API.

#### <a name="resolution"></a>Oplossing

Controleer of het hulp programma of de agent die u gebruikt om een query uit te zoeken op Azure resource Graph, de REST API-header heeft `Content-Type` geconfigureerd voor **toepassing/JSON**.

### <a name="rest-403"></a>Scenario: geen lees machtiging voor alle abonnementen in de lijst

#### <a name="issue"></a>Probleem

Klanten die expliciet een lijst met abonnementen door geven met een query van een Azure-resource grafiek, krijgen een antwoord van _403_ (verboden).

#### <a name="cause"></a>Oorzaak

Als de klant geen lees machtiging heeft voor alle aangevraagde abonnementen, wordt de aanvraag geweigerd omdat de juiste beveiligings rechten ontbreken.

#### <a name="resolution"></a>Oplossing

Neem ten minste één abonnement op in de lijst met abonnementen die de klant die de query uitvoert, ten minste lees toegang heeft tot. Zie [machtigingen in azure resource Graph](../overview.md#permissions-in-azure-resource-graph)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
- Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure-account voor het verbeteren van de gebruikerservaring door de Azure-community in contact te brengen met de juiste resources: antwoorden, ondersteuning en experts.
- Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.