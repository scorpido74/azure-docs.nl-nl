---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68249919"
---
Een Azure AD-bron-id geeft de doelgroep aan waarvoor een token dat is uitgegeven, kan worden gebruikt om toegang te bieden tot een Azure-bron. In het geval van Azure Storage kan de bron-id specifiek zijn voor één opslagaccount of is deze mogelijk van toepassing op een opslagaccount. In de volgende tabel worden de waarden beschreven die u opgeven voor de resource-id:

|Resource-id  |Beschrijving  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Het serviceeindpunt voor een bepaald opslagaccount. Gebruik deze waarde om een token te verkrijgen voor het toestaan van aanvragen voor dat specifieke Azure Storage-account en -service. Vervang de waarde tussen haakjes door de naam van uw opslagaccount.      |
|`https://storage.azure.com/`     | Gebruik om een token te verkrijgen voor het toestaan van aanvragen voor een Azure Storage-account.        |
