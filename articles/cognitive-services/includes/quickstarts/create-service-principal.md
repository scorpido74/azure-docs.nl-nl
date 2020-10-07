---
title: Een Azure-service-principal maken
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 5cff103d17138608f4932e36444847437f6da236
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321563"
---
## <a name="create-an-azure-service-principal"></a>Een Azure-service-principal maken

Als u uw toepassing wilt laten communiceren met uw Azure-account, hebt u een Azure-service-principal nodig om machtigingen te beheren. Volg de instructies in [Een Azure-service-principal maken](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Wanneer u een service-principal maakt, ziet u dat deze een geheime waarde, een id en een toepassings-id heeft. Sla de toepassings-id en het geheim op een tijdelijke locatie op voor latere stappen.