---
title: bestand opnemen
description: bestand opnemen
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: eaa76153fe96b5fd41166b20770e0a969aa9260d
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797105"
---
Een taak die wordt uitgevoerd in Azure Batch kan uitvoer gegevens produceren wanneer deze wordt uitgevoerd. Taak uitvoer gegevens moeten vaak worden opgeslagen om te worden opgehaald door andere taken in de taak, de client toepassing die de taak heeft uitgevoerd, of beide. Taken schrijven uitvoer gegevens naar het bestands systeem van een batch Compute-knoop punt, maar alle gegevens op het knoop punt gaan verloren wanneer de installatie kopie wordt gewijzigd of wanneer het knoop punt de groep verlaat. Taken kunnen ook een Bewaar periode voor bestanden hebben, waarna de bestanden die door de taak zijn gemaakt, worden verwijderd. Daarom is het belang rijk dat u de taak uitvoer persistent maakt die u later nodig hebt voor een gegevens archief, zoals [Azure Storage](https://docs.microsoft.com/azure/storage/).

Zie [batch-accounts en Azure Storage accounts](../articles/batch/accounts.md#azure-storage-accounts)voor opties voor opslag accounts in batch.
