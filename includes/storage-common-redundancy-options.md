---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77157212"
---
De volgende redundantieopties zijn beschikbaar voor een opslagaccount:

* Lokaal redundante opslag (LRS): Een eenvoudige, voordelige redundantiestrategie. Gegevens worden synchroon drie keer binnen de primaire regio gekopieerd.
* Zone-redundante opslag (ZRS): Redundantie voor scenario's die hoge beschikbaarheid vereisen. Gegevens worden synchroon gekopieerd naar drie Azure-beschikbaarheidszones in de primaire regio.
* Geografisch redundante opslag (GRS): Redundantie over meerdere regio's om te beschermen tegen regionale storingen. Gegevens worden synchroon drie keer in de primaire regio gekopieerd en vervolgens asynchroon gekopieerd naar de secundaire regio. Voor leestoegang tot gegevens in de secundaire regio schakelt u geografisch redundante opslag met leestoegang (RA-GRS) in.
* Geografische zone-redundante opslag (GZRS) (preview-versie): Redundantie voor scenario's waarbij hoge beschikbaarheid en maximale duurzaamheid zijn vereist. Gegevens worden synchroon gekopieerd naar drie Azure-beschikbaarheidszones in de primaire regio en vervolgens asynchroon gekopieerd naar de secundaire regio. Voor leestoegang tot gegevens in de secundaire regio schakelt u geografisch zone-redundante opslag met lees toegang (RA-GZRS) in.

Zie [Azure Storage-redundantie](../articles/storage/common/storage-redundancy.md) voor meer informatie over opties voor redundantie in Azure Storage.
