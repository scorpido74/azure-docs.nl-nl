---
title: Desktop-apps registreren die web-API's aanroepen - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het maken van een bureaublad-app die web-API's aanroept (app-registratie)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c55fc9eb94a88dba1ab9fc915fe84bc2dd7d4d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702178"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Bureaublad-app die web-API's aanroept: app-registratie

Dit artikel behandelt de app registratie details voor een desktop applicatie.

## <a name="supported-account-types"></a>Ondersteunde accounttypen

De accounttypen die worden ondersteund in een bureaubladtoepassing, zijn afhankelijk van de ervaring die u wilt oplichten. Vanwege deze relatie zijn de ondersteunde accounttypen afhankelijk van de stromen die u wilt gebruiken.

### <a name="audience-for-interactive-token-acquisition"></a>Publiek voor interactieve tokenacquisitie

Als uw bureaubladtoepassing interactieve verificatie gebruikt, u gebruikers aanmelden vanaf elk [accounttype.](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-desktop-app-silent-flows"></a>Doelgroep voor stille stromen voor bureaublad-apps

- Als u geïntegreerde Windows-verificatie of een gebruikersnaam en een wachtwoord wilt gebruiken, moet uw toepassing zich aanmelden bij gebruikers in uw eigen tenant, bijvoorbeeld als u een LOB-ontwikkelaar (line-of-business) bent. Of in Azure Active Directory-organisaties moet uw toepassing zich aanmelden bij gebruikers in uw eigen tenant als het een ISV-scenario is. Deze verificatiestromen worden niet ondersteund voor persoonlijke Microsoft-accounts.
- Als u de stroom van apparaatcodes wilt gebruiken, u gebruikers nog niet aanmelden met hun persoonlijke Microsoft-accounts.
- Als u zich aanmeldt voor gebruikers met een sociale identiteit die voldoen aan een Business-to-commerce (B2C) autoriteit en beleid, u alleen de interactieve en gebruikersnaam-wachtwoord authenticatie gebruiken.

## <a name="redirect-uris"></a>URI's omleiden

De omleidings-URI's die u wilt gebruiken in een bureaubladtoepassing, zijn afhankelijk van de stroom die u wilt gebruiken.

- Als u interactieve verificatie of apparaatcodestroom gebruikt, gebruikt u `https://login.microsoftonline.com/common/oauth2/nativeclient`. Als u deze configuratie wilt bereiken, selecteert u de bijbehorende URL in de sectie **Verificatie** voor uw toepassing.
  
  > [!IMPORTANT]
  > Vandaag, MSAL.NET gebruikt een andere redirect URI standaard`urn:ietf:wg:oauth:2.0:oob`in desktop toepassingen die draaien op Windows ( ). In de toekomst willen we deze standaard wijzigen, dus `https://login.microsoftonline.com/common/oauth2/nativeclient`we raden u aan .

- Als u een native Objective-C- of Swift-app voor macOS bouwt, registreert u de omleidings-URI op basis van de bundel-id van uw toepassing in de volgende indeling: msauth.<your.app.bundle.id>://auth. Vervang <your.app.bundle.id> met de bundel-id van uw toepassing.
- Als uw app alleen geïntegreerde Windows-verificatie of een gebruikersnaam en een wachtwoord gebruikt, hoeft u geen omleidingsURI voor uw toepassing te registreren. Deze stromen doen een rondreis naar het Microsoft-identiteitsplatform v2.0-eindpunt. Uw aanvraag wordt niet teruggebeld op een specifieke URI.
- Om de stroom van apparaatcodes, geïntegreerde Windows-verificatie en een gebruikersnaam en een wachtwoord te onderscheiden van een vertrouwelijke clienttoepassingsstroom die ook geen omleidings-URI's heeft (de clientreferentiestroom die wordt gebruikt in daemon-toepassingen), moet u dat uitdrukken uw aanvraag is een openbare clientapplicatie. Ga naar de sectie **Verificatie** voor uw toepassing om deze configuratie te bereiken. Selecteer in de onderafdeling **Geavanceerde instellingen** in de alinea **Standaardclienttype** de optie **Ja** voor behandeling als **openbare client**.

  ![Openbare client toestaan](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API-machtigingen

Bureaubladtoepassingen roepen API's op voor de aangemelde gebruiker. Ze moeten gedelegeerde machtigingen aanvragen. Ze kunnen geen toepassingsmachtigingen aanvragen, die alleen worden verwerkt in [daemon-toepassingen.](scenario-daemon-overview.md)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bureaublad-app: app-configuratie](scenario-desktop-app-configuration.md)
