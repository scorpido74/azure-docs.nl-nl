---
title: azcopy-taken hervatten | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de opdracht azcopy Jobs hervatten.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: dd25bec04d651c01d622f0652a29a65069421786
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87281953"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Hiermee wordt de bestaande taak hervat met de opgegeven taak-ID.

## <a name="synopsis"></a>Samen vatting

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](storage-use-azcopy-blobs.md)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opties

|Optie|Beschrijving|
|--|--|
|--doel-SAS-teken reeks|De doel-SAS van de doel locatie voor de opgegeven taak-ID.|
|--teken reeks uitsluiten|Filter: deze mislukte overdracht (en) uitsluiten wanneer de taak wordt hervat. Bestanden moeten worden gescheiden door '; '.|
|-h,--Help|Help-inhoud weer geven voor de opdracht hervatten.|
|--reeks toevoegen|Filter: deze mislukte overdracht (en) zijn alleen beschikbaar wanneer de taak wordt hervat. Bestanden moeten worden gescheiden door '; '.|
|--SAS-teken reeks |Bron-SAS van de bron voor de opgegeven taak-ID.|

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--Cap-Mbps-float|De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.|
|--type teken reeks voor uitvoer|De indeling van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is "text".|
|--vertrouwd-micro soft-achtervoegsels teken reeks   |Hiermee geeft u aanvullende domein achtervoegsels op waar Azure Active Directory aanmeldings tokens kunnen worden verzonden.  De standaard waarde is *. core.Windows.net;*. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. Alle hier vermelde waarden worden toegevoegd aan de standaard instelling. Voor beveiliging moet u Microsoft Azure domeinen hier alleen plaatsen. Scheid meerdere vermeldingen met een punt komma.|

## <a name="see-also"></a>Zie ook

- [azcopy jobs](storage-ref-azcopy-jobs.md)
