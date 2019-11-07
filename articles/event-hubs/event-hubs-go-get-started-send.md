---
title: 'Quick Start: gebeurtenissen verzenden en ontvangen met Go-Azure Event Hubs'
description: 'Snelstartgids: in dit artikel vindt u een overzicht van het maken van een go-toepassing die gebeurtenissen vanuit Azure Event Hubs verzendt.'
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: e5f52d0ddbf9a66d974732d6d98ca8a5b09cc2d0
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720583"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Snelstartgids: gebeurtenissen verzenden naar of ontvangen van Event Hubs met behulp van Go
Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze zelf studie wordt beschreven hoe u go-toepassingen schrijft voor het verzenden van gebeurtenissen naar of het ontvangen van gebeurtenissen van een Event Hub. 

> [!NOTE]
> U kunt deze snelstart als voorbeeld downloaden van de [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), de tekenreeksen `EventHubConnectionString` en `EventHubName` vervangen door uw event hub-waarden en deze uitvoeren. U kunt ook de stappen in deze zelfstudie volgen om uw eigen oplossing te maken.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- Go lokaal geïnstalleerd. Volg [deze instructies](https://golang.org/doc/install) als dat nodig is.
- Een actief Azure-account. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][] aan voordat u begint.
- **Een event hubs naam ruimte en een event hub maken**. Gebruik de [Azure Portal](https://portal.azure.com) om een naam ruimte van het type Event hubs te maken en de beheer referenties te verkrijgen die uw toepassing nodig heeft om met de Event hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken.

## <a name="send-events"></a>Gebeurtenissen verzenden
In deze sectie wordt beschreven hoe u een go-toepassing maakt om gebeurtenissen naar een Event Hub te verzenden. 

### <a name="install-go-package"></a>Go-pakket installeren

Down load het Go-pakket voor Event Hubs met `go get` of `dep`. Bijvoorbeeld:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Pakketten in het code bestand importeren

Als u de go-pakketten wilt importeren, gebruikt u het volgende code voorbeeld:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Een service-principal maken

Maak een nieuwe Service-Principal door de instructies in [Create a Azure Service Principal with Azure CLI 2,0](/cli/azure/create-an-azure-service-principal-azure-cli)te volgen. Sla de gegeven referenties op in uw omgeving met de volgende namen. Zowel de Azure SDK voor Go als de Event Hubs pakketten zijn vooraf geconfigureerd om te zoeken naar deze namen van variabelen:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Maak nu een autorisatie provider voor uw Event Hubs-client die gebruikmaakt van deze referenties:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Event Hubs-client maken

Met de volgende code wordt een Event Hubs-client gemaakt:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Code schrijven om berichten te verzenden

Gebruik in het volgende code fragment (1) om berichten interactief te verzenden vanuit een Terminal of (2) om berichten te verzenden binnen uw programma:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Extra's

Haal de Id's van de partities op in uw Event Hub:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Voer de toepassing uit om gebeurtenissen naar het Event Hub te verzenden. 

Gefeliciteerd! U hebt nu berichten verzonden naar een Event Hub.

## <a name="receive-events"></a>Gebeurtenissen ontvangen

### <a name="create-a-storage-account-and-container"></a>Een opslag account en een container maken

De status, zoals leases op partities en controle punten in de gebeurtenis stroom, wordt gedeeld tussen ontvangers die gebruikmaken van een Azure Storage-container. U kunt een opslag account en container maken met de go SDK, maar u kunt er ook een maken door de instructies te volgen in [over Azure Storage-accounts](../storage/common/storage-create-storage-account.md).

Voor beelden voor het maken van opslag artefacten met de go-SDK zijn beschikbaar in de Go-voor [beelden opslag plaats](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) en in het voor beeld dat overeenkomt met deze zelf studie.

### <a name="go-packages"></a>Go-pakketten

Als u de berichten wilt ontvangen, kunt u de go-pakketten ophalen voor Event Hubs met `go get` of `dep`:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Pakketten in het code bestand importeren

Als u de go-pakketten wilt importeren, gebruikt u het volgende code voorbeeld:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Een service-principal maken

Maak een nieuwe Service-Principal door de instructies in [Create a Azure Service Principal with Azure CLI 2,0](/cli/azure/create-an-azure-service-principal-azure-cli)te volgen. Sla de gegeven referenties op in uw omgeving met de volgende namen: zowel Azure SDK voor Go als Event Hubs pakket zijn vooraf geconfigureerd om te zoeken naar deze namen van variabelen.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Maak vervolgens een autorisatie provider voor uw Event Hubs-client die gebruikmaakt van deze referenties:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Struct van meta gegevens ophalen

Maak een struct met meta gegevens over uw Azure-omgeving met behulp van de Azure go-SDK. Latere bewerkingen gebruiken deze struct om juiste eind punten te vinden.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Referentie hulpprogramma maken 

Maak een referentie helper die gebruikmaakt van de referenties van de vorige Azure Active Directory (AAD) om een Shared Access Signature (SAS)-referentie voor opslag te maken. De laatste para meter vertelt deze constructor om dezelfde omgevings variabelen te gebruiken zoals eerder gebruikt:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Een controle verwijzing en een leaser maken 

Maak een **leaser**die verantwoordelijk is voor het leasen van een partitie aan een bepaalde ontvanger en een **controle aanwijzer**, die verantwoordelijk is voor het schrijven van controle punten voor de berichten stroom, zodat andere ontvangers kunnen beginnen met het lezen van de juiste offset.

Op dit moment is er één **StorageLeaserCheckpointer** beschikbaar die dezelfde opslag container gebruikt voor het beheren van zowel leases als controle punten. Naast het opslag account en de namen van de containers heeft de **StorageLeaserCheckpointer** de referentie nodig die in de vorige stap is gemaakt en de Azure-omgevings struct om op de juiste wijze toegang te krijgen tot de container.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Event Processor Host maken

U hebt nu de volgende onderdelen nodig om een EventProcessorHost te maken. Dezelfde **StorageLeaserCheckpointer** wordt gebruikt als een leaser en een controle, zoals eerder is beschreven:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>Handler maken 

Maak nu een handler en Registreer deze bij de host van de gebeurtenis processor. Wanneer de host wordt gestart, worden deze en andere opgegeven handlers toegepast op inkomende berichten:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>Code schrijven om berichten te ontvangen

Als alles is ingesteld, kunt u de host van de gebeurtenis processor starten met `Start(context)` zodat deze permanent wordt uitgevoerd, of met `StartNonBlocking(context)` alleen worden uitgevoerd zolang er berichten beschikbaar zijn.

Deze zelf studie wordt gestart en wordt als volgt uitgevoerd: Zie het GitHub-voor beeld voor een voorbeeld met `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[gratis account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
