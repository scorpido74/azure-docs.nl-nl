---
title: Veelvoorkomende problemen oplossen
description: Meer informatie over het oplossen van problemen met de verschillende SDK's tijdens het opvragen van Azure-bronnen met Azure Resource Graph.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74303907"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Fouten oplossen met Azure Resource Graph

U fouten tegenkomen bij het opvragen van Azure-bronnen met Azure Resource Graph. In dit artikel worden verschillende fouten beschreven die kunnen optreden en hoe u deze oplossen.

## <a name="finding-error-details"></a>Foutgegevens zoeken

De meeste fouten zijn het gevolg van een probleem tijdens het uitvoeren van een query met Azure Resource Graph. Wanneer een query mislukt, geeft de SDK details over de mislukte query. Deze informatie geeft het probleem aan, zodat het kan worden opgelost en een latere query slaagt.

## <a name="general-errors"></a>Algemene fouten

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Scenario: Te veel abonnementen

#### <a name="issue"></a>Probleem

Klanten met toegang tot meer dan 1000 abonnementen, waaronder abonnementen met meerdere tenants met [Azure Lighthouse,](../../../lighthouse/overview.md)kunnen geen gegevens ophalen voor alle abonnementen in één gesprek met Azure Resource Graph.

#### <a name="cause"></a>Oorzaak

Azure CLI en PowerShell sturen alleen de eerste 1000 abonnementen naar Azure Resource Graph. De REST API voor Azure Resource Graph accepteert een maximum aantal abonnementen om de query op uit te voeren.

#### <a name="resolution"></a>Oplossing

Batchaanvragen voor de query met een subset van abonnementen om onder de limiet van 1000 abonnementen te blijven. De oplossing is het gebruik van de parameter **Abonnement** in PowerShell.

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

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Scenario: Rest-header van inhoudstype niet-ondersteund

#### <a name="issue"></a>Probleem

Klanten die de Azure Resource Graph REST API bevragen, krijgen een reactie van _500_ (Interne serverfout) geretourneerd.

#### <a name="cause"></a>Oorzaak

De Azure Resource Graph REST `Content-Type` API ondersteunt alleen een **van toepassing/json**. Sommige REST-hulpprogramma's of agents standaard **tekst/effen,** die niet wordt ondersteund door de REST API.

#### <a name="resolution"></a>Oplossing

Valideren of de tool of agent die u gebruikt om `Content-Type` Azure Resource Graph op te vragen, de REST API-header heeft geconfigureerd voor **toepassing/json**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Scenario: Geen leestoestemming voor alle abonnementen in de lijst

#### <a name="issue"></a>Probleem

Klanten die expliciet slagen voor een lijst met abonnementen met een Azure Resource Graph-query, krijgen een antwoord van _403_ (Verboden).

#### <a name="cause"></a>Oorzaak

Als de klant geen leestoestemming heeft voor alle verstrekte abonnementen, wordt het verzoek geweigerd wegens gebrek aan geschikte beveiligingsrechten.

#### <a name="resolution"></a>Oplossing

Neem ten minste één abonnement op in de abonnementslijst waartoe de klant die de query uitvoert, ten minste leestoegang heeft. Zie [Machtigingen in Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
- Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met – het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
- Als u meer hulp nodig hebt, u een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.