---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011997"
---
De [Microsoft Azure app Authentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) -client bibliotheek voor .net (preview) vereenvoudigt het proces van het verkrijgen en vernieuwen van een token uit uw code. De client bibliotheek voor app-verificatie beheert de verificatie automatisch. De bibliotheek gebruikt de referenties van de ontwikkelaar voor verificatie tijdens de lokale ontwikkeling. Het gebruik van ontwikkelaars referenties tijdens de lokale ontwikkeling is veiliger omdat u geen Azure AD-referenties hoeft te maken of referenties tussen ontwikkel aars moet delen. Wanneer de oplossing later wordt geïmplementeerd in azure, wordt automatisch overgeschakeld naar toepassings referenties met de bibliotheek.

Als u de bibliotheek voor app-verificatie in een Azure Storage toepassing wilt gebruiken, installeert u het meest recente preview-pakket van [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)en de meest recente versie van de [Azure Storage common client library voor .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) en de [Azure Blob Storage-client bibliotheek voor .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Voeg de volgende **using** -instructies toe aan uw code:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
