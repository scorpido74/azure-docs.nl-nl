---
title: Azure-opslagbronnen weergeven met C++-clientbibliotheek
description: Meer informatie over het gebruik van de vermeldings-API's in microsoft Azure Storage Client Library for C++ om containers, blobs, wachtrijen, tabellen en entiteiten op te sommen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: dineshm
ms.openlocfilehash: 0f9e80aff20c1b2663491f6d6ceb99aaec58230f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74269451"
---
# <a name="list-azure-storage-resources-in-c"></a>Azure Storage-resources in C++ weergeven

Aanbiedingsbewerkingen zijn de sleutel tot veel ontwikkelingsscenario's met Azure Storage. In dit artikel wordt beschreven hoe u objecten in Azure Storage het meest efficiënt opsommen met behulp van de vermeldings-API's die zijn opgenomen in de Microsoft Azure Storage Client Library voor C++.

> [!NOTE]
> Deze handleiding is gericht op de Azure Storage Client Library voor C++ versie 2.x, die beschikbaar is via [NuGet](https://www.nuget.org/packages/wastorage) of [GitHub.](https://github.com/Azure/azure-storage-cpp)

De opslagclientbibliotheek biedt verschillende methoden om objecten in Azure Storage weer te geven of op te vragen. In dit artikel worden de volgende scenario's ingegaan:

* Containers in een account weergeven
* Blobs in een container of virtuele blobmap weergeven
* Wachtrijen in een account weergeven
* Tabellen in een account weergeven
* Queryentiteiten in een tabel

Elk van deze methoden wordt weergegeven met behulp van verschillende overbelasting voor verschillende scenario's.

## <a name="asynchronous-versus-synchronous"></a>Asynchroon versus synchroon

Omdat de opslagclientbibliotheek voor C++ is gebouwd bovenop de [C++ REST-bibliotheek,](https://github.com/Microsoft/cpprestsdk)ondersteunen we inherent asynchrone bewerkingen met behulp van [pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Bijvoorbeeld:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Synchrone bewerkingen wikkelen de bijbehorende asynchrone bewerkingen:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Als u met meerdere threading-toepassingen of -services werkt, raden we u aan de async-API's rechtstreeks te gebruiken in plaats van een thread te maken om de synchronisatie-API's aan te roepen, wat een aanzienlijke invloed heeft op uw prestaties.

## <a name="segmented-listing"></a>Gesegmenteerde aanbieding

De schaal van cloudopslag vereist een gesegmenteerde vermelding. U bijvoorbeeld meer dan een miljoen blobs in een Azure-blobcontainer of meer dan een miljard entiteiten in een Azure-tabel hebben. Dit zijn geen theoretische getallen, maar echte klantgebruikscases.

Het is daarom onpraktisch om alle objecten in één reactie op te sommen. In plaats daarvan u objecten aanbieden met paging. Elk van de vermelding API's heeft een *gesegmenteerde* overbelasting.

Het antwoord voor een gesegmenteerde aanbiedingsbewerking omvat:

* *_segment*, die de set resultaten bevat die voor één aanroep naar de lijst-API zijn geretourneerd.
* *continuation_token*, die wordt doorgegeven aan de volgende oproep om de volgende pagina met resultaten te krijgen. Wanneer er geen resultaten meer zijn om terug te keren, is het vervolgtoken null.

Een typische aanroep om alle blobs in een container weer te geven, kan er bijvoorbeeld uitzien als het volgende codefragment. De code is beschikbaar in onze [monsters:](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp)

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Houd er rekening mee dat het aantal resultaten dat op een pagina wordt geretourneerd, kan worden beheerd door de parameter *max_results* in de overbelasting van elke API, bijvoorbeeld:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Als u de *parameter max_results* niet opgeeft, wordt de standaardmaximale waarde van maximaal 5000 resultaten op één pagina geretourneerd.

Houd er ook rekening mee dat een query tegen Azure Table-opslag geen records of minder records kan retourneren dan de waarde van de *parameter max_results* die u hebt opgegeven, zelfs als het vervolgtoken niet leeg is. Een reden zou kunnen zijn dat de query niet kon voltooien in vijf seconden. Zolang het vervolgtoken niet leeg is, moet de query worden voortgezet en mag uw code niet uitgaan van de grootte van de segmentresultaten.

Het aanbevolen coderingspatroon voor de meeste scenario's is een gesegmenteerde vermelding, die expliciete voortgang van vermelding of query's biedt en hoe de service op elke aanvraag reageert. Met name voor C++-toepassingen of -services kan lagere controle over de voortgang van de vermelding helpen het geheugen en de prestaties te beheren.

## <a name="greedy-listing"></a>Hebzuchtige aanbieding

Eerdere versies van de opslagclientbibliotheek voor C++ (versies 0.5.0 Preview en eerder) bevatten niet-gesegmenteerde aanbiedings-API's voor tabellen en wachtrijen, zoals in het volgende voorbeeld:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Deze methoden werden geïmplementeerd als wrappers van gesegmenteerde API's. Voor elke reactie van gesegmenteerde vermelding, de code toegevoegd de resultaten aan een vector en keerde alle resultaten na de volledige containers werden gescand.

Deze aanpak kan werken wanneer het opslagaccount of de opslagtabel een klein aantal objecten bevat. Echter, met een toename van het aantal objecten, het vereiste geheugen kan toenemen zonder limiet, omdat alle resultaten bleef in het geheugen. Een aanbieding operatie kan een zeer lange tijd duren, waarin de beller had geen informatie over de voortgang ervan.

Deze hebzuchtige vermelding API's in de SDK bestaan niet in C#, Java of de JavaScript Node.js-omgeving. Om de mogelijke problemen van het gebruik van deze hebzuchtige API's te voorkomen, hebben we ze verwijderd in versie 0.6.0 Preview.

Als uw code deze hebzuchtige API's roept:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Vervolgens moet u uw code wijzigen om de gesegmenteerde aanbiedings-API's te gebruiken:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Door de *max_results* parameter van het segment op te geven, u de aantallen aanvragen en het geheugengebruik in evenwicht brengen om aan prestatieoverwegingen voor uw toepassing te voldoen.

Als u gesegmenteerde aanbiedings-API's gebruikt, maar de gegevens in een lokale verzameling opslaat in een "hebzuchtige" stijl, raden we u ook ten zeerste aan uw code te refactoren om gegevens in een lokale verzameling zorgvuldig op schaal te verwerken.

## <a name="lazy-listing"></a>Luie aanbieding

Hoewel hebzuchtige lijst potentiële problemen opgeworpen, is het handig als er niet te veel objecten in de container.

Als u ook C# of Oracle Java SDKs gebruikt, moet u bekend zijn met het Enumerable-programmeermodel, dat een aanbieding in luie stijl biedt, waarbij de gegevens op een bepaalde verschuiving alleen worden opgehaald als dit vereist is. In C++ biedt de op iterator gebaseerde sjabloon ook een vergelijkbare aanpak.

Een typische lazy listing API, met **list_blobs** als voorbeeld, ziet er als volgt uit:

```cpp
list_blob_item_iterator list_blobs() const;
```

Een typisch codefragment dat het patroon van luie vermeldingen gebruikt, ziet er mogelijk als volgt uit:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Houd er rekening mee dat lazy listing alleen beschikbaar is in synchrone modus.

In vergelijking met hebzuchtige aanbieding, lazy listing haalt gegevens alleen wanneer dat nodig is. Onder de covers haalt het alleen gegevens uit Azure Storage op wanneer de volgende iterator naar het volgende segment gaat. Daarom wordt het geheugengebruik gecontroleerd met een begrensde grootte en is de bewerking snel.

Lazy listing API's zijn opgenomen in de Storage Client Library voor C++ in versie 2.2.0.

## <a name="conclusion"></a>Conclusie

In dit artikel bespraken we verschillende overbelastingen voor het vermelden van API's voor verschillende objecten in de Opslagclientbibliotheek voor C++ . Samenvattend:

* Async API's worden sterk aanbevolen onder meerdere threading scenario's.
* Gesegmenteerde aanbieding wordt aanbevolen voor de meeste scenario's.
* Lazy listing is voorzien in de bibliotheek als een handige wrapper in synchrone scenario's.
* Hebzuchtige vermelding wordt niet aanbevolen en is verwijderd uit de bibliotheek.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over Azure Storage en Client Library voor C++.

* [Blob-opslag van C++ gebruiken](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Tabelopslag gebruiken vanuit C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Queue Storage gebruiken met C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Azure Storage Client Library voor C++ API-documentatie.](https://azure.github.io/azure-storage-cpp/)
* [Blog van azure-opslagteam](https://blogs.msdn.com/b/windowsazurestorage/)
* [Documentatie bij Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
