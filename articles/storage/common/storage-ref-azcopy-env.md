---
title: azcopy env | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy env-opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033748"
---
# <a name="azcopy-env"></a>azcopy env

Hier worden de omgevings variabelen weer gegeven waarmee het gedrag van AzCopy kan worden geconfigureerd.

## <a name="synopsis"></a>Samen vatting

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Als u een omgevings variabele instelt met behulp van de opdracht regel, wordt die variabele leesbaar in de opdracht regel geschiedenis. U kunt variabelen met referenties uit de opdracht regel geschiedenis wissen. Als u wilt voor komen dat variabelen in uw geschiedenis worden weer gegeven, kunt u een script gebruiken om de gebruiker om referenties te vragen en de omgevings variabele in te stellen.

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](storage-use-azcopy-blobs.md)
- [Gegevens overdragen met AzCopy en File Storage](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en problemen oplossen](storage-use-azcopy-configure.md)

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h,--Help|Hiermee wordt Help-inhoud voor de env-opdracht weer gegeven. |
|--gevoelige weer gave|Gevoelige/geheime omgevings variabelen worden weer gegeven.|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
