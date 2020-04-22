---
title: bestand opnemen
description: bestand opnemen
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: fc5f4d2c10cac23600025a72fedf7fe2cec5a34e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684092"
---
Als u gegevens wilt lezen die aanwezig zijn in Archive Storage, moet u de laag van de blob eerst wijzigen in Hot of Cool. Dit proces staat bekend als rehydratie en kan uren duren om te voltooien. We raden grote blobmaten aan voor optimale rehydratieprestaties. Enkele kleine blobs tegelijk reactiveren kan extra tijd toevoegen. Er zijn momenteel twee rehydraatprioriteiten, Hoog en Standaard, die kunnen worden ingesteld via de optionele eigenschap *x-ms-rehydraatprioriteit* op een [setbloblaag](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) of [kopieerblobbewerking.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Standaardprioriteit**: De rehydratieaanvraag wordt verwerkt in de volgorde waarin het is ontvangen en kan tot 15 uur duren.
* **Hoge prioriteit**: De rehydratieaanvraag krijgt prioriteit boven standaardaanvragen en kan in minder dan 1 uur eindigen. Hoge prioriteit kan langer duren dan 1 uur, afhankelijk van blob grootte en de huidige vraag. Aanvragen met hoge prioriteit worden gegarandeerd voorrang gegeven boven standaardprioriteitsaanvragen.

> [!NOTE]
> Standaardprioriteit is de standaardoptie voor het opnieuw uitdrogen voor archiveren. Hoge prioriteit is een snellere optie die meer kost dan standaard prioriteit rehydratie en is meestal gereserveerd voor gebruik in noodsituaties gegevens restauratie situaties.

Zodra een rehydratieverzoek is gestart, kan deze niet meer worden geannuleerd. Tijdens het rehydratieproces blijft de *blob-eigenschap x-ms-access-tier* weergegeven als archief totdat rehydratie is voltooid tot een online laag. Om de rehydratiestatus en voortgang te bevestigen, u [Blob-eigenschappen](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) ophalen om de *x-ms-archive-status* en de *blob-eigenschappen met x-ms-rehydraatprioriteit* te controleren. De archiefstatus kan 'rehydrateren-tot-warm' of 'rehydrateren-in-to-cool' lezen, afhankelijk van de doellaag voor rehydrateren. De prioriteit voor rehydrateren geeft de snelheid van "Hoog" of "Standaard" aan. Na voltooiing worden de eigenschappen van de archiefstatus en de prioriteitsprioriteit opnieuw gehydrateerd en wordt de eigenschap blob voor toegangslagen bijgewerkt om de geselecteerde hot- of cool-laag weer te geven.
