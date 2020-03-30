---
title: De eigenschap Laatste synchronisatietijd controleren voor een opslagaccount
titleSuffix: Azure Storage
description: Meer informatie over het controleren van de eigenschap **Laatste synchronisatietijd** voor een geo-gerepliceerd opslagaccount. De eigenschap **Last Sync Time** geeft de laatste keer aan waarop alle schrijfbewerkingen uit het primaire gebied met succes naar het secundaire gebied zijn geschreven.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165486"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>De eigenschap Laatste synchronisatietijd controleren voor een opslagaccount

Wanneer u een opslagaccount configureert, u opgeven dat uw gegevens worden gekopieerd naar een secundaire regio die honderden kilometers van de primaire regio verwijderd is. Geo-replicatie biedt duurzaamheid voor uw gegevens in het geval van een aanzienlijke uitval in de primaire regio, zoals een natuurramp. Als u bovendien leestoegang tot het secundaire gebied inschakelt, blijven uw gegevens beschikbaar voor leesbewerkingen als de primaire regio niet meer beschikbaar is. U uw toepassing zo ontwerpen dat u naadloos overschakelt naar lezen vanuit het secundaire gebied als de primaire regio niet reageert.

Georedundante opslag (GRS) en geo-zoneredundante opslag (GZRS) (voorbeeld) repliceren uw gegevens asynchroon naar een secundair gebied. Voor leestoegang tot de secundaire regio u georedundante opslag (RA-GRS) of geo-zoneredundante opslag (RA-GZRS) voor leestoegang tot de secundaire regio inschakelen. Zie [Azure Storage redundantie](storage-redundancy.md)voor meer informatie over de verschillende redundantieopties die door Azure Storage worden aangeboden.

In dit artikel wordt beschreven hoe u de eigenschap **Last Sync Time** voor uw opslagaccount controleren, zodat u eventuele verschillen tussen de primaire en secundaire regio's evalueren.

## <a name="about-the-last-sync-time-property"></a>Informatie over de eigenschap Laatste synchronisatietijd

Omdat georeplicatie asynchroon is, is het mogelijk dat gegevens die naar het primaire gebied zijn geschreven, nog niet naar het secundaire gebied zijn geschreven op het moment dat er een storing optreedt. De eigenschap **Last Sync Time** geeft de laatste keer aan dat gegevens uit het primaire gebied zijn geschreven naar het secundaire gebied. Alle schrijfbewerkingen die naar het primaire gebied zijn geschreven voordat de laatste synchronisatietijd beschikbaar is, kunnen worden gelezen vanaf de secundaire locatie. Schrijfbewerkingen naar het primaire gebied na de laatste synchronisatietijdeigenschap zijn mogelijk al dan niet beschikbaar voor lezen.

De eigenschap **Laatste synchronisatietijd** is een GMT-datum/-tijdwaarde.

## <a name="get-the-last-sync-time-property"></a>De eigenschap Laatste synchronisatietijd ophalen

U PowerShell of Azure CLI gebruiken om de waarde van de eigenschap **Last Sync Time** op te halen.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Als u de laatste synchronisatietijd voor het opslagaccount met PowerShell wilt ontvangen, installeert u een Azure Storage preview-module die ondersteuning biedt voor het verkrijgen van georeplicatiestatistieken. Bijvoorbeeld:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

Controleer vervolgens de eigenschap **GeoReplicatieStats.LastSyncTime** van het opslagaccount. Vergeet niet om de tijdelijke aanduidingswaarden te vervangen door uw eigen waarden:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de laatste synchronisatietijd voor het opslagaccount met Azure CLI wilt krijgen, controleert u de eigenschap **geoReplicatieStats.lastSyncTime** van het opslagaccount. Gebruik `--expand` de parameter om waarden terug te geven voor de eigenschappen die zijn genest onder **geoReplicatiestatistieken**. Vergeet niet om de tijdelijke aanduidingswaarden te vervangen door uw eigen waarden:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Zie ook

- [Redundantie azure-opslag](storage-redundancy.md)
- [De redundantieoptie voor een opslagaccount wijzigen](redundancy-migration.md)
- [Ontwerpen van zeer beschikbare toepassingen met behulp van georedundante opslag met leestoegang](storage-designing-ha-apps-with-ragrs.md)