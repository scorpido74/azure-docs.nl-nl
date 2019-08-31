---
title: azcopy env | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy env-opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 15e72493190e1bc56e779c22695bc51bd05da940
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195774"
---
# <a name="azcopy-env"></a>azcopy env

Hier worden de omgevings variabelen weer gegeven waarmee het gedrag van AzCopy kan worden geconfigureerd.

## <a name="synopsis"></a>Samen vatting

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Als u een omgevings variabele instelt met behulp van de opdracht regel, wordt die variabele leesbaar in de opdracht regel geschiedenis. U kunt variabelen met referenties uit de opdracht regel geschiedenis wissen. Als u wilt voor komen dat variabelen in uw geschiedenis worden weer gegeven, kunt u een script gebruiken om de gebruiker om referenties te vragen en de omgevings variabele in te stellen.

## <a name="options"></a>Opties

|Optie|Description|
|--|--|
|-h,--Help|Hiermee wordt Help-inhoud voor de env-opdracht weer gegeven. |
|--gevoelige weer gave|Gevoelige/geheime omgevings variabelen worden weer gegeven.|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Description|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
