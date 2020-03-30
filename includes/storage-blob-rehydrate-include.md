---
title: bestand opnemen
description: bestand opnemen
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 81ffc87ce97a936e693c59bca6caf721cb8599cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75780234"
---
Als u gegevens wilt lezen die aanwezig zijn in Archive Storage, moet u de laag van de blob eerst wijzigen in Hot of Cool. Dit proces staat bekend als rehydratie en kan uren duren om te voltooien. We raden grote blobmaten aan voor optimale rehydratieprestaties. Enkele kleine blobs tegelijk reactiveren kan extra tijd toevoegen. Er zijn momenteel twee rehydraatprioriteiten, Hoog (voorbeeld) en Standaard, die kunnen worden ingesteld via de optionele eigenschap *x-ms-rehydraatprioriteit* op een [setbloblaag](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) of [kopieerblobbewerking.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Standaardprioriteit**: De rehydratieaanvraag wordt verwerkt in de volgorde waarin het is ontvangen en kan tot 15 uur duren.
* **Hoge prioriteit (preview)**: De rehydratieaanvraag krijgt prioriteit boven standaardaanvragen en kan in minder dan 1 uur worden voltooid. Hoge prioriteit kan langer duren dan 1 uur, afhankelijk van blob grootte en de huidige vraag. Aanvragen met hoge prioriteit worden gegarandeerd voorrang gegeven boven standaardprioriteitsaanvragen.

> [!NOTE]
> Standaardprioriteit is de standaardoptie voor het opnieuw uitdrogen voor archiveren. Hoge prioriteit is een snellere optie die meer kost dan standaard prioriteit rehydratie en is meestal gereserveerd voor gebruik in noodsituaties gegevens restauratie situaties.

Zodra een rehydratieverzoek is gestart, kan deze niet meer worden geannuleerd. Tijdens rehydratie u de *blobeigenschap Archiefstatus* controleren om te controleren of de laag is gewijzigd. De status is "rehydrate-pending-to-hot" of "rehydrate-pending-to-cool", afhankelijk van de doellaag. Na voltooiing wordt de eigenschap archiefstatus verwijderd en weerspiegelt de eigenschap *Access Tier* blob de nieuwe hot- of cool-laag.
