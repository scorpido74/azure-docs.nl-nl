---
title: Toegang tot de Azure Media Services-API-Azure CLI | Microsoft Docs
description: Volg de stappen in dit artikel om toegang te krijgen tot de Azure Media Services-API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d66b3e1b6ed2c8eef9f5cd21c0657648ad550ebe
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896160"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Toegang tot Azure Media Services-API met Azure CLI
 
Als u de Azure AD-Service-Principal-verificatie wilt gebruiken om verbinding te maken met de API van Azure Media Services, moet uw toepassing een Azure AD-token aanvragen met de volgende para meters:

* Azure AD-Tenant eindpunt
* Media Services resource-URI
* Resource-URI voor REST Media Services
* Azure AD-toepassings waarden: de client-ID en het client geheim

Zie [toegang tot Media Services v3-api's](media-services-apis-overview.md#accessing-the-azure-media-services-api)voor gedetailleerde uitleg.

In dit artikel wordt beschreven hoe u de Azure CLI gebruikt om een Azure AD-toepassing en Service-Principal te maken en de waarden op te halen die nodig zijn voor toegang tot Azure Media Services resources.

## <a name="prerequisites"></a>Vereisten 

[Een Azure Media Services-account maken](create-account-cli-how-to.md).

Vergeet niet de waarden die u hebt gebruikt voor de namen van de resourcegroep en het Media Services-account.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Zie ook

- [Gereserveerde media-eenheden schalen - CLI](media-reserved-units-cli-how-to.md)
- [Een Azure Media Services-account maken - CLI](create-account-cli-how-to.md) 
- [De referenties voor het account opnieuw instellen - CLI](cli-reset-account-credentials.md)
- [Assets maken - CLI](cli-create-asset.md)
- [Een bestand uploaden - CLI](cli-upload-file-asset.md)
- [Transformaties maken - CLI](cli-create-transform.md)
- [Versleutelen met een aangepaste transform-CLI](custom-preset-cli-howto.md)
- [Taken maken - CLI](cli-create-jobs.md)
- [EventGrid maken - CLI](job-state-events-cli-how-to.md)
- [Een asset publiceren - CLI](cli-publish-asset.md)
- [Filteren - CLI](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Volgende stappen

Het streaming-eind punt van waaruit u inhoud wilt streamen, moet de status wordt uitgevoerd hebben. Met de volgende CLI-opdracht wordt het standaard streaming-eind punt gestart:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

