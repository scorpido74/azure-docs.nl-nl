---
title: Problemen bij het aanmelden bij een niet-galerie federatieve eenmalige aanmelding-app
description: Richt lijnen voor de specifieke problemen die u kunt tegen komen wanneer u zich aanmeldt bij een toepassing die is geconfigureerd voor op SAML gebaseerde federatieve eenmalige aanmelding met Azure AD
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
ms.openlocfilehash: 0b8aac627936aef2cfa79bbd92d6163fe40b4d32
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274855"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problemen bij het aanmelden bij een niet-galerie toepassing die is geconfigureerd voor federatieve eenmalige aanmelding

Als u de onderstaande aanmeldings problemen wilt oplossen, raden we u aan deze suggestie te volgen om betere diagnose te krijgen en de oplossings stappen te automatiseren:

- Installeer de [beveiligde browser uitbreiding mijn apps](access-panel-extension-problem-installing.md) om Azure Active Directory (Azure AD) te helpen bij het verbeteren van de test ervaring in de Azure Portal.
- Reproduceer de fout met behulp van de test ervaring op de pagina app-configuratie in de Azure Portal. Meer informatie over het [opsporen van op SAML gebaseerde toepassingen voor eenmalige aanmelding](../develop/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Kan de toepassing niet vinden in de map

*Fout AADSTS70001: de toepassing met de id `https://contoso.com` is niet gevonden in de map*.

**Mogelijke oorzaak**

Het kenmerk van de verlener verzendt van de toepassing naar Azure AD in de SAML-aanvraag komt niet overeen met de id-waarde die is geconfigureerd in de toepassing Azure AD.

**Opgelost**

Zorg ervoor dat het kenmerk `Issuer` in de SAML-aanvraag overeenkomt met de id-waarde die is geconfigureerd in azure AD. Als u de [test ervaring](../develop/howto-v1-debug-saml-sso-issues.md) in de Azure Portal met de beveiligde browser uitbreiding mijn apps gebruikt, hoeft u deze stappen niet hand matig uit te voeren.

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Nadat de toepassing is geladen, opent u **Standaard SAML-configuratie**. Controleer of de waarde in het tekstvak id overeenkomt met de waarde voor de id-waarde die wordt weer gegeven in de fout.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Het antwoord adres komt niet overeen met de antwoord adressen die voor de toepassing zijn geconfigureerd. 

*Fout AADSTS50011: het antwoord adres `https://contoso.com` komt niet overeen met de antwoord adressen die voor de toepassing zijn geconfigureerd* 

**Mogelijke oorzaak** 

De AssertionConsumerServiceURL-waarde in de SAML-aanvraag komt niet overeen met de antwoord-URL-waarde of het patroon dat is geconfigureerd in azure AD. De AssertionConsumerServiceURL-waarde in de SAML-aanvraag is de URL die u in de fout ziet. 

**Opgelost** 

Zorg ervoor dat het kenmerk `Issuer` in de SAML-aanvraag overeenkomt met de id-waarde die is geconfigureerd in azure AD. Als u de [test ervaring](../develop/howto-v1-debug-saml-sso-issues.md) in de Azure Portal met de beveiligde browser uitbreiding mijn apps gebruikt, hoeft u deze stappen niet hand matig uit te voeren.
 
1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.** 

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant. 

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item. 

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant. 

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven. 

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**
  
6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Nadat de toepassing is geladen, opent u **Standaard SAML-configuratie**. Controleer of werk de waarde in het tekstvak antwoord-URL in om overeen te komen met de `AssertionConsumerServiceURL` waarde in de SAML-aanvraag.    
    
Nadat u de waarde voor de antwoord-URL in azure AD hebt bijgewerkt en deze overeenkomt met de waarde die door de toepassing in de SAML-aanvraag is verzonden, moet u zich kunnen aanmelden bij de toepassing.

## <a name="user-not-assigned-a-role"></a>Gebruiker heeft geen rol toegewezen

*Fout AADSTS50105: de aangemelde gebruiker `brian\@contoso.com` is niet toegewezen aan een rol voor de toepassing*

**Mogelijke oorzaak**

De gebruiker heeft geen toegang gekregen tot de toepassing in azure AD.

**Opgelost**

Volg de onderstaande stappen om een of meer gebruikers rechtstreeks toe te wijzen aan een toepassing. Als u de [test ervaring](../develop/howto-v1-debug-saml-sso-issues.md) in de Azure Portal met de beveiligde browser uitbreiding mijn apps gebruikt, hoeft u deze stappen niet hand matig uit te voeren.

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing die u wilt toewijzen van een gebruiker in de lijst.

7. Zodra de toepassing is geladen, klikt u op **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

8. Klik op de knop **toevoegen** boven aan de lijst **gebruikers en groepen** om het deel venster **toewijzing toevoegen** te openen.

9. Klik op de selector **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

10. Typ de **volledige naam** of het **e-mail adres** van de gebruiker die u wilt toewijzen in het zoekvak **zoeken op naam of e-mail adres** .

11. Beweeg de muis aanwijzer over de **gebruiker** in de lijst om een **selectie vakje**weer te geven. Klik op het selectie vakje naast de foto of het logo van het profiel van de gebruiker om uw gebruiker toe te voegen aan de **geselecteerde** lijst.

12. **Optioneel:** Als u **meer dan één gebruiker wilt toevoegen**, typt u een andere **volledige naam** of een ander **e-mail adres** in het zoekvak **zoeken op naam of e-mail adres** en klikt u op het selectie vakje om deze gebruiker aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** Klik op het selectie vakje **rol** selecteren in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik op de knop **toewijzen** om de toepassing aan de geselecteerde gebruikers toe te wijzen.

Na een korte periode, de gebruikers die u hebt geselecteerd mogelijk om deze toepassingen met behulp van de methoden die worden beschreven in de sectie oplossing beschrijving te starten.

## <a name="not-a-valid-saml-request"></a>Geen geldige SAML-aanvraag

*Fout AADSTS75005: de aanvraag is geen geldig Saml2-protocol bericht.*

**Mogelijke oorzaak**

Azure AD biedt geen ondersteuning voor de SAML-aanvraag die door de toepassing is verzonden voor eenmalige aanmelding. Enkele veelvoorkomende problemen zijn:

-   Ontbrekende vereiste velden in de SAML-aanvraag

-   Versleutelde methode van SAML-aanvraag

**Opgelost**

1.  SAML-aanvraag vastleggen. Volg de zelf studie over het [opsporen van op SAML gebaseerde eenmalige aanmelding bij toepassingen in azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) voor meer informatie over het vastleggen van de SAML-aanvraag.

2.  Neem contact op met de leverancier van de toepassing en deel:

    -   SAML-aanvraag

    -   [Vereisten voor het SAML-protocol voor eenmalige aanmelding voor Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

De leverancier van de toepassing moet valideren dat de Azure AD SAML-implementatie voor eenmalige aanmelding wordt ondersteund.

## <a name="misconfigured-application"></a>Onjuist geconfigureerde toepassing

*Fout AADSTS650056: onjuist geconfigureerde toepassing. Dit kan een van de volgende oorzaken hebben: de client heeft geen machtigingen voor de ' AAD graph ' vermeld in de aangevraagde machtigingen in de registratie van de client toepassing. Of de beheerder heeft geen toestemming gegeven in de Tenant. Of Controleer de toepassings-id in de aanvraag om er zeker van te zijn dat deze overeenkomt met de geconfigureerde client toepassings-id. Neem contact op met uw beheerder om de configuratie of instemming namens de Tenant op te lossen.*

**Mogelijke oorzaak**

Het `Issuer` kenmerk dat vanuit de toepassing naar Azure AD is verzonden in de SAML-aanvraag komt niet overeen met de id-waarde die is geconfigureerd voor de toepassing in azure AD.

**Opgelost**

Zorg ervoor dat het kenmerk `Issuer` in de SAML-aanvraag overeenkomt met de id-waarde die is geconfigureerd in azure AD. Als u de [test ervaring](../develop/howto-v1-debug-saml-sso-issues.md) in de Azure Portal met de beveiligde browser uitbreiding mijn apps gebruikt, hoeft u deze stappen niet hand matig uit te voeren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-** beheerder.

1.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.

1.  Typ **' Azure Active Directory '** in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

1.  Selecteer **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

1.  Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen**in.

1.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

1.  Nadat de toepassing is geladen, opent u **Standaard SAML-configuratie**. Controleer of de waarde in het tekstvak id overeenkomt met de waarde voor de id-waarde die wordt weer gegeven in de fout.

## <a name="certificate-or-key-not-configured"></a>Het certificaat of de sleutel is niet geconfigureerd

Fout AADSTS50003: er is geen handtekening sleutel geconfigureerd.

**Mogelijke oorzaak**

Het toepassings object is beschadigd en het certificaat dat is geconfigureerd voor de toepassing wordt niet herkend door Azure AD.

**Opgelost**

Voer de volgende stappen uit om een nieuw certificaat te verwijderen en te maken:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Klik op **Nieuw certificaat maken** onder de sectie **SAML-handtekening certificaat** .

9. Selecteer een verval datum. Klik vervolgens op **opslaan.**

10. Schakel het selectie vakje **Nieuw certificaat actief maken** in om het actieve certificaat te onderdrukken. Klik vervolgens op **Opslaan** boven in het deelvenster en accepteer het rollovercertificaat om het te activeren.

11. Klik onder de sectie **SAML-handtekening certificaat** op **verwijderen** om het **ongebruikte** certificaat te verwijderen.

## <a name="saml-request-not-present-in-the-request"></a>SAML-aanvraag is niet aanwezig in de aanvraag

*Fout AADSTS750054: SAMLRequest of SAMLResponse moet aanwezig zijn als query reeks parameters in HTTP-aanvraag voor de binding van SAML-omleiding.*

**Mogelijke oorzaak**

Azure AD kan de SAML-aanvraag niet identificeren binnen de URL-para meters in de HTTP-aanvraag. Dit kan gebeuren als de toepassing geen gebruik maakt van een binding voor HTTP-omleiding bij het verzenden van de SAML-aanvraag naar Azure AD.

**Opgelost**

De toepassing moet de SAML-aanvraag die is gecodeerd naar de locatie header verzenden met behulp van binding van HTTP-omleiding. Lees de sectie over binding voor HTTP-omleidingen in het [specificatiedocument over het SAML-protocol](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD stuurt het token naar een onjuist eind punt

**Mogelijke oorzaak**

Als de aanmeldings aanvraag bij eenmalige aanmelding geen expliciete antwoord-URL (assertion Consumer Service-URL) bevat, selecteert Azure AD een van de geconfigureerde vertrouwens-Url's voor die toepassing. Zelfs als de toepassing een expliciete antwoord-URL heeft geconfigureerd, is het mogelijk dat de gebruiker https://127.0.0.1:444omgeleid. 

Toen de toepassing werd toegevoegd als niet-galerie-app, is deze antwoord-URL in Azure Active Directory gemaakt als standaardwaarde. Dit gedrag is gewijzigd en deze URL wordt niet meer standaard toegevoegd in Azure Active Directory. 

**Opgelost**

Verwijder de ongebruikte antwoord-Url's die voor de toepassing zijn geconfigureerd.

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-** beheerder.

2.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.

3.  Typ **' Azure Active Directory '** in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Selecteer **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen**in.

6.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

7.  Nadat de toepassing is geladen, opent u **Standaard SAML-configuratie**. Verwijder in de **antwoord-URL (assertion Consumer Service-URL)** ongebruikte of standaard antwoord-url's die zijn gemaakt door het systeem. Bijvoorbeeld `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Probleem bij het aanpassen van de SAML-claims die worden verzonden naar een toepassing

Zie [claim toewijzing in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) voor meer informatie over het aanpassen van de SAML-kenmerk claims die naar uw toepassing worden verzonden.

## <a name="next-steps"></a>Volgende stappen
[Vereisten voor het SAML-protocol voor eenmalige aanmelding voor Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
