---
title: azcopy | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy-opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4da9206f4500941179d781a0fe2a57ad15d7393d
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195891"
---
# <a name="azcopy"></a>azcopy

AzCopy is een opdracht regel programma waarmee gegevens worden verplaatst van en naar Azure Storage.

## <a name="synopsis"></a>Samen vatting

De algemene indeling van de opdrachten is: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Zie [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)als u problemen wilt melden of als u meer wilt weten over het hulp programma.

## <a name="options"></a>Opties

|Optie|Description|
|---|---|
|--Cap-Mbps uint32|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|-h,--Help|Hiermee wordt de Help-inhoud voor azcopy weer gegeven.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|

## <a name="see-also"></a>Zie ook

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [azcopy kopiëren](storage-ref-azcopy-copy.md)
- [azcopy-document](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy-taken](storage-ref-azcopy-jobs.md)
- [lijst met azcopy](storage-ref-azcopy-list.md)
- [azcopy-aanmelding](storage-ref-azcopy-login.md)
- [azcopy afmelden](storage-ref-azcopy-logout.md)
- [azcopy maken](storage-ref-azcopy-make.md)
- [azcopy verwijderen](storage-ref-azcopy-remove.md)
- [azcopy-synchronisatie](storage-ref-azcopy-sync.md)
