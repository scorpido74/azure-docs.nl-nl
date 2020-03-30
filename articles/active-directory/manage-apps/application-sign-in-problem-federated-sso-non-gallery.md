---
title: Problemen met het aanmelden bij niet-galerijfederatie's app voor één aanmelding
description: Richtlijnen voor de specifieke problemen waarmee u te maken krijgen bij het aanmelden bij een toepassing die is geconfigureerd voor een op SAML gebaseerde federatieve enkele aanmelding met Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cd951f0b4d2f4887630e29cbd3b0ae429b9f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367859"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problemen met het aanmelden bij een niet-galerietoepassing die is geconfigureerd voor een gefedereerde enkele aanmelding

Om de onderstaande aanmeldingsproblemen op te lossen, raden we u aan deze suggestie te volgen om een betere diagnose te krijgen en de oplossingsstappen te automatiseren:

- Installeer de [my apps secure browser extensie](access-panel-extension-problem-installing.md) om Azure Active Directory (Azure AD) te helpen betere diagnoses en oplossingen te bieden bij het gebruik van de testervaring in de Azure-portal.
- De fout reproduceren met behulp van de testervaring op de configuratiepagina van de app in de Azure-portal. Meer informatie over [op SamL gebaseerde toepassingen voor foutopsporing saml](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Toepassing niet gevonden in directory

*Fout AADSTS70001: Toepassing `https://contoso.com` met id is niet gevonden in de map.*

**Mogelijke oorzaak**

Het kenmerk Issuer verzendt vanuit de toepassing naar Azure AD in het SAML-verzoek komt niet overeen met de id-waarde die is geconfigureerd in de toepassing Azure AD.

**Afsluiting**

Controleer of `Issuer` het kenmerk in de SAML-aanvraag overeenkomt met de id-waarde die is geconfigureerd in Azure AD. Als u de [testervaring](../azuread-dev/howto-v1-debug-saml-sso-issues.md) in de Azure-portal gebruikt met de beveiligde browserextensie Mijn apps, hoeft u deze stappen niet handmatig te volgen.

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u wilt configureren als u zich wilt aanmelden.

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Nadat de toepassing is geladen, opent u **Standaard SAML-configuratie**. Controleer of de waarde in het tekstvak Identifier overeenkomt met de waarde voor de id-waarde die in de fout wordt weergegeven.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Het antwoordadres komt niet overeen met de antwoordadressen die voor de toepassing zijn geconfigureerd. 

*Fout AADSTS50011: Het `https://contoso.com` antwoordadres komt niet overeen met de antwoordadressen die voor de toepassing zijn geconfigureerd* 

**Mogelijke oorzaak** 

De waarde AssertionConsumerServiceURL in de SAML-aanvraag komt niet overeen met de waarde of het patroon van de antwoord-URL die is geconfigureerd in Azure AD. De AssertionConsumerServiceURL-waarde in de SAML-aanvraag is de URL die u ziet in de fout. 

**Afsluiting** 

Controleer of `Issuer` het kenmerk in de SAML-aanvraag overeenkomt met de id-waarde die is geconfigureerd in Azure AD. Als u de [testervaring](../azuread-dev/howto-v1-debug-saml-sso-issues.md) in de Azure-portal gebruikt met de beveiligde browserextensie Mijn apps, hoeft u deze stappen niet handmatig te volgen.
 
1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.** 

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken. 

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.** 

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker. 

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven. 

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**
  
6. Selecteer de toepassing die u wilt configureren als u één aanmelding wilt configureren

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Nadat de toepassing is geladen, opent u **Standaard SAML-configuratie**. Controleer of werk de waarde in het `AssertionConsumerServiceURL` tekstvak Van de URL van antwoord bij om de waarde in het SAML-verzoek te evenaren.    
    
Nadat u de waarde van de URL voor beantwoorden in Azure AD hebt bijgewerkt en overeenkomt met de waarde die door de toepassing in het SAML-verzoek wordt verzonden, moet u zich bij de toepassing kunnen aanmelden.

## <a name="user-not-assigned-a-role"></a>Gebruiker die geen rol heeft toegewezen

*Fout AADSTS50105: De `brian\@contoso.com` aangemelde gebruiker is niet toegewezen aan een rol voor de toepassing*

**Mogelijke oorzaak**

De gebruiker heeft in Azure AD geen toegang gekregen tot de toepassing.

**Afsluiting**

Volg de onderstaande stappen om een of meer gebruikers rechtstreeks aan een toepassing toe te wijzen. Als u de [testervaring](../azuread-dev/howto-v1-debug-saml-sso-issues.md) in de Azure-portal gebruikt met de beveiligde browserextensie Mijn apps, hoeft u deze stappen niet handmatig te volgen.

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing waaraan u een gebruiker wilt toewijzen uit de lijst.

7. Zodra de toepassing wordt geladen, klikt u op **Gebruikers en groepen** in het linkernavigatiemenu van de toepassing.

8. Klik **op** de knop Toevoegen boven aan de lijst **Gebruikers en groepen** om het deelvenster Toewijzing **toevoegen** te openen.

9. klik op de selectie **voor gebruikers en groepen** in het deelvenster Toewijzing **toevoegen.**

10. Typ de **volledige naam** of het volledige **e-mailadres** van de gebruiker die u wilt toewijzen aan het zoekvak Zoeken op naam of **e-mailadres.**

11. Plaats de **plaats van** de gebruiker in de lijst om een **selectievakje**te onthullen. Klik op het selectievakje naast de profielfoto of het logo van de gebruiker om uw gebruiker toe te voegen aan de **lijst Geselecteerde.**

12. **Optioneel:** Als u meer dan één gebruiker wilt **toevoegen,** typt u een andere **volledige naam** of **e-mailadres** in het zoekvak **Zoeken op naam of e-mailadres** en klikt u op het selectievakje om deze gebruiker toe te voegen aan de **lijst Geselecteerde.**

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op de knop **Selecteren** om deze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** klik op de rolkiezer **selecteren** in het deelvenster **Toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik **op** de knop Toewijzen om de toepassing toe te wijzen aan de geselecteerde gebruikers.

Na een korte periode kunnen de gebruikers die u hebt geselecteerd deze toepassingen starten met behulp van de methoden die zijn beschreven in de sectie oplossingsbeschrijving.

## <a name="not-a-valid-saml-request"></a>Geen geldige SAML-aanvraag

*Fout AADSTS75005: De aanvraag is geen geldig Saml2-protocolbericht.*

**Mogelijke oorzaak**

Azure AD biedt geen ondersteuning voor de SAML-aanvraag die door de toepassing is verzonden voor eenmalige aanmelding. Enkele veelvoorkomende problemen zijn:

-   Ontbrekende vereiste velden in de SAML-aanvraag

-   Gecodeerde methode voor SAML-aanvraag

**Afsluiting**

1.  Leg SAML-verzoek vast. volg de zelfstudie over [het opsporen van SAML-gebaseerde eenmalige aanmelding voor toepassingen in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) om te leren hoe u de SAML-aanvraag vastleggen.

2.  Neem contact op met de leverancier van de toepassing en deel:

    -   SAML-aanvraag

    -   [Azure AD Single Sign-on SAML-protocolvereisten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

De leverancier van de toepassing moet valideren dat hij de Azure AD SAML-implementatie voor eenmalige aanmelding ondersteunt.

## <a name="misconfigured-application"></a>Verkeerd geconfigureerde toepassing

*Fout AADSTS650056: Verkeerd geconfigureerde toepassing. Dit kan te wijten zijn aan een van de volgende: De client heeft geen machtigingen vermeld in de gevraagde machtigingen in de aanvraagregistratie van de client. Of de beheerder heeft geen toestemming gegeven in de tenant. Of controleer de toepassings-id in het verzoek om te controleren of deze overeenkomt met de geconfigureerde clienttoepassings-id. Neem contact op met uw beheerder om de configuratie of toestemming namens de tenant te herstellen.*

**Mogelijke oorzaak**

Het `Issuer` kenmerk dat vanuit de toepassing naar Azure AD wordt verzonden in de SAML-aanvraag, komt niet overeen met de id-waarde die is geconfigureerd voor de toepassing in Azure AD.

**Afsluiting**

Controleer of `Issuer` het kenmerk in de SAML-aanvraag overeenkomt met de id-waarde die is geconfigureerd in Azure AD. Als u de [testervaring](../azuread-dev/howto-v1-debug-saml-sso-issues.md) in de Azure-portal gebruikt met de beveiligde browserextensie Mijn apps, hoeft u de volgende stappen niet handmatig te volgen:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **medebeheerder.**

1.  Open de **Azure Active Directory-extensie** door **Alle services** boven aan het linkernavigatiemenu te selecteren.

1.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

1.  Selecteer **Bedrijfstoepassingen** in het linkernavigatiemenu van Azure Active Directory.

1.  Selecteer **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen**.

1.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

1.  Nadat de toepassing is geladen, opent u **Standaard SAML-configuratie**. Controleer of de waarde in het tekstvak Identifier overeenkomt met de waarde voor de id-waarde die in de fout wordt weergegeven.

## <a name="certificate-or-key-not-configured"></a>Certificaat of sleutel niet geconfigureerd

Fout AADSTS50003: Geen ondertekeningssleutel geconfigureerd.

**Mogelijke oorzaak**

Het toepassingsobject is beschadigd en Azure AD herkent het certificaat dat is geconfigureerd voor de toepassing niet.

**Afsluiting**

Voer de onderstaande stappen uit om een nieuw certificaat te verwijderen en te maken:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u wilt configureren als u zich wilt aanmelden.

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. klik **op Nieuw certificaat maken** onder de sectie **SAML-ondertekeningscertificaat.**

9. Selecteer Vervaldatum. Klik vervolgens op **Opslaan.**

10. Schakel **Nieuw certificaat actief maken** om het actieve certificaat te overschrijven. Klik vervolgens op **Opslaan** boven in het deelvenster en accepteer het rollovercertificaat om het te activeren.

11. Klik onder de sectie **SAML-ondertekeningscertificaat** op **Verwijderen** om het **ongebruikte** certificaat te verwijderen.

## <a name="saml-request-not-present-in-the-request"></a>SAML Request niet aanwezig in het verzoek

*Fout AADSTS750054: SAMLRequest of SAMLResponse moet aanwezig zijn als querytekenreeksparameters in HTTP-aanvraag voor SAML Redirect-binding.*

**Mogelijke oorzaak**

Azure AD heeft de SAML-aanvraag niet kunnen identificeren binnen de URL-parameters in de HTTP-aanvraag. Dit kan gebeuren als de toepassing http-omleidingsbinding niet gebruikt bij het verzenden van het SAML-verzoek naar Azure AD.

**Afsluiting**

De toepassing moet de SAML-aanvraag gecodeerd in de locatieheader verzenden met HTTP redirect binding. Lees de sectie over binding voor HTTP-omleidingen in het [specificatiedocument over het SAML-protocol](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD verzendt het token naar een onjuist eindpunt

**Mogelijke oorzaak**

Als de aanmeldingsaanvraag tijdens eenmalige aanmelding geen expliciete antwoord-URL bevat (URL van de Bevestigingsconsumentenservice), selecteert Azure AD een van de geconfigureerde rely-URL's voor die toepassing. Zelfs als de toepassing een expliciete antwoord-URL heeft geconfigureerd, kan de gebruiker worden doorgestuurd. https://127.0.0.1:444 

Toen de toepassing werd toegevoegd als niet-galerie-app, is deze antwoord-URL in Azure Active Directory gemaakt als standaardwaarde. Dit gedrag is gewijzigd en deze URL wordt niet meer standaard toegevoegd in Azure Active Directory. 

**Afsluiting**

Verwijder de ongebruikte antwoord-URL's die voor de toepassing zijn geconfigureerd.

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **medebeheerder.**

2.  Open de **Azure Active Directory-extensie** door **Alle services** boven aan het linkernavigatiemenu te selecteren.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  Selecteer **Bedrijfstoepassingen** in het linkernavigatiemenu van Azure Active Directory.

5.  Selecteer **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen**.

6.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

7.  Nadat de toepassing is geladen, opent u **Standaard SAML-configuratie**. Verwijder in de URL van de **antwoord -URL (Bevestigingsconsumentenservice)** ongebruikte of standaard-antwoord-URL's die door het systeem zijn gemaakt. Bijvoorbeeld `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Probleem bij het aanpassen van de SAML-claims die naar een toepassing worden verzonden

Zie Claimtoewijzing in Azure Active Directory voor meer informatie voor meer informatie voor het aanpassen van de SAML-attribuutclaims die naar uw toepassing zijn [verzonden.](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

## <a name="next-steps"></a>Volgende stappen
[Azure AD Single Sign-on SAML-protocolvereisten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
