---
title: Bureau blad-apps registreren die web-Api's aanroepen-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een bureau blad-app die web-Api's aanroept (app-registratie)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 599603ba867e21694392e38e9692280f010e08eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885154"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Bureau blad-app voor het aanroepen van web-Api's: app-registratie

In dit artikel worden de app-registratie gegevens voor een bureaublad toepassing besproken.

## <a name="supported-account-types"></a>Ondersteunde accounttypen

De account typen die worden ondersteund in een bureaublad toepassing, zijn afhankelijk van de ervaring die u wilt oplichten. Vanwege deze relatie zijn de ondersteunde account typen afhankelijk van de stromen die u wilt gebruiken.

### <a name="audience-for-interactive-token-acquisition"></a>Doel groep voor het verkrijgen van interactief tokens

Als uw bureaublad toepassing interactieve verificatie gebruikt, kunt u gebruikers van elk [account type](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)aanmelden.

### <a name="audience-for-desktop-app-silent-flows"></a>Doel groep voor desktop-app Silent flows

- Als u geïntegreerde Windows-verificatie of een gebruikers naam en wacht woord wilt gebruiken, moet uw toepassing zich aanmelden bij uw eigen Tenant, bijvoorbeeld als u een LOB-ontwikkelaar (line-of-Business) bent. Of, in Azure Active Directory organisaties, moet uw toepassing gebruikers in uw eigen Tenant aanmelden als deze een ISV-scenario is. Deze verificatie stromen worden niet ondersteund voor persoonlijke micro soft-accounts.
- Als u de apparaatcode stroom wilt gebruiken, kunt u zich nog niet aanmelden bij gebruikers met hun persoonlijke micro soft-accounts.
- Als u gebruikers aanmeldt met sociale identiteiten die een Business-to-commerce (B2C)-instantie en-beleid door geven, kunt u alleen de interactieve verificatie en gebruikers naam-wacht woord gebruiken.

## <a name="redirect-uris"></a>Omleidings-Uri's

De omleidings-Uri's voor gebruik in een bureaublad toepassing is afhankelijk van de stroom die u wilt gebruiken.

- Als u interactieve verificatie of de code stroom van het apparaat `https://login.microsoftonline.com/common/oauth2/nativeclient`gebruikt, gebruikt u. Als u deze configuratie wilt behaalt, selecteert u de bijbehorende URL in het gedeelte **verificatie** voor uw toepassing.
  
  > [!IMPORTANT]
  > Vandaag gebruikt MSAL.NET een andere omleidings-URI standaard in bureaublad toepassingen die worden uitgevoerd`urn:ietf:wg:oauth:2.0:oob`op Windows (). In de toekomst wilt u deze standaard instelling wijzigen. u wordt aangeraden om te gebruiken `https://login.microsoftonline.com/common/oauth2/nativeclient`.

- Als u een systeem eigen doel stelling-C of SWIFT-app voor macOS bouwt, registreert u de omleidings-URI op basis van de bundel-id van uw toepassing in de volgende notatie: msauth. <your.app.bundle.id>://auth. Vervang <your.app.bundle.id> door de bundel-id van uw toepassing.
- Als uw app alleen geïntegreerde Windows-verificatie of een gebruikers naam en wacht woord gebruikt, hoeft u geen omleidings-URI voor uw toepassing te registreren. Deze stromen maken een retour afronding naar het micro soft Identity platform v 2.0-eind punt. Uw toepassing wordt niet terugaangeroepen op een specifieke URI.
- Als u de apparaatcode stroom, geïntegreerde Windows-verificatie en een gebruikers naam en wacht woord van een vertrouwelijke client toepassings stroom die geen omleidings-Uri's heeft, wilt onderscheiden (de client referentie stroom die wordt gebruikt in de daemon-toepassingen), moet u uitdrukkelijk controleren of uw toepassing een open bare client toepassing is. Voor deze configuratie gaat u naar het gedeelte **verificatie** voor uw toepassing. In de Subsectie **Geavanceerde instellingen** , in de **standaard client type** alinea, selecteert u **Ja** voor **toepassing behandelen als een open bare client**.

  ![Open bare client toestaan](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API-machtigingen

Bureau blad-Apps bellen Api's voor de aangemelde gebruiker. Ze moeten gedelegeerde machtigingen aanvragen. Ze kunnen geen toepassings machtigingen aanvragen, die alleen worden verwerkt in [daemon-toepassingen](scenario-daemon-overview.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bureau blad-app: app-configuratie](scenario-desktop-app-configuration.md)
