---
title: Inhoud uploaden naar een Azure Media Services-asset met Azure CLI
description: In het Azure CLI-script in dit onderwerp ziet u hoe u een Media Services-asset kunt maken om inhoud naar te uploaden.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 65b0b276dcda58810f44494c14f06dd412d633f6
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719288"
---
# <a name="create-an-asset"></a>Een Asset maken

In dit artikel wordt beschreven hoe u een Media Services-activum maakt.  U gebruikt een Asset om media-inhoud te bewaren voor code ring en streaming.  Lees [assets in azure Media Services v3](assets-concept.md) voor meer informatie over Media Services assets

## <a name="prerequisites"></a>Vereisten

Volg de stappen in [een Media Services-account maken](./create-account-howto.md) om het benodigde Media Services account en de resource groep te maken om een Asset te maken.

## <a name="methods"></a>Methoden

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>REST gebruiken

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>Met cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="using-postman"></a>Met Postman

[!INCLUDE[Create an asset with Postman](./includes/task-create-asset-postman.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Media Services](media-services-overview.md)
