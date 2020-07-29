---
title: azcopy env | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy env-opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 197d8ee1ea1d389a26548fa65adee83c6a92cf96
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87275969"
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
- [Gegevens overdragen met AzCopy en bestandopslag](storage-use-azcopy-files.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|-h,--Help|Hiermee wordt Help-inhoud voor de env-opdracht weer gegeven. |
|--gevoelige weer gave|Gevoelige/geheime omgevings variabelen worden weer gegeven.|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--Cap-Mbps-float|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|
|--vertrouwd-micro soft-achtervoegsels teken reeks  | Hiermee geeft u aanvullende domein achtervoegsels op waar Azure Active Directory aanmeldings tokens kunnen worden verzonden.  De standaard waarde is *. core.Windows.net;*. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. Alle hier vermelde waarden worden toegevoegd aan de standaard instelling. Voor beveiliging moet u Microsoft Azure domeinen hier alleen plaatsen. Scheid meerdere vermeldingen met een punt komma.|

## <a name="see-also"></a>Zie tevens

- [azcopy](storage-ref-azcopy.md)
