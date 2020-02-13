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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157212"
---
De redundantie opties voor een opslag account zijn onder andere:

* Lokaal redundante opslag (LRS): een eenvoudige, voordelige redundantie strategie. Gegevens worden synchroon drie keer binnen de primaire regio gekopieerd.
* Zone-redundante opslag (ZRS): redundantie voor scenario's die hoge Beschik baarheid vereisen. Gegevens worden synchroon gekopieerd over drie Azure-beschikbaarheids zones in de primaire regio.
* Geografisch redundante opslag (GRS): Kruis regionale redundantie om te beschermen tegen regionale storingen. Gegevens worden synchroon drie keer in de primaire regio gekopieerd en vervolgens asynchroon gekopieerd naar de secundaire regio. Voor lees toegang tot gegevens in de secundaire regio schakelt u geografisch redundante opslag met lees toegang (RA-GRS) in.
* Geo-zone-redundante opslag (GZRS) (preview): redundantie voor scenario's waarbij hoge Beschik baarheid en maximale duurzaamheid zijn vereist. Gegevens worden synchroon gekopieerd over drie Azure-beschikbaarheids zones in de primaire regio en vervolgens asynchroon gekopieerd naar de secundaire regio. Voor lees toegang tot gegevens in de secundaire regio schakelt u geo-zone-redundante opslag met lees toegang (RA-GZRS) in.

Zie [Azure Storage redundantie](../articles/storage/common/storage-redundancy.md)voor meer informatie over redundantie opties in azure Storage.
