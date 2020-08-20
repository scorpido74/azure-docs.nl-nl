---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: dcd2cda3bad2a13a83c5f3f6700e5a57471e2065
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653873"
---
<!--Create a media services asset REST-->

Met de volgende Azure .NET-opdracht maakt u een nieuwe Media Services-Asset. Vervang de waarden `subscriptionID` , `resourceGroup` en `amsAccountName` met de waarden waarmee u momenteel werkt. Geef uw asset een naam door hier in te stellen `assetName` .

[!code-csharp[Main](../../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]
