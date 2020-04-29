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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "67176388"
---
Een taak die wordt uitgevoerd in Azure Batch kan uitvoer gegevens produceren wanneer deze wordt uitgevoerd. Taak uitvoer gegevens moeten vaak worden opgeslagen om te worden opgehaald door andere taken in de taak, de client toepassing die de taak heeft uitgevoerd, of beide. Taken schrijven uitvoer gegevens naar het bestands systeem van een batch Compute-knoop punt, maar alle gegevens op het knoop punt gaan verloren wanneer de installatie kopie wordt gewijzigd of wanneer het knoop punt de groep verlaat. Taken kunnen ook een Bewaar periode voor bestanden hebben, waarna de bestanden die door de taak zijn gemaakt, worden verwijderd. Daarom is het belang rijk dat u de taak uitvoer persistent maakt die u later nodig hebt voor een gegevens archief, zoals [Azure Storage](https://docs.microsoft.com/azure/storage/).

Zie voor opslagaccountopties in Batch het [Overzicht van Batch-functies](../articles/batch/batch-api-basics.md#azure-storage-account).
