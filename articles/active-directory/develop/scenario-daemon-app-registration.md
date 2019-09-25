---
title: Daemon-app die web-Api's aanroept (app-registratie)-micro soft Identity-platform
description: Meer informatie over het bouwen van een daemon-app die web-Api's aanroept-app-registratie
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fddd95564e3683aaee067f0442573c41e93376dd
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264375"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Daemon-app voor het aanroepen van web-Api's-app-registratie

Voor een daemon-toepassing is dit wat u moet weten wanneer u de app registreert.

## <a name="supported-account-types"></a>Ondersteunde accounttypen

Omdat daemon-toepassingen alleen zinvol zijn in azure AD-tenants, kunt u bij het maken van de toepassing het volgende kiezen:

- **alleen accounts in deze organisatie Directory**. Deze keuze is het meest voorkomende geval, omdat daemon-toepassingen meestal worden geschreven door LOB-ontwikkel aars (line-of-Business).
- of **accounts in een organisatorische Directory**. U maakt deze keuze als u een ISV bent die een hulp programma biedt aan uw klanten. U hebt Tenant beheerders van de klant nodig om deze goed te keuren.

## <a name="authentication---no-reply-uri-needed"></a>Verificatie-geen antwoord-URI vereist

Als uw vertrouwelijke client toepassing **alleen** de client referenties stroom gebruikt, hoeft de antwoord-URL niet te worden geregistreerd. Het is niet nodig voor de toepassings configuratie/-constructie. De client referenties stroom gebruikt deze niet.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API-machtigingen-app-machtigingen en beheerders toestemming

Een daemon-toepassing kan alleen toepassings machtigingen voor Api's aanvragen (niet-gedelegeerde machtigingen). Klik op de pagina **API-machtiging** voor de registratie van de toepassing, nadat u **een machtiging toevoegen** hebt geselecteerd en de API-familie hebt gekozen, kies **toepassings machtigingen**en selecteer vervolgens uw machtigingen

![App-machtigingen en beheerders toestemming](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> De Web-API die u wilt aanroepen, moet de **toepassings machtigingen (app-rollen)** definiëren, niet de gedelegeerde machtigingen. Zie [voor meer informatie over het beschikbaar maken van een dergelijke API de beveiligde web-API: App-registratie: wanneer uw web-API wordt aangeroepen door een daemon-app](https://docs.microsoft.com/en-us/azure/active-directory/develop/scenario-protected-web-api-app-registration#if-your-web-api-is-called-by-a-daemon-app)

Voor daemon-toepassingen moet een Tenant beheerder vooraf toestemming hebben voor de toepassing die de Web-API aanroept. Deze toestemming wordt gegeven op dezelfde **API-machtigings** pagina, door een Tenant beheerder die de toestemming van de **beheerder toekennen aan *onze organisatie***  selecteert

Als u een ISV-toepassing met meerdere tenants maakt, moet u de implementatie van de alinea met [multi tenant-apps voor meerdere pachters](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) controleren.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Daemon-app-app-code configuratie](./scenario-daemon-app-configuration.md)
