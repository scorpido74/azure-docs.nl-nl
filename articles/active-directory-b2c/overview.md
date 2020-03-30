---
title: Wat is Azure Active Directory B2C?
description: Lees hoe u Azure Active Directory B2C gebruiken om externe identiteiten in uw toepassingen te ondersteunen, waaronder sociale aanmelding bij Facebook, Google en andere identiteitsproviders.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 61b6d5ea903d00519c58556bc99da7065741a6e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78184057"
---
# <a name="what-is-azure-active-directory-b2c"></a>Wat is Azure Active Directory B2C?

Azure Active Directory B2C biedt de identiteit van business-to-customer als service. Uw klanten gebruiken hun favoriete sociale, zakelijke of lokale accountidentiteiten om toegang te krijgen tot uw toepassingen en API's.

![Infographic van Azure AD B2C-identiteitsproviders en downstream-toepassingen](./media/overview/azureadb2c-overview.png)

Azure Active Directory B2C (Azure AD B2C) is een CIAM-oplossing (Customer Identity Access Management) die miljoenen gebruikers en miljarden verificaties per dag kan ondersteunen. Het zorgt voor de schaling en veiligheid van het authenticatieplatform, het monitoren en automatisch afhandelen van bedreigingen zoals denial-of-service, password spray of brute force-aanvallen.

## <a name="custom-branded-identity-solution"></a>Identiteitsoplossing voor een speciaal merk

Azure AD B2C is een white-label authenticatieoplossing. U de volledige gebruikerservaring met uw merk aanpassen, zodat deze naadloos aansluit op uw web- en mobiele toepassingen.

Pas elke pagina aan die wordt weergegeven door Azure AD B2C wanneer uw gebruikers zich aanmelden, zich aanmelden en hun profielgegevens wijzigen. Pas de HTML, CSS en JavaScript aan in uw gebruikersreizen, zodat de Azure AD B2C-ervaring eruit ziet en aanvoelt als een native onderdeel van uw toepassing.

![Aangepaste aanmeldings- en aanmeldingspagina's en achtergrondafbeelding](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Toegang tot één aanmelding met een door de gebruiker verstrekte identiteit

Azure AD B2C maakt gebruik van op standaarden gebaseerde verificatieprotocollen, waaronder OpenID Connect, OAuth 2.0 en SAML. Het integreert met de meeste moderne toepassingen en commerciële kant-en-klare software.

![Diagram met identiteiten van derden die naar Azure AD B2C worden gefingeert](./media/overview/scenario-singlesignon.png)

Door te fungeren als de centrale verificatieautoriteit voor uw webtoepassingen, mobiele apps en API's, stelt Azure AD B2C u in staat om één sso-oplossing (sign-on) voor hen allemaal te bouwen. Centraliseer het verzamelen van gebruikersprofiel- en voorkeursinformatie en leg gedetailleerde analyses vast over aanmeldingsgedrag en aanmeldingsconversie.

## <a name="integrate-with-external-user-stores"></a>Integreren met externe gebruikerswinkels

Azure AD B2C biedt een map met 100 aangepaste kenmerken per gebruiker. U echter ook integreren met externe systemen. Gebruik bijvoorbeeld Azure AD B2C voor verificatie, maar delegeer naar een externe crm -database (customer relationship management) of klantenloyaliteit als de bron van de waarheid voor klantgegevens.

Een ander extern gebruikersarchiefscenario is dat Azure AD B2C de verificatie voor uw toepassing moet laten afhandelen, maar moet worden geïntegreerd met een extern systeem dat gebruikersprofielen of persoonlijke gegevens opslaat. Bijvoorbeeld om te voldoen aan de vereisten voor gegevensresidentie, zoals regionaal of on-premises beleid voor gegevensopslag.

![Een logisch diagram van Azure AD B2C dat communiceert met een externe gebruikerswinkel](./media/overview/scenario-remoteprofile.png)

Azure AD B2C kan het verzamelen van de informatie van de gebruiker tijdens registratie of profielbewerking vergemakkelijken en die gegevens vervolgens aan het externe systeem overhandigen. Vervolgens kan Azure AD B2C tijdens toekomstige verificaties de gegevens ophalen uit het externe systeem en deze indien nodig opnemen als onderdeel van het verificatietokenantwoord dat naar uw toepassing wordt verzendt.

## <a name="progressive-profiling"></a>Progressieve profilering

Een andere optie voor gebruikersreizen omvat progressieve profilering. Progressieve profilering stelt uw klanten in staat om hun eerste transactie snel te voltooien door een minimale hoeveelheid informatie te verzamelen. Verzamel vervolgens geleidelijk meer profielgegevens van de klant over toekomstige aanmeldingen.

![Een visuele weergave van progressieve profilering](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Identiteitsverificatie en -controle door derden

Gebruik Azure AD B2C om identiteitsverificatie en -controle te vergemakkelijken door gebruikersgegevens te verzamelen en deze vervolgens door te geven aan een systeem van derden om validatie, vertrouwensscore en goedkeuring voor het maken van gebruikersaccounts uit te voeren.

![Een diagram met de gebruikersstroom voor identiteitscontrole door derden](./media/overview/scenario-idproofing.png)

Dit zijn slechts enkele van de dingen die u doen met Azure AD B2C als uw business-to-customer identity platform. In de volgende secties van dit overzicht wordt u door een demotoepassing voorloopt die Azure AD B2C gebruikt. U bent ook van harte welkom om direct over te gaan tot een meer diepgaand [technisch overzicht van Azure AD B2C.](technical-overview.md)

## <a name="example-woodgrove-groceries"></a>Voorbeeld: WoodGrove Groceries

[WoodGrove Groceries][woodgrove] is een live webapplicatie die door Microsoft is gemaakt om verschillende Azure AD B2C-functies aan te tonen. In de volgende secties worden enkele verificatieopties van Azure AD B2C naar de website van WoodGrove beoordeeld.

### <a name="business-overview"></a>Bedrijfsoverzicht

WoodGrove is een online supermarkt die boodschappen verkoopt aan zowel individuele consumenten als zakelijke klanten. Hun zakelijke klanten kopen boodschappen namens hun bedrijf, of bedrijven die ze beheren.

### <a name="sign-in-options"></a>Aanmeldingsopties

WoodGrove Groceries biedt verschillende aanmeldingsopties op basis van de relatie die hun klanten hebben met de winkel:

* **Individuele** klanten kunnen zich aanmelden of zich aanmelden met individuele accounts, zoals met een aanbieder van sociale identiteit of een e-mailadres en wachtwoord.
* **Zakelijke** klanten kunnen zich aanmelden of zich aanmelden met hun bedrijfsreferenties.
* **Partners** en leveranciers zijn particulieren die de supermarkt voorzien van producten om te verkopen. Partneridentiteit wordt geleverd door [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).

![Individuele (B2C), business (B2C) en partner (B2B) aanmeldingspagina's](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Individuele klanten verifiëren

Wanneer een klant **Aanmelden selecteert met uw persoonlijke account,** worden deze doorgestuurd naar een aangepaste aanmeldingspagina die wordt gehost door Azure AD B2C. U in de volgende afbeelding zien dat we de gebruikersinterface (UI) hebben aangepast om er net zo uit te zien en te voelen als de WoodGrove Groceries-website. Klanten van WoodGrove moeten zich er niet van bewust zijn dat de verificatie-ervaring wordt gehost en beveiligd door Azure AD B2C.

![Aanmeldingspagina voor aangepaste WoodGrove die wordt gehost door Azure AD B2C](./media/overview/sign-in.png)

Met WoodGrove kunnen hun klanten zich aanmelden en zich aanmelden met hun Google-, Facebook- of Microsoft-accounts als hun identiteitsprovider. Of ze kunnen zich aanmelden met behulp van hun e-mailadres en een wachtwoord om een zogenaamd *lokaal account*te maken.

Wanneer een klant Zich aanmeldt **met uw persoonlijke account** en zich vervolgens **aanmeldt,** krijgt deze persoon een aangepaste aanmeldingspagina te zien.

![Aangepaste aanmeldingspagina voor WoodGrove die wordt gehost door Azure AD B2C](./media/overview/sign-up.png)

Nadat u een e-mailadres hebt ingevoerd en **verificatiecode verzenden**hebt geselecteerd, stuurt Azure AD B2C deze code. Zodra ze hun code invoeren, selecteer **code verifiëren**en voer vervolgens de andere informatie op het formulier in, moeten ze ook instemmen met de servicevoorwaarden.

Als u op de knop **Maken** klikt, wordt Azure AD B2C omgeleid naar de website van WoodGrove Groceries. Wanneer azure AD B2C wordt omgeleid, geeft het een OpenID Connect-verificatietoken door aan de WoodGrove-webtoepassing. De gebruiker is nu aangemeld en klaar om te gaan, hun weergavenaam weergegeven in de rechterbovenhoek om aan te geven dat ze zijn aangemeld.

![WoodGrove Groceries website header met gebruiker is aangemeld](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Zakelijke klanten verifiëren

Wanneer een klant een van de opties selecteert onder **Zakelijke klanten,** beroept de website Van WoodGrove Groceries zich op een ander Azure AD B2C-beleid dan voor individuele klanten.

Dit beleid biedt de gebruiker een optie om zijn bedrijfsreferenties te gebruiken voor aanmelding en aanmelding. In het voorbeeld WoodGrove wordt gebruikers gevraagd zich aan te melden met een Office 365- of Azure AD-account. Dit beleid maakt gebruik van een `/common` [Azure AD-toepassing met meerdere tenant's](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) en het Azure AD-eindpunt om Azure AD B2C te beoordelen met elke Office 365-klant ter wereld.

### <a name="authenticate-partners"></a>Partners verifiëren

De koppeling **Aanmelden met uw leveranciersaccount** maakt gebruik van de samenwerkingsfunctionaliteit van Azure Active Directory B2B. Azure AD B2B is een reeks functies in Azure Active Directory om partneridentiteiten te beheren. Deze identiteiten kunnen worden gefedereerd vanuit Azure Active Directory voor toegang tot azure AD B2C-beveiligde toepassingen.

Meer informatie over Azure AD B2B in [Wat is toegang voor gastgebruikers in Azure Active Directory B2B?](../active-directory/b2b/what-is-b2b.md).

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Volgende stappen

Nu u een idee hebt van wat Azure AD B2C is en sommige van de scenario's waarmee het kan helpen, u wat dieper ingaan op de functies en technische aspecten.

> [!div class="nextstepaction"]
> [Technisch overzicht van Azure AD B2C >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo
