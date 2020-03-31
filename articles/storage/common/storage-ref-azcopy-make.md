---
title: azcopy maken | Microsoft Documenten
description: Dit artikel bevat referentie-informatie voor de opdracht azcopy make.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 67d685684c5227377a0f8a7e822a06e785a69d89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034046"
---
# <a name="azcopy-make"></a>azcopy make

Hiermee maakt u een container of bestandsshare.

## <a name="synopsis"></a>Synopsis

Maak een container of bestandsshare die wordt weergegeven door de opgegeven resource-URL.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overbrengen met AzCopy- en Blob-opslag](storage-use-azcopy-blobs.md)
- [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [AzCopy configureren, optimaliseren en oplossen](storage-use-azcopy-configure.md)

## <a name="examples"></a>Voorbeelden

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h, --help|Help-inhoud weergeven voor de opdracht Maken. |
|--quota-gb uint32|Hiermee geeft u de maximale grootte van het aandeel in gigabytes (GiB) op, 0 betekent dat u het standaardquotum van de bestandsservice accepteert.|

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps uint32|Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.|
|--uitvoer-type tekenreeks|Indeling van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is "tekst".|

## <a name="see-also"></a>Zie ook

- [azcopie](storage-ref-azcopy.md)
