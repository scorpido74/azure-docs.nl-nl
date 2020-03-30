---
title: bestand opnemen
description: bestand opnemen
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: eb13d889cb72911e2268b7538a74336befe3320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392424"
---
In de volgende tabel worden elk van de belangrijkste quota, limieten, standaardwaarden en gashendels in Azure Scheduler beschreven.

| Resource | Beschrijving van de limiet |
| -------- | ----------------- |
| **Taakgrootte** | De maximale baangrootte is 16.000. Als een PUT- of PATCH-bewerking resulteert in een taakgrootte die groter is dan deze limiet, wordt een statuscode van 400 bad request geretourneerd. | 
| **Taakverzamelingen** | Het maximum aantal taakverzamelingen per Azure-abonnement is 200.000. | 
| **Vacatures per verzameling** | Standaard is het maximum aantal banen vijf banen in een gratis taakverzameling en 50 banen in een standaardtaakverzameling. U het maximum aantal taken in een taakverzameling wijzigen. Alle taken in een taakverzameling zijn beperkt tot de waarde die is ingesteld op het ophalen van taken. Als u probeert meer taken te maken dan het maximumquotum, mislukt de aanvraag met een statuscode voor 409 conflict. | 
| **Tijd om de tijd te beginnen** | De maximale "starttijd" is 18 maanden. |
| **Herhalingsperiode** | De maximale herhalingsperiode is 18 maanden. | 
| **Frequentie** | Standaard is het maximumfrequentiequotum een uur in een gratis taakverzameling en één minuut in een standaardtaakverzameling. <p>U de maximale frequentie op een taakverzameling lager maken dan het maximum. Alle taken in de taakverzameling zijn beperkt tot de waarde die is ingesteld op het ophalen van taken. Als u probeert een taak te maken met een hogere frequentie dan de maximale frequentie voor het verzamelen van een taak, mislukt de aanvraag met een statuscode voor 409 conflict. | 
| **Lichaamsgrootte** | De maximale lichaamsgrootte voor een aanvraag is 8.192 chars. |
| **URL-grootte aanvragen** | De maximale grootte voor een aanvraag-URL is 2.048 chars. |
| **Aantal kopteksten** | Het maximale aantal kopteksten is 50 kopteksten. | 
| **Totale koptekstgrootte** | De maximale totale kopgrootte is 4.096 chars. |
| **Timeout** | De time-out van de aanvraag is statisch, dat wil zeggen, niet configureerbaar. en is 60 seconden voor HTTP-acties. Volg de HTTP-asynchrone protocollen voor langere bewerkingen. Geef bijvoorbeeld onmiddellijk een 202 terug, maar blijf op de achtergrond werken. | 
| **Jobgeschiedenis** | De maximale reactiebody die is opgeslagen in de taakgeschiedenis is 2.048 bytes. |
| **Behoud van de functiegeschiedenis** | De geschiedenis van de taak wordt bewaard voor maximaal twee maanden of tot de laatste 1.000 uitvoeringen. | 
| **Voltooide en gebrekkige taakbehoud** | Voltooide en foute taken worden 60 dagen bewaard. |
||| 

