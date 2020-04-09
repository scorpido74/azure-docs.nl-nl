---
title: Microsoft-identiteitsplatform & saml-beweringsstroom aan toonder | Azure
description: Meer informatie over het ophalen van gegevens uit Microsoft Graph zonder de gebruiker te vragen om referenties met behulp van de SAML-beweringsstroom voor dragers.
services: active-directory
author: umeshbarapatre
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1cd79b1f9e4cd3afadee250da0c184c0c5b8ac07
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886174"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft-identiteitsplatform en OAuth 2.0 SAML-beweringsstroom
Met de oauth 2.0 SAML-claimstroom voor dragers u een OAuth-toegangstoken aanvragen met behulp van een SAML-bewering wanneer een client een bestaande vertrouwensrelatie moet gebruiken. De handtekening die is toegepast op de SAML-bewering biedt verificatie van de geautoriseerde app. Een SAML-bewering is een XML-beveiligingstoken dat is uitgegeven door een identiteitsprovider en wordt verbruikt door een serviceprovider. De serviceprovider vertrouwt op de inhoud ervan om het onderwerp van de bewering te identificeren voor beveiligingsdoeleinden.

De SAML-bewering wordt geplaatst op het Eindpunt van het OAuth-token.  Het eindpunt verwerkt de bewering en geeft een toegangstoken uit op basis van voorafgaande goedkeuring van de app. De client hoeft geen vernieuwingstoken te hebben of op te slaan, noch moet het clientgeheim worden doorgegeven aan het token-eindpunt.

Saml-beweringsstroom voor dragers is handig bij het ophalen van gegevens uit Microsoft Graph API's (die alleen gedelegeerde machtigingen ondersteunen) zonder de gebruiker om referenties te vragen. In dit scenario werkt de clientreferentiessubsidie, die de voorkeur heeft voor achtergrondprocessen, niet.

Voor toepassingen die interactieve browser-gebaseerde aanmelding doen om een SAML-bewering te krijgen en vervolgens toegang willen toevoegen tot een door OAuth beveiligde API (zoals Microsoft Graph), u een OAuth-verzoek indienen om een toegangstoken voor de API te krijgen. Wanneer de browser wordt doorgestuurd naar Azure AD om de gebruiker te verifiëren, neemt de browser de sessie op van de SAML-aanmelding en hoeft de gebruiker zijn referenties niet in te voeren.

De OAuth SAML-claimstroom voor dragers wordt ook ondersteund voor gebruikers die authenticeren met identiteitsproviders zoals Active Directory Federation Services (ADFS) die worden gefedereerd naar Azure Active Directory.  De SAML-bewering van ADFS kan worden gebruikt in een OAuth-stroom om de gebruiker te verifiëren.

![OAuth-stroom](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Oproepgrafiek met SAML-bewering aan toonder
Laten we nu begrijpen hoe we daadwerkelijk kunnen halen SAML bewering programatically. Deze aanpak wordt getest met ADFS. Dit werkt echter met elke identiteitsprovider die de terugkeer van SAML-bewering programmatisch ondersteunt. Het basisproces is: krijg een SAML-bewering, krijg een toegangstoken en krijg toegang tot Microsoft Graph.

### <a name="prerequisites"></a>Vereisten

Een vertrouwensrelatie opbouwen tussen de autorisatieserver/-omgeving (Microsoft 365) en de identiteitsprovider of uitgever van de SAML 2.0-claim aan toonder (ADFS). Als u ADFS wilt configureren voor eenmalige aanmelding en als identiteitsprovider, u [naar dit artikel](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/)verwijzen.

Registreer de aanvraag in het [portaal:](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
1. Meld u aan bij het [app-registratieblad van de portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (Houd er rekening mee dat we de v2.0-eindpunten voor Graph API gebruiken en daarom de toepassing in deze portal moeten registreren. Anders hadden we de registraties in azure active directory kunnen gebruiken). 
1. Selecteer **Nieuwe registratie**.
1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in: 
    1. **Naam**: voer een beschrijvende toepassingsnaam in. Deze wordt zichtbaar voor gebruikers van de app.
    1. **Ondersteunde accounttypen**: selecteer voor welke accounts de toepassing ondersteuning moet bieden.
    1. **Uri omleiden (optioneel)** - Selecteer het type app dat u bouwt, web of openbare client (mobile & desktop) en voer vervolgens de omleiding URI (of antwoord-URL) in voor uw toepassing.
    1. Selecteer **Registreren** wanneer u klaar bent.
1. Noteer de applicatie (client) ID.
1. Selecteer certificaten & **geheimen**in het linkerdeelvenster. Klik **op Nieuw klantgeheim** in de sectie **Klantgeheimen.** Kopieer het nieuwe klantgeheim, u het niet ophalen wanneer u het mes verlaat.
1. Selecteer **api-machtigingen** in het linkerdeelvenster en **voeg vervolgens een machtiging toe.** Selecteer **Microsoft Graph,** vervolgens **gedelegeerde machtigingen**en selecteer **Taken.read** omdat we van plan zijn de Api voor Outlook Graph te gebruiken. 

Installeer [Postman](https://www.getpostman.com/), een hulpmiddel dat nodig is om de voorbeeldaanvragen te testen.  Later u de aanvragen converteren naar code.

### <a name="get-the-saml-assertion-from-adfs"></a>Krijg de SAML-bewering van ADFS
Maak een POST-verzoek naar het ADFS-eindpunt met soapenvelop om de SAML-bewering op te halen:

![SamL-bewering krijgen](./media/v2-saml-bearer-assertion/2.png)

Koptekstwaarden:

![Koptekstwaarden](./media/v2-saml-bearer-assertion/3.png)

ADFS-aanvraaginstantie:

![ADFS-aanvraaginstantie](./media/v2-saml-bearer-assertion/4.png)

Zodra dit verzoek is geplaatst, ontvangt u een SAML-bewering van ADFS. Alleen de **SAML:Assertion** tag gegevens is vereist, converteren naar base64 codering te gebruiken in verdere verzoeken.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Het OAuth2-token krijgen met behulp van de SAML-bewering 
Haal in deze stap een OAuth2-token op met behulp van de ADFS-beweringsreactie.

1. Maak een post-aanvraag zoals hieronder weergegeven met de kopwaarden:

    ![POST-verzoek](./media/v2-saml-bearer-assertion/5.png)
1. Vervang in de tekst van het verzoek **client_id,** **client_secret**en **bewering** (de bewering van de basis64 gecodeerde SAML-bewering verkregen de vorige stap):

    ![Aanvraagbody](./media/v2-saml-bearer-assertion/6.png)
1. Op een succesvol verzoek ontvangt u een toegangstoken van de Active Directory van Azure.

### <a name="get-the-data-with-the-oauth-token"></a>De gegevens opvragen met het Oauth-token

Bel na ontvangst van het toegangstoken de Graph API's (Outlook-taken in dit voorbeeld). 

1. Maak een GET-aanvraag met het toegangstoken dat in de vorige stap is opgehaald:

    ![GET-aanvraag](./media/v2-saml-bearer-assertion/7.png)

1. Op een succesvol verzoek ontvangt u een JSON-antwoord.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende [verificatiestromen en toepassingsscenario's](authentication-flows-app-scenarios.md).