---
title: BLOB-moment opnamen
titleSuffix: Azure Storage
description: Meer informatie over het maken van een alleen-lezen momentopname van een blob om een back-up van blobgegevens op een bepaald moment in de tijd.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 24118e6ae5c31399ce5d33361dd60e3a08424681
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88055765"
---
# <a name="blob-snapshots"></a>BLOB-moment opnamen

Een moment opname is een alleen-lezen versie van een blob die op een bepaald moment wordt uitgevoerd.

> [!NOTE]
> BLOB-versie beheer (preview) biedt een alternatieve manier om historische kopieën van een BLOB te onderhouden. Zie voor meer informatie [BLOB-versie beheer (preview)](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Over blob-moment opnamen

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Een moment opname van een blob is identiek aan de basis-blob, behalve dat de BLOB-URI een **datum/tijd** -waarde heeft toegevoegd aan de BLOB-URI om het tijdstip aan te geven waarop de moment opname is gemaakt. Als de URI van een pagina-BLOB bijvoorbeeld is `http://storagesample.core.blob.windows.net/mydrives/myvhd` , is de moment opname-URI vergelijkbaar met `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Alle moment opnamen delen de URI van de basis-blob. Het enige verschil tussen de basis-Blob en de moment opname is de toegevoegde **DateTime** -waarde.
>

Een BLOB kan een wille keurig aantal moment opnamen hebben. Moment opnamen blijven behouden totdat ze expliciet worden verwijderd, hetzij onafhankelijk of als onderdeel van de bewerking BLOB verwijderen voor de basis-blob. U kunt de moment opnamen die zijn gekoppeld aan de basis-BLOB opsommen om uw huidige moment opnamen bij te houden.

Wanneer u een moment opname van een BLOB maakt, worden de systeem eigenschappen van de BLOB gekopieerd naar de moment opname met dezelfde waarden. De meta gegevens van de basis-BLOB worden ook gekopieerd naar de moment opname, tenzij u afzonderlijke meta gegevens voor de moment opname opgeeft wanneer u deze maakt. Nadat u een moment opname hebt gemaakt, kunt u deze lezen, kopiëren of verwijderen, maar u kunt deze niet wijzigen.

Alle leases die zijn gekoppeld aan de basis-blob, hebben geen invloed op de moment opname. U kunt geen lease verkrijgen voor een moment opname.

Een VHD-bestand wordt gebruikt voor het opslaan van de huidige informatie en status voor een VM-schijf. U kunt een schijf loskoppelen van in de VM of de virtuele machine afsluiten en vervolgens een moment opname maken van het VHD-bestand. U kunt dit momentopname bestand later gebruiken om het VHD-bestand op dat moment op te halen en de virtuele machine opnieuw te maken.

## <a name="understand-how-snapshots-accrue-charges"></a>Meer informatie over hoe moment opnamen kosten samen voegen

Het maken van een moment opname, een alleen-lezen kopie van een blob, kan leiden tot extra kosten voor gegevens opslag voor uw account. Bij het ontwerpen van uw toepassing is het belang rijk om te weten hoe deze kosten kunnen toenemen, zodat u de kosten kunt minimaliseren.

### <a name="important-billing-considerations"></a>Belang rijke overwegingen met betrekking tot facturering

De volgende lijst bevat belang rijke punten waarmee u rekening moet houden bij het maken van een moment opname.

- In uw opslag account worden kosten in rekening gebracht voor unieke blokken of pagina's, ongeacht of deze zich in de BLOB of in de moment opname bevinden. Voor uw account worden geen extra kosten in rekening gebracht voor moment opnamen die zijn gekoppeld aan een BLOB totdat u de BLOB bijwerkt waarop ze zijn gebaseerd. Nadat u de basis-BLOB hebt bijgewerkt, is deze afwijkend van de moment opnamen. Als dit gebeurt, worden er kosten in rekening gebracht voor de unieke blokken of pagina's in elke BLOB of moment opname.
- Wanneer u een blok in een blok-BLOB vervangt, wordt dat blok vervolgens als een uniek blok in rekening gebracht. Dit geldt ook als het blok dezelfde blok-ID en dezelfde gegevens bevat als in de moment opname. Nadat het blok opnieuw is doorgevoerd, is het afwijkend van de tegen hanger in een moment opname en worden de gegevens in rekening gebracht. Hetzelfde geldt voor een pagina in een pagina-blob die wordt bijgewerkt met identieke gegevens.
- Het vervangen van een blok-BLOB door het aanroepen van de methode [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] of [UploadFromByteArray] [dotnet_UploadFromByteArray] vervangt alle blokken in de blob. Als u een moment opname hebt die is gekoppeld aan die blob, zijn alle blokken in de basis-Blob en snap shot nu afwijkend en worden er kosten in rekening gebracht voor alle blokken in beide blobs. Dit geldt ook als de gegevens in de basis-Blob en de moment opname identiek blijven.
- De Azure-Blob service heeft geen manier om te bepalen of twee blokken identieke gegevens bevatten. Elk blok dat wordt geüpload en doorgevoerd, wordt behandeld als uniek, zelfs als het dezelfde gegevens en dezelfde blok-ID heeft. Omdat kosten toenemen voor unieke blokken, is het belang rijk om te overwegen dat het bijwerken van een blob met een moment opname resulteert in extra unieke blokken en extra kosten.

### <a name="minimize-cost-with-snapshot-management"></a>Kosten minimaliseren met snap shot Management

We raden u aan uw moment opnamen zorgvuldig te beheren om extra kosten te voor komen. U kunt deze aanbevolen procedures volgen om de kosten te minimaliseren die worden gemaakt door de opslag van uw moment opnamen:

- U kunt moment opnamen die zijn gekoppeld aan een blob, verwijderen en opnieuw maken wanneer u de BLOB bijwerkt, zelfs als u een update uitvoert met identieke gegevens, tenzij uw toepassings ontwerp vereist dat u moment opnamen bijhoudt. Door de moment opnamen van de BLOB te verwijderen en opnieuw te maken, kunt u ervoor zorgen dat de BLOB en de moment opnamen niet afwijken.
- Als u moment opnamen voor een BLOB onderhoudt, moet u [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] of [UploadFromByteArray] [dotnet_UploadFromByteArray] niet aanroepen om de BLOB bij te werken. Deze methoden vervangen alle blokken in de blob, waardoor uw basis-Blob en de bijbehorende moment opnamen aanzienlijk afwijken. Werk in plaats daarvan het minste mogelijke aantal blokken bij met behulp van de methoden [PutBlock] [dotnet_PutBlock] en [Putblock list] [dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Facturerings scenario's voor moment opnamen

De volgende scenario's laten zien hoe kosten toenemen voor een blok-Blob en de bijbehorende moment opnamen.

#### <a name="scenario-1"></a>Scenario 1

In scenario 1 is de basis-BLOB niet bijgewerkt nadat de moment opname is gemaakt, dus worden er alleen kosten in rekening gebracht voor unieke blokken 1, 2 en 3.

![Azure Storage resources](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

In scenario 2 is de basis-BLOB bijgewerkt, maar niet de moment opname. Blok 3 is bijgewerkt en hoewel het dezelfde gegevens en dezelfde ID bevat, is dit niet hetzelfde als blok 3 in de moment opname. Als gevolg hiervan wordt het account in rekening gebracht voor vier blokken.

![Azure Storage resources](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

In scenario 3 is de basis-BLOB bijgewerkt, maar niet de moment opname. Blok 3 is vervangen door blok 4 in de basis-blob, maar de moment opname komt nog steeds overeen met blok 3. Als gevolg hiervan wordt het account in rekening gebracht voor vier blokken.

![Azure Storage resources](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

In scenario 4 is de basis-BLOB volledig bijgewerkt en bevat deze geen van de oorspronkelijke blokken. Als gevolg hiervan wordt het account in rekening gebracht voor alle acht unieke blokken. Dit scenario kan zich voordoen als u een update methode gebruikt zoals [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] of [UploadFromByteArray] [dotnet_UploadFromByteArray], omdat deze methoden alle inhoud van een BLOB vervangen.

![Azure Storage resources](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Volgende stappen

- [Een BLOB-moment opname maken en beheren in .NET](snapshots-manage-dotnet.md)
- [Back-ups maken van onbeheerde VM-schijven met incrementele momentopnames](../../virtual-machines/windows/incremental-snapshots.md)
