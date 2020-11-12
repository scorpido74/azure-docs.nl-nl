---
author: baanders
description: include-bestand voor Azure Digital Apparaatdubbels-manieren om het exemplaar te beheren
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 47de00780fb259e7476c16dea9b7c63bc512a211
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533889"
---
In dit artikel wordt uitgelegd hoe u verschillende beheer bewerkingen kunt uitvoeren met behulp van de [Azure Digital apparaatdubbels .net (C#) **SDK**](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true). U kunt dezelfde beheer aanroepen ook maken met behulp van de andere taal-Sdk's die worden beschreven in [*How-to: gebruik de Azure Digital Apparaatdubbels api's en sdk's*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Houd er rekening mee dat alle SDK-methoden in synchrone en asynchrone versies worden geleverd. Voor wissel gesprekken retour neren de async-methoden `AsyncPageable<T>` wanneer de synchrone versies retour neren `Pageable<T>` .

Een andere beheer optie is om de Azure Digital Apparaatdubbels [**rest-api's**](/rest/api/azure-digitaltwins/) voor dit onderwerp rechtstreeks aan te roepen.

Ten slotte kunt u dezelfde beheer bewerkingen uitvoeren met behulp van de Azure Digital Apparaatdubbels **cli**. Zie [*How-to: use the Azure Digital APPARAATDUBBELS cli*](../articles/digital-twins/how-to-use-cli.md)(Engelstalig) voor meer informatie over het gebruik van de cli.