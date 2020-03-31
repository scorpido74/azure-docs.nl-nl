---
title: bestand opnemen
description: bestand opnemen
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7ba4c90811bd8051ed9c307d9f9fa33e08e69dc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176388"
---
Een taak die wordt uitgevoerd in Azure Batch kan uitvoergegevens produceren wanneer deze wordt uitgevoerd. Taakuitvoergegevens moeten vaak worden opgeslagen voor ophalen door andere taken in de taak, de clienttoepassing die de taak heeft uitgevoerd of beide. Taken schrijven uitvoergegevens naar het bestandssysteem van een batchcomputeknooppunt, maar alle gegevens op het knooppunt gaan verloren wanneer deze opnieuw worden weergegeven of wanneer het knooppunt de groep verlaat. Taken kunnen ook een bewaarperiode voor bestanden hebben, waarna bestanden die door de taak zijn gemaakt, worden verwijderd. Om deze redenen is het belangrijk om de taakuitvoer voort te houden die u later nodig hebt naar een gegevensarchief zoals [Azure Storage.](https://docs.microsoft.com/azure/storage/)

Zie voor opslagaccountopties in Batch het [Overzicht van Batch-functies](../articles/batch/batch-api-basics.md#azure-storage-account).
