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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77157212"
---
Redundantieopties voor een opslagaccount zijn:

* Lokaal redundante opslag (LRS): een eenvoudige, goedkope redundantiestrategie. Gegevens worden drie keer synchroon gekopieerd binnen het primaire gebied.
* Zoneredundante opslag (ZRS): redundantie voor scenario's die een hoge beschikbaarheid vereisen. Gegevens worden synchroon gekopieerd in drie Azure-beschikbaarheidszones in het primaire gebied.
* Geo-redundante opslag (GRS): Cross-regional redundantie om te beschermen tegen regionale storingen. Gegevens worden drie keer synchroon gekopieerd in het primaire gebied en vervolgens asynchroon gekopieerd naar het secundaire gebied. Voor leestoegang tot gegevens in de secundaire regio u georedundante opslag (RA-GRS) gebruiken voor leestoegang tot gegevens in de secundaire regio.
* Geo-zone-redundante opslag (GZRS) (voorbeeld): Redundantie voor scenario's die zowel hoge beschikbaarheid als maximale duurzaamheid vereisen. Gegevens worden synchroon gekopieerd over drie Azure-beschikbaarheidszones in het primaire gebied en vervolgens asynchroon gekopieerd naar het secundaire gebied. Voor leestoegang tot gegevens in de secundaire regio u geozoneredundante opslag (RA-GZRS) inschakelen voor leestoegang tot gegevens in de secundaire regio.

Zie [Redundantie](../articles/storage/common/storage-redundancy.md)azure Storage voor meer informatie over redundantieopties in Azure Storage.
