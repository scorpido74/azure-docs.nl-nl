---
title: Serialisatie van aangepaste tokencache (MSAL Python) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het serialiseren van de tokencache voor MSAL voor Python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2593cc856afb98cf5186c4e33032c5e9151614f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76704388"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Aangepaste tokencacheserialisatie in MSAL voor Python

In MSAL Python wordt standaard een tokencache in het geheugen die gedurende de duur van de app-sessie blijft bestaan, standaard geleverd wanneer u een exemplaar van [ClientApplication maakt.](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)

Serialisatie van de tokencache, zodat verschillende sessies van uw app er toegang toe hebben, wordt niet 'out of the box' geleverd. Dat komt omdat MSAL Python kan worden gebruikt in app-typen die geen toegang hebben tot het bestandssysteem, zoals web-apps. Als u een permanente tokencache wilt hebben in een MSAL Python-app, moet u aangepaste tokencache-serialisatie bieden.

De strategieën voor het serialiseren van de tokencache verschillen afhankelijk van of u een openbare clienttoepassing (Desktop) of een vertrouwelijke clienttoepassing (Web App, Web API of Daemon-app) schrijft.

## <a name="token-cache-for-a-public-client-application"></a>Tokencache voor een openbare clienttoepassing

Openbare clienttoepassingen worden uitgevoerd op het apparaat van een gebruiker en beheren tokens voor één gebruiker. In dit geval u de hele cache in een bestand serialiseren. Vergeet niet om bestandsvergrendeling op te geven als uw app of een andere app gelijktijdig toegang heeft tot de cache. Zie het voorbeeld in de referentiedocumentatie van de [class SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) voor een eenvoudig voorbeeld van hoe u een tokencache serialiseren voor een bestand zonder vergrendeling.

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Tokencache voor een web-app (vertrouwelijke clienttoepassing)

Voor web-apps of web-API's u de sessie of een Redis-cache of een database gebruiken om de tokencache op te slaan. Er moet één tokencache per gebruiker zijn (per account), dus zorg ervoor dat u de tokencache per account serialiseert.

## <a name="next-steps"></a>Volgende stappen

Zie [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) voor een voorbeeld van het gebruik van de tokencache voor een Windows- of Linux Web-app of Web API. Het voorbeeld is voor een web-app die de Microsoft Graph API aanroept.
