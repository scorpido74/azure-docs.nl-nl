---
title: azcopy | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy-opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 984d0c570c6c0d5048d58377f113319157411244
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513377"
---
# <a name="azcopy"></a>azcopy

AzCopy is een opdracht regel programma waarmee gegevens worden verplaatst van en naar Azure Storage.

## <a name="synopsis"></a>Samen vatting

De algemene indeling van de opdrachten is: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Zie [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)als u problemen wilt melden of meer wilt weten over het hulp programma.

## <a name="options"></a>Opties

**--Cap-Mbps uint32**   De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.

**-h,--Help** Help voor azcopy
      
**--uitvoer type**  De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is ' text '. (standaard tekst)

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
