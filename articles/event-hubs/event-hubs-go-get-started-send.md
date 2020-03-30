---
title: 'Snelstart: gebeurtenissen verzenden en ontvangen met Go - Azure Event Hubs'
description: 'Snelstart: in dit artikel wordt een walkthrough gemaakt voor het maken van een Go-toepassing die gebeurtenissen verzendt vanuit Azure Event Hubs.'
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720583"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Snelstart: gebeurtenissen verzenden naar of ontvangen van evenementen vanuit gebeurtenishubs via Go
Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze zelfstudie wordt beschreven hoe u Go-toepassingen schrijft om gebeurtenissen naar gebeurtenissen te verzenden of te ontvangen vanuit een gebeurtenishub. 

> [!NOTE]
> U kunt deze snelstart als voorbeeld downloaden van de [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), de tekenreeksen `EventHubConnectionString` en `EventHubName` vervangen door uw event hub-waarden en deze uitvoeren. U kunt ook de stappen in deze zelfstudie volgen om uw eigen oplossing te maken.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- Ga lokaal installeren. Volg indien nodig [deze instructies](https://golang.org/doc/install) op.
- Een actief Azure-account. Als u geen Azure-abonnement hebt, maakt u een [gratis account][] voordat u begint.
- **Maak een naamruimte voor gebeurtenishubs en een gebeurtenishub**. Gebruik de [Azure-portal](https://portal.azure.com) om een naamruimte van het type Gebeurtenishubs te maken en de beheerreferenties te verkrijgen die uw toepassing nodig heeft om te communiceren met de gebeurtenishub. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken.

## <a name="send-events"></a>Gebeurtenissen verzenden
In deze sectie ziet u hoe u een Go-toepassing maakt om gebeurtenissen naar een gebeurtenishub te verzenden. 

### <a name="install-go-package"></a>Go-pakket installeren

Ontvang het Go-pakket `go get` voor `dep`eventhubs met of . Bijvoorbeeld:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Pakketten importeren in uw codebestand

Als u de Pakketten Voor Gaan wilt importeren, gebruikt u het volgende codevoorbeeld:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Een service-principal maken

Maak een nieuwe serviceprincipal door de instructies te volgen in [Een Azure-serviceprincipal maken met Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Sla de opgegeven referenties in uw omgeving op met de volgende namen. Zowel de Azure SDK for Go als de Event Hubs-pakketten zijn vooraf geconfigureerd om te zoeken naar deze variabele namen:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Maak nu een autorisatieprovider voor uw Event Hubs-client die deze referenties gebruikt:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Client Gebeurtenishubs maken

Met de volgende code wordt een Client Event Hubs gemaakt:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Code schrijven om berichten te verzenden

Gebruik in het volgende fragment (1) om berichten interactief vanaf een terminal te verzenden of (2) om berichten binnen uw programma te verzenden:

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

Download de gegevens van de partities in uw gebeurtenishub:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Voer de toepassing uit om gebeurtenissen naar de gebeurtenishub te verzenden. 

Gefeliciteerd! U hebt nu berichten verzonden naar een Event Hub.

## <a name="receive-events"></a>Gebeurtenissen ontvangen

### <a name="create-a-storage-account-and-container"></a>Een opslagaccount en -container maken

Status, zoals leases op partities en controlepunten in de gebeurtenisstream, wordt gedeeld tussen ontvangers met behulp van een Azure Storage-container. U een opslagaccount en -container maken met de Go SDK, maar u er ook een maken door de instructies in [Over Azure-opslagaccounts te volgen.](../storage/common/storage-create-storage-account.md)

Voorbeelden voor het maken van storageartefacten met de Go SDK zijn beschikbaar in de [Repo Go-monsters](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) en in het voorbeeld dat overeenkomt met deze zelfstudie.

### <a name="go-packages"></a>Ga pakketten

Als u de berichten wilt ontvangen, ontvangt `go get` `dep`u de Go-pakketten voor gebeurtenishubs met of:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Pakketten importeren in uw codebestand

Als u de Pakketten Voor Gaan wilt importeren, gebruikt u het volgende codevoorbeeld:

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

Maak een nieuwe serviceprincipal door de instructies te volgen in [Een Azure-serviceprincipal maken met Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Sla de opgegeven referenties in uw omgeving op met de volgende namen: Het pakket Azure SDK for Go en Event Hubs is vooraf geconfigureerd om naar deze variabele namen te zoeken.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Maak vervolgens een autorisatieprovider voor uw Event Hubs-client die deze referenties gebruikt:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Metagegevenstruct

Krijg een truc met metagegevens over uw Azure-omgeving met behulp van de Azure Go SDK. Latere bewerkingen gebruiken deze structuur om de juiste eindpunten te vinden.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Referentiehelper maken 

Maak een referentiehelper die de vorige Azure Active Directory (AAD)-referenties gebruikt om een SAS-referentie (Shared Access Signature) voor opslag te maken. De laatste parameter vertelt deze constructor om dezelfde omgevingsvariabelen te gebruiken als eerder werd gebruikt:

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

### <a name="create-a-check-pointer-and-a-leaser"></a>Een controleaanwijzer en een leaser maken 

Maak een **leaser**, verantwoordelijk voor het leasen van een partitie aan een bepaalde ontvanger, en een **check pointer**, verantwoordelijk voor het schrijven van checkpoints voor de berichtstroom, zodat andere ontvangers kunnen beginnen met lezen van de juiste verschuiving.

Momenteel is er één **StorageLeaserCheckpointer** beschikbaar die dezelfde opslagcontainer gebruikt om zowel leases als checkpoints te beheren. Naast de opslagaccount- en containernamen heeft de **StorageLeaserCheckpointer** de referenties nodig die in de vorige stap zijn gemaakt en de Azure-omgeving om de container correct te openen.

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

### <a name="construct-event-processor-host"></a>Host van gebeurtenisprocessor en

Je hebt nu de stukken die nodig zijn om een EventProcessorHost te bouwen, als volgt. Dezelfde **StorageLeaserCheckpointer** wordt gebruikt als zowel leaser als check pointer, zoals eerder beschreven:

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

Maak nu een handler en registreer deze bij de Event Processor Host. Wanneer de host wordt gestart, worden deze en alle andere opgegeven handlers op binnenkomende berichten gebruikt:

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

Als alles is ingesteld, u `Start(context)` de Host van de gebeurtenisprocessor starten om deze permanent te laten draaien, of met `StartNonBlocking(context)` alleen te laten draaien zolang er berichten beschikbaar zijn.

Deze zelfstudie begint en loopt als volgt; zie het GitHub-voorbeeld `StartNonBlocking`voor een voorbeeld met :

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
