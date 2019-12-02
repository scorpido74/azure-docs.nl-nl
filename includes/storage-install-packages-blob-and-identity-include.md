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
ms.openlocfilehash: 0adf1280fa50e9ee594f3025dff70786f5ba2199
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666156"
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

Voor meer informatie over het verifiëren van de identiteit van de Azure Identity client-bibliotheek van Azure Storage, zie de sectie **verificatie met de Azure Identity-bibliotheek** in [toegang verlenen tot blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources](/azure/storage/common/storage-auth-aad-msi?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).