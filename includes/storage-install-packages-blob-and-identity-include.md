---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74806576"
---
## <a name="install-client-library-packages"></a>Client bibliotheek pakketten installeren

> [!NOTE]
> De voor beelden die hier worden weer gegeven, gebruiken de Azure Storage-client bibliotheek versie 12. De client bibliotheek van versie 12 maakt deel uit van de Azure SDK. Zie de Azure SDK-opslag plaats op [github](https://github.com/Azure/azure-sdk)voor meer informatie over de Azure SDK.

Als u het Blob Storage-pakket wilt installeren, voert u de volgende opdracht uit vanuit de NuGet Package Manager-console:

```powershell
Install-Package Azure.Storage.Blobs
```

De voor beelden die hier worden weer gegeven, gebruiken ook de nieuwste versie van de [Azure Identity client-bibliotheek voor .net](https://www.nuget.org/packages/Azure.Identity/) om te verifiëren met Azure AD-referenties. Als u het pakket wilt installeren, voert u de volgende opdracht uit vanuit de NuGet Package Manager-console:

```powershell
Install-Package Azure.Identity
```
