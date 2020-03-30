---
title: azcopy env | Microsoft Documenten
description: Dit artikel bevat referentie-informatie voor de azcopy env opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033748"
---
# <a name="azcopy-env"></a>azcopy env

Toont de omgevingsvariabelen die het gedrag van AzCopy kunnen configureren.

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Als u een omgevingsvariabele instelt met behulp van de opdrachtregel, is die variabele leesbaar in de geschiedenis van de opdrachtregel. Overweeg variabelen te wissen die referenties bevatten uit de geschiedenis van de opdrachtregel. Om te voorkomen dat variabelen in uw geschiedenis worden weergegeven, u een script gebruiken om de gebruiker om hun referenties te vragen en de omgevingsvariabele in te stellen.

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overbrengen met AzCopy- en Blob-opslag](storage-use-azcopy-blobs.md)
- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h, --help|Hiermee wordt help-inhoud voor de env-opdracht weergegeven. |
|--show-gevoelig|Toont gevoelige/geheime omgevingsvariabelen.|

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps uint32|Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.|
|--uitvoer-type tekenreeks|Indeling van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is "tekst".|

## <a name="see-also"></a>Zie ook

- [azcopie](storage-ref-azcopy.md)
