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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806576"
---
## <a name="install-client-library-packages"></a>Clientbibliotheekpakketten installeren

> [!NOTE]
> De voorbeelden die hier worden weergegeven, gebruiken de Azure Storage-clientbibliotheek versie 12. De clientbibliotheek van versie 12 is onderdeel van de Azure SDK. Zie de Azure SDK-repository op [GitHub](https://github.com/Azure/azure-sdk)voor meer informatie over de Azure SDK.

Voer de volgende opdracht uit in de NuGet package manager console als u het Blob-opslagpakket wilt installeren:

```powershell
Install-Package Azure.Storage.Blobs
```

De hier weergegeven voorbeelden gebruiken ook de nieuwste versie van de [Azure Identity-clientbibliotheek voor .NET](https://www.nuget.org/packages/Azure.Identity/) om te verifiëren met Azure AD-referenties. Voer de volgende opdracht uit in de NuGet package manager console om het pakket te installeren:

```powershell
Install-Package Azure.Identity
```
