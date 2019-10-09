---
title: Een Android-app maken in Azure App Service Mobile Apps | Microsoft Docs
description: Volg deze zelfstudie om aan de slag te gaan met back-ends voor mobiele apps van Azure voor Android-ontwikkeling.
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: emalani
ms.openlocfilehash: 0f79d2e7112847dbc5553e60f12bc872a5c0b5d5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027635"
---
# <a name="create-an-android-app"></a>Een Android-app maken
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end en geïntegreerde services in de ontwikkeling van mobiele apps. Ontwikkel aars kunnen services **bouwen**, **testen** en **distribueren** om een continue integratie-en leverings pijplijn in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkel aars de status en het gebruik van hun app bewaken met behulp van de **analyse** -en **diagnose** Services en gebruikers benaderen met behulp van de **Push** service. Ontwikkel aars kunnen ook gebruikmaken van **auth** voor het verifiëren van hun gebruikers en **gegevens** service om app-gegevens in de Cloud op te slaan en te synchroniseren.
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan met App Center [app Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="overview"></a>Overzicht
Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een mobiele Android-app met een back-end voor een mobiele apps van Azure.  U maakt zowel een nieuwe back-end voor een mobiele app als een eenvoudige Android-app voor *takenlijsten* die app-gegevens opslaat in Azure.

Het volgen van deze zelfstudie is een vereiste voor alle andere Android-zelfstudies over het gebruik van de functie Mobile Apps in Azure App Service.

## <a name="prerequisites"></a>Vereisten
Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* De [Android-hulpprogramma's voor ontwikkelaars](https://developer.android.com/sdk/index.html), waaronder Integrated Development Environment van Android Studio en het meest recente Android-platform.
* Azure Mobile Android SDK.
* Een [actief Azure-account](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Een nieuwe back-end voor mobiele apps van Azure maken
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Een database verbinding maken en het client-en server project configureren
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>De Android-app uitvoeren
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
