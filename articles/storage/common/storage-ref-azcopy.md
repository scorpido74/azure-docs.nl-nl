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
ms.openlocfilehash: 0772446b0259b12d3f76b2020eeed4c9c2de1119
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882225"
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
- [azcopy Bank](storage-ref-azcopy-bench.md)
- [azcopy kopiëren](storage-ref-azcopy-copy.md)
- [azcopy-document](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy-taken](storage-ref-azcopy-jobs.md)
- [azcopy-taken opschonen](storage-ref-azcopy-jobs-clean.md)
- [azcopy-taken lijst](storage-ref-azcopy-jobs-list.md)
- [azcopy-taken verwijderen](storage-ref-azcopy-jobs-remove.md)
- [azcopy-taken hervatten](storage-ref-azcopy-jobs-resume.md)
- [azcopy-taken weer geven](storage-ref-azcopy-jobs-show.md)
- [lijst met azcopy](storage-ref-azcopy-list.md)
- [azcopy-aanmelding](storage-ref-azcopy-login.md)
- [azcopy afmelden](storage-ref-azcopy-logout.md)
- [azcopy maken](storage-ref-azcopy-make.md)
- [azcopy verwijderen](storage-ref-azcopy-remove.md)
- [azcopy-synchronisatie](storage-ref-azcopy-sync.md)
