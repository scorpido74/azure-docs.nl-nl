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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597791"
---
Alle gegevens die zijn opgeslagen in Azure Files worden in rust versleuteld met Azure Storage Service Encryption (SSE). Opslagserviceversleuteling werkt op dezelfde manier als BitLocker op Windows: gegevens worden versleuteld onder het niveau van het bestandssysteem. Omdat gegevens worden versleuteld onder het bestandssysteem van het Azure-bestandsshare, omdat deze is gecodeerd op schijf, hoeft u geen toegang te hebben tot de onderliggende sleutel op de client om het Azure-bestandsshare te lezen of te schrijven.

Gegevens die zijn opgeslagen in Azure Files, worden standaard versleuteld met door Microsoft beheerde sleutels. Met door Microsoft beheerde sleutels heeft Microsoft de sleutels om de gegevens te versleutelen/decoderen en is het verantwoordelijk voor het regelmatig roteren ervan. U er ook voor kiezen om uw eigen sleutels te beheren, waardoor u controle hebt over het rotatieproces. Als u ervoor kiest om uw bestandsshares te versleutelen met door de klant beheerde sleutels, is Azure Files geautoriseerd om toegang te krijgen tot uw sleutels om lees- en schrijfverzoeken van uw clients te vervullen. Met door de klant beheerde sleutels u deze autorisatie op elk gewenst moment intrekken, maar dit betekent dat uw Azure-bestandsshare niet langer toegankelijk is via SMB of de FileREST-API.

Azure Files gebruikt hetzelfde versleutelingsschema als de andere Azure-opslagservices, zoals Azure Blob-opslag. Zie [Azure-opslagversleuteling voor gegevens in rust voor](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)meer informatie over Azure storage service encryption (SSE).