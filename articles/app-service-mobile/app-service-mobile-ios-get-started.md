---
title: Een iOS-app maken in Azure App Service Mobile Apps| Microsoft Docs
description: Volg deze zelfstudie om aan de slag te gaan met back-ends voor mobiele apps van Azure voor iOs-ontwikkeling in Objective-C of Swift.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: cdfe395bca7ac73f00506da0180146310bd71d3d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027232"
---
# <a name="create-an-ios-app"></a>Een iOS-app maken

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end en geïntegreerde services in de ontwikkeling van mobiele apps. Ontwikkel aars kunnen services **bouwen**, **testen** en **distribueren** om een continue integratie-en leverings pijplijn in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkel aars de status en het gebruik van hun app bewaken met behulp van de **analyse** -en **diagnose** Services en gebruikers benaderen met behulp van de **Push** service. Ontwikkel aars kunnen ook gebruikmaken van **auth** voor het verifiëren van hun gebruikers en **gegevens** service om app-gegevens in de Cloud op te slaan en te synchroniseren.
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan met App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="overview"></a>Overzicht

Deze zelfstudie laat zien hoe u [Azure App Service Mobile Apps](app-service-mobile-value-prop.md), een back-endservice in de cloud, toevoegt aan een iOS-app. De eerste stap bestaat uit het maken van een nieuwe mobiele back-end in Azure. Download vervolgens een eenvoudige iOS voorbeeld-app van een *takenlijst* die gegevens in Azure opslaat.

Voor het uitvoeren van deze zelfstudie hebt u een Mac en [een Azure-account](https://azure.microsoft.com/pricing/free-trial/) nodig

## <a name="create-a-new-azure-mobile-app-backend"></a>Een nieuwe back-end voor mobiele apps van Azure maken

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Een database verbinding maken en het client-en server project configureren
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>De iOS-app uitvoeren

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
