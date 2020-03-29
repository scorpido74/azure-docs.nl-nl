---
title: De postman-omgeving importeren voor REST-aanroepen van Azure Media Services
description: In dit onderwerp wordt een definitie van de Postman-omgeving voor Azure Media Services REST-oproepen gedefinieerd.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8254d121c62a20de0a1593920b7793195f8eb50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926717"
---
# <a name="import-the-postman-environment"></a>De postman-omgeving importeren  

Dit artikel bevat een definitie van de **postman-omgevingsvariabelen** die worden gebruikt in de [Postman-verzameling](postman-collection.md) die gegroepeerde HTTP-aanvragen bevat die API's voor mediaservices REST aanroepen. De omgeving- en verzamelingsbestanden worden gebruikt door de zelfstudie [Voor API-aanroepen](media-rest-apis-with-postman.md) van De REST van mediaservices configureren voor Media Services.

> [!NOTE]
> De waarde `AzureADSTSEndpoint `  =  `https://login.microsoftonline.com/{{TenantId}}/oauth2/token`van . Als u uw tenant-id wilt krijgen, u met uw muis over uw gebruikersnaam in de portal (in de rechterbovenhoek) zweven en deze bevindt zich in de 'Directory: Microsoft ( {{TENANTID}} ).

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
