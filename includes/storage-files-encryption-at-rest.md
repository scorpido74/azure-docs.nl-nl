---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597791"
---
Alle gegevens die zijn opgeslagen in Azure Files, worden op rest versleuteld met behulp van Azure Storage service Encryption (SSE). Versleuteling van de opslag service werkt op dezelfde manier als BitLocker op Windows: gegevens worden versleuteld onder het bestandssysteem niveau. Omdat de gegevens worden versleuteld onder het bestands systeem van de Azure-bestands share, wanneer deze zijn gecodeerd naar schijf, hoeft u geen toegang te hebben tot de onderliggende sleutel op de client om te lezen of te schrijven naar de Azure-bestands share.

Standaard worden gegevens die zijn opgeslagen in Azure Files versleuteld met door micro soft beheerde sleutels. Met door micro soft beheerde sleutels bevat micro soft de sleutels voor het versleutelen/ontsleutelen van de gegevens en is het de verantwoordelijkheid om deze regel matig te draaien. U kunt er ook voor kiezen om uw eigen sleutels te beheren. Dit geeft u de controle over het rotatie proces. Als u ervoor kiest om uw bestands shares te versleutelen met door de klant beheerde sleutels, is Azure Files gemachtigd om toegang te krijgen tot uw sleutels om te voldoen aan lees-en schrijf aanvragen van uw klanten. Met door de klant beheerde sleutels kunt u deze autorisatie op elk gewenst moment intrekken, maar dit betekent dat uw Azure-bestands share niet langer toegankelijk is via SMB of de FileREST-API.

Azure Files gebruikt hetzelfde versleutelings schema als de andere Azure Storage-services zoals Azure Blob Storage. Zie voor meer informatie over Azure Storage service Encryption (SSE) [Azure Storage-versleuteling voor Data-at-rest](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).