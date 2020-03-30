---
title: Resourcebestanden maken en gebruiken - Azure Batch
description: Meer informatie over het maken van Batch-bronbestanden uit verschillende invoerbronnen. Dit artikel behandelt een paar veelvoorkomende methoden voor het maken en plaatsen van deze methoden op een vm.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 03/18/2020
ms.author: labrenne
ms.openlocfilehash: 0fe859ac30e7b8050d1f4688d7cf106a465e7566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531138"
---
# <a name="creating-and-using-resource-files"></a>Resourcebestanden maken en gebruiken

Voor een Azure Batch-taak is vaak een bepaalde vorm van gegevens vereist om te verwerken. Bronbestanden zijn de manier om deze gegevens via een taak aan uw virtuele batchmachine (VM) te verstrekken. Alle soorten taken ondersteunen bronbestanden: taken, starttaken, taakvoorbereidingstaken, taakreleasetaken, enz. Dit artikel behandelt een paar veelvoorkomende methoden voor het maken van bronbestanden en deze op een vm.  

Bronbestanden zetten gegevens op een VM in Batch, maar het type gegevens en de manier waarop deze worden gebruikt, is flexibel. Er zijn echter enkele veelvoorkomende use cases:

1. Algemene bestanden op elke virtuele machine inrichten met behulp van bronbestanden in een starttaak
1. Inputgegevens inrichten die door taken moeten worden verwerkt

Veelgebruikte bestanden kunnen bijvoorbeeld bestanden zijn op een starttaak die wordt gebruikt om toepassingen te installeren die uw taken uitvoeren. Invoergegevens kunnen ruwe afbeeldings- of videogegevens zijn, of gegevens die door Batch moeten worden verwerkt.

## <a name="types-of-resource-files"></a>Typen bronbestanden

Er zijn een paar verschillende opties beschikbaar om bronbestanden te genereren. Het aanmaakproces voor bronbestanden is afhankelijk van waar de oorspronkelijke gegevens worden opgeslagen.

Opties voor het maken van een bronbestand:

- [URL van opslagcontainer:](#storage-container-url)genereert een bronbestand uit een opslagcontainer in Azure
- [Naam van opslagcontainer:](#storage-container-name)genereert een bronbestand op basis van de naam van een container in een Azure-opslagaccount dat is gekoppeld aan Batch
- [Webeindpunt:](#web-endpoint)genereert een bronbestand van een geldige HTTP-URL

### <a name="storage-container-url"></a>URL van opslagcontainer

Met behulp van een URL voor opslagcontainers u met de juiste machtigingen toegang krijgen tot bestanden in elke opslagcontainer in Azure. 

In dit c#-voorbeeld zijn de bestanden al geüpload naar een Azure-opslagcontainer als blobopslag. Om toegang te krijgen tot de gegevens die nodig zijn om een bronbestand te maken, moeten we eerst toegang krijgen tot de opslagcontainer.

Maak een SAS-uri (Shared Access Signature) met de juiste machtigingen voor toegang tot de opslagcontainer. Stel de vervaldatum en machtigingen voor de SAS in. In dit geval wordt geen begintijd opgegeven, dus de SAS wordt onmiddellijk geldig en vervalt twee uur nadat deze is gegenereerd.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Voor containertoegang moet u `Read` `List` beide en machtigingen hebben, terwijl `Read` u met blobtoegang alleen toestemming nodig hebt.

Zodra de machtigingen zijn geconfigureerd, maakt u het SAS-token en maakt u de SAS-URL op voor toegang tot de opslagcontainer. Met de opgemaakte SAS-URL voor de opslagcontainer genereert u een bronbestand met [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Een alternatief voor het genereren van een SAS-URL is het inschakelen van anonieme, openbare leestoegang tot een container en de blobs in Azure Blob-opslag. Op deze basis u alleen-lezen toegang verlenen tot deze bronnen zonder uw accountsleutel te delen en zonder dat u een SAS nodig hebt. Openbare leestoegang wordt meestal gebruikt voor scenario's waarin u wilt dat bepaalde blobs altijd beschikbaar zijn voor anonieme leestoegang. Als dit scenario bij uw oplossing past, raadpleegt u het artikel [Anonieme toegang tot blobs](../storage/blobs/storage-manage-access-to-resources.md) voor meer informatie over het beheren van toegang tot uw blobgegevens.

### <a name="storage-container-name"></a>Naam opslagcontainer

In plaats van een SAS-URL te configureren en te maken, u de naam van uw Azure-opslagcontainer gebruiken om toegang te krijgen tot uw blobgegevens. De opslagcontainer die u gebruikt, moet zich bevinden in het Azure-opslagaccount dat is gekoppeld aan uw Batch-account. Dat opslagaccount staat bekend als het autostorage-account. Met de autoopslagcontainer u het configureren en maken van een SAS-URL omzeilen om toegang te krijgen tot een opslagcontainer.

In dit voorbeeld gaan we ervan uit dat de gegevens die moeten worden gebruikt voor het maken van bronbestanden al in een Azure Storage-account zijn gekoppeld aan uw Batch-account. Als u geen autoopslagaccount hebt, raadpleegt u de stappen in [Een Batch-account maken](batch-account-create-portal.md) voor meer informatie over het maken en koppelen van een account.

Door een gekoppeld opslagaccount te gebruiken, hoeft u geen SAS-URL te maken en te configureren voor een opslagcontainer. Geef in plaats daarvan de naam op van de opslagcontainer in uw gekoppelde opslagaccount.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Webeindpunt

Gegevens die niet naar Azure Storage worden geüpload, kunnen nog steeds worden gebruikt om bronbestanden te maken. U een geldige HTTP-URL opgeven die uw invoergegevens bevat. De URL wordt verstrekt aan de Batch-API en vervolgens worden de gegevens gebruikt om een bronbestand te maken.

In het volgende C#-voorbeeld worden de invoergegevens gehost op een fictief GitHub-eindpunt. De API haalt het bestand op uit het geldige webeindpunt en genereert een bronbestand dat door uw taak moet worden verbruikt. Voor dit scenario zijn geen referenties nodig.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Tips en suggesties

Elke Azure Batch-taak gebruikt bestanden anders, daarom heeft Batch opties beschikbaar voor het beheren van bestanden over taken. De volgende scenario's zijn niet bedoeld om uitgebreid te zijn, maar in plaats daarvan betrekking hebben op een paar veelvoorkomende situaties en aanbevelingen te geven.

### <a name="many-resource-files"></a>Veel bronbestanden

Uw batchtaak kan verschillende taken bevatten die allemaal dezelfde algemene bestanden gebruiken. Als algemene taakbestanden worden gedeeld tussen veel taken, kan het gebruik van een toepassingspakket om de bestanden te bevatten in plaats van resourcebestanden te gebruiken, een betere optie zijn. Toepassingspakketten bieden optimalisatie voor downloadsnelheid. Ook gegevens in toepassingspakketten worden opgeslagen tussen taken, dus als uw taakbestanden niet vaak veranderen, kunnen toepassingspakketten geschikt zijn voor uw oplossing. Met toepassingspakketten hoeft u verschillende bronbestanden niet handmatig te beheren of SAS-URL's te genereren om toegang te krijgen tot de bestanden in Azure Storage. Batch werkt op de achtergrond met Azure Storage om toepassingspakketten op te slaan en te implementeren voor compute nodes.

Als elke taak veel bestanden heeft die uniek zijn voor die taak, zijn bronbestanden de beste optie omdat taken die unieke bestanden gebruiken vaak moeten worden bijgewerkt of vervangen, wat niet zo eenvoudig is om te doen met inhoud met toepassingspakketten. Bronbestanden bieden extra flexibiliteit voor het bijwerken, toevoegen of bewerken van afzonderlijke bestanden.

### <a name="number-of-resource-files-per-task"></a>Aantal bronbestanden per taak

Als er enkele honderden bronbestanden zijn opgegeven voor een taak, kan Batch de taak afwijzen als te groot. Het is het beste om uw taken klein te houden door het aantal bronbestanden op de taak zelf te minimaliseren.

Als er geen manier is om het aantal bestanden dat uw taak nodig heeft te minimaliseren, u de taak optimaliseren door één bronbestand te maken dat verwijst naar een opslagcontainer met bronbestanden. Plaats hiervoor uw bronbestanden in een Azure Storage-container en gebruikt de verschillende ['container'-methoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods) voor bronbestanden. Gebruik de blob-voorvoegselopties om verzamelingen op te geven die voor uw taken moeten worden gedownload.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [toepassingspakketten](batch-application-packages.md) als alternatief voor bronbestanden.
- Zie [Containerworkloads](batch-docker-container-workloads.md)voor meer informatie over het gebruik van containers voor resourcebestanden.
- Zie [Taak- en taakuitvoer](batch-task-output.md)blijven uitvoeren voor meer informatie over het verzamelen en opslaan van de uitvoergegevens van uw taken.
- Meer informatie over de [Batch-API's en -hulpprogramma's](batch-apis-tools.md) die beschikbaar zijn voor het bouwen van Batch-oplossingen.
