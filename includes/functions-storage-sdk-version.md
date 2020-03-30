---
title: bestand opnemen
description: bestand opnemen
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198412"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Azure Storage SDK-versie in functies 1.x

In Functies 1.x gebruiken de opslagtriggers en -bindingen versie 7.2.1 van het Azure Storage SDK[(WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet-pakket). Als u verwijst naar een andere versie van de Storage SDK en u aan een Storage SDK-type in uw functiehandtekening bindt, kan de runtime Functies melden dat deze niet aan dat type kan worden gekoppeld. De oplossing is ervoor te zorgen dat uw project verwijst naar [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
