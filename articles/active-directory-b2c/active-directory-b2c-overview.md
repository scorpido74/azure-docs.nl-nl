---
title: Wat is Azure Active Directory B2C?
description: Meer informatie over hoe u Azure Active Directory B2C kunt gebruiken ter ondersteuning van externe identiteiten in uw toepassingen, waaronder sociale aanmelding bij Facebook, Google en andere id-providers.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 007b23f07afec6163c2158feb3f17ba71e44bdb5
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71120675"
---
# <a name="what-is-azure-active-directory-b2c"></a>Wat is Azure Active Directory B2C?

Azure Active Directory B2C biedt identiteit van Business-to-Customer als een service. Uw klanten gebruiken hun voorkeurs identiteiten voor sociale netwerken, ondernemingen of lokale accounts om toegang te krijgen tot eenmalige aanmelding voor uw toepassingen en Api's.

![Infographic van Azure AD B2C-id-providers en downstream-toepassingen](media/active-directory-b2c-overview/azureadb2c-overview.png)

Azure Active Directory B2C (Azure AD B2C) is een CIAM-oplossing (Customer Identity Access Management) waarmee miljoenen gebruikers en miljarden verificaties per dag kunnen worden ondersteund. Het zorgt voor de schaal en veiligheid van het verificatie platform, het bewaken en automatisch verwerken van bedreigingen zoals denial-of-service-, wachtwoord-en beveiligings aanvallen.

## <a name="custom-branded-identity-solution"></a>Oplossing met aangepaste merk identiteit

Azure AD B2C is een oplossing voor het verifiëren van witte labels. U kunt de volledige gebruikers ervaring met uw merk aanpassen zodat deze naadloos wordt gemengd met uw web-en mobiele toepassingen.

Pas elke pagina aan die wordt weer gegeven door Azure AD B2C wanneer uw gebruikers zich aanmelden, zich aanmelden en hun profiel gegevens wijzigen. U kunt de HTML, CSS en Java script in uw gebruikers reizen aanpassen zodat de Azure AD B2C ervaring eruitziet en lijkt alsof het een eigen deel van uw toepassing is.

![Aangepaste registratie-en aanmeldings pagina's en achtergrond afbeelding](media/active-directory-b2c-overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Toegang met eenmalige aanmelding met een door de gebruiker gedefinieerde identiteit

Azure AD B2C gebruikt op standaarden gebaseerde verificatie protocollen, waaronder OpenID Connect Connect, OAuth 2,0 en SAML. Het is geïntegreerd met de meeste moderne toepassingen en commerciële software.

![Diagram van identiteiten van derden federeren naar Azure AD B2C](media/active-directory-b2c-overview/scenario-singlesignon.png)

Met behulp van de centrale verificatie-instantie voor uw webtoepassingen, mobiele apps en Api's kunt u met Azure AD B2C een SSO-oplossing (single sign-on) maken voor alle toepassingen. Centraliseer de verzameling van gebruikers profielen en de voorkeurs informatie en leg gedetailleerde analyses vast over aanmeldings gedrag en registratie-conversie.

## <a name="integrate-with-external-user-stores"></a>Integreren met externe gebruikers archieven

Azure AD B2C biedt een map die aangepaste kenmerken van 100 kan bevatten per gebruiker. U kunt echter ook integreren met externe systemen. Gebruik bijvoorbeeld Azure AD B2C voor verificatie, maar delegeren aan een externe klant relatiebeheer (CRM) of klant loyale Data Base als de bron van een waarheid voor klant gegevens.

Een ander scenario voor het opslaan van externe gebruikers is om de verificatie voor uw toepassing te Azure AD B2C laten afhandelen, maar te integreren met een extern systeem waarmee gebruikers profielen of persoons gegevens worden opgeslagen. Om bijvoorbeeld te voldoen aan de vereisten voor gegevens locatie, zoals een regionaal of een on-premises beleid voor gegevens opslag.

![Een logisch diagram van Azure AD B2C het communiceren met een extern gebruikers archief](media/active-directory-b2c-overview/scenario-remoteprofile.png)

Azure AD B2C kunt het verzamelen van de gegevens van de gebruiker tijdens de registratie of het bewerken van het profiel vergemakkelijken en vervolgens de gegevens naar het externe systeem hand matig. Tijdens toekomstige verificaties kan Azure AD B2C de gegevens van het externe systeem ophalen en, indien nodig, opnemen als onderdeel van het verificatie token antwoord dat wordt verzonden naar uw toepassing.

## <a name="progressive-profiling"></a>Progressieve profilering

Een andere reis optie voor een gebruiker bevat progressieve profilering. Met de progressieve profilering kunnen uw klanten snel hun eerste trans actie volt ooien door een minimale hoeveelheid informatie te verzamelen. Verzamelt vervolgens geleidelijk meer profiel gegevens van de klant voor toekomstige aanmeldingen.

![Een visuele voors telling van progressieve profilering](media/active-directory-b2c-overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Verificatie en controle van de identiteit van derden

Gebruik Azure AD B2C om identiteits verificatie en controle te vereenvoudigen door gebruikers gegevens te verzamelen en deze door te geven aan een systeem van derden om validatie, vertrouwens scores en goed keuring van het maken van een gebruikers account uit te voeren.

![Een diagram van de gebruikers stroom voor identiteits controle van derden](media/active-directory-b2c-overview/scenario-idproofing.png)

Dit zijn slechts enkele van de dingen die u kunt doen met Azure AD B2C als uw Business-to-Customer-identiteits platform. In de volgende secties van dit overzicht vindt u een demonstratie toepassing die gebruikmaakt van Azure AD B2C. U kunt ook rechtstreeks naar een dieper [technisch overzicht van Azure AD B2C](technical-overview.md)gaan.

## <a name="example-woodgrove-groceries"></a>Voorbeeld: WoodGrove-boodschappen

[WoodGrove-boodschappen][woodgrove] zijn een live web-app die door micro soft is gemaakt om verschillende Azure AD B2C functies te demonstreren. In de volgende secties worden enkele van de verificatie opties van Azure AD B2C op de WoodGrove-website gecontroleerd.

### <a name="business-overview"></a>Bedrijfs overzicht

WoodGrove is een online winkel voor de winkels waarmee de boodschappen worden verkocht aan individuele consumenten en zakelijke klanten. Hun zakelijke klanten kopen hun boodschappen namens hun bedrijf of bedrijven die ze beheren.

### <a name="sign-in-options"></a>Aanmeldingsopties

WoodGrove-boodschappen bieden verschillende aanmeldings opties op basis van de relatie die hun klanten hebben met de Store:

* **Individuele** klanten kunnen zich aanmelden of zich aanmelden met afzonderlijke accounts, zoals een id-provider voor sociale netwerken of een e-mail adres en wacht woord.
* **Zakelijke** klanten kunnen zich aanmelden of zich aanmelden met hun bedrijfs referenties.
* **Partners** en leveranciers zijn personen die de Store van de boodschappen leveren met producten om te verkopen. De identiteit van de partner wordt verschaft door [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).

![Afzonderlijke aanmeldings pagina's (B2C), Business (B2C) en partners (B2B)](media/active-directory-b2c-overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Afzonderlijke klanten verifiëren

Wanneer een klant **zich aanmeldt met uw persoonlijke account**, wordt deze omgeleid naar een aangepaste aanmeldings pagina die wordt gehost door Azure AD B2C. U kunt in de volgende afbeelding zien dat de gebruikers interface (UI) zo is aangepast dat deze eruitziet als de website van de WoodGrove-boodschappen. Klanten van WoodGrove moeten zich niet bewust zijn dat de verificatie-ervaring wordt gehost en beveiligd door Azure AD B2C.

![Aangepaste WoodGrove-aanmeldings pagina gehost door Azure AD B2C](media/active-directory-b2c-overview/sign-in.png)

Met WoodGrove kunnen hun klanten zich registreren en aanmelden met hun Google-, Facebook-of micro soft-account als id-provider. Of ze kunnen zich registreren door hun e-mail adres en een wacht woord te gebruiken om te maken wat een *lokaal account*wordt genoemd.

Wanneer een klant **zich aanmeldt met uw persoonlijke account** en **zich nu aanmeldt**, wordt er een aangepaste aanmeldings pagina weer gegeven.

![Aangepaste WoodGrove-aanmeldings pagina die wordt gehost door Azure AD B2C](media/active-directory-b2c-overview/sign-up.png)

Nadat u een e-mail adres hebt ingevoerd en **verificatie code verzenden**hebt geselecteerd, worden de code door Azure AD B2C verzonden. Als ze hun code hebben ingevoerd, selecteert u **code verifiëren**en voert u de overige gegevens in het formulier in. ze moeten ook akkoord gaan met de service voorwaarden.

Als u op de knop **maken** klikt, wordt Azure AD B2C de gebruiker teruggeleid naar de website van de WoodGrove-boodschappen. Wanneer dit wordt omgeleid, wordt door Azure AD B2C een verificatie token voor OpenID Connect Connect door gegeven aan de WoodGrove-webtoepassing. De gebruiker is nu aangemeld en klaar voor gebruik. de weergave naam wordt weer gegeven in de rechter bovenhoek om aan te geven dat ze zijn aangemeld.

![Website van WoodGrove-boodschappen waarop de gebruiker is aangemeld](media/active-directory-b2c-overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Zakelijke klanten verifiëren

Wanneer een klant een van de opties onder **zakelijke klanten**selecteert, roept de website van de WoodGrove-boodschappen een andere Azure AD B2C-beleid op dan voor individuele klanten.

Dit beleid geeft de gebruiker de mogelijkheid om hun bedrijfs referenties te gebruiken voor registratie en aanmelding. In het WoodGrove-voor beeld wordt gebruikers gevraagd zich aan te melden met een Office 365-of Azure AD-account. Dit beleid maakt gebruik van een [Azure AD-toepassing met meerdere tenants](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) en het `/common` Azure AD-eind punt om Azure AD B2C te communiceren met elke Office 365-klant in de wereld.

### <a name="authenticate-partners"></a>Partners verifiëren

De koppeling **Aanmelden met uw leveranciers account** maakt gebruik van Azure Active Directory B2B's samenwerkings functionaliteit. Azure AD B2B is een reeks functies in Azure Active Directory om partner identiteiten te beheren. Deze identiteiten kunnen worden afgeleid van Azure Active Directory voor toegang tot met Azure AD B2C beveiligde toepassingen.

Meer informatie over Azure AD B2B in [Wat is toegang tot gast gebruikers in azure Active Directory B2B?](../active-directory/b2b/what-is-b2b.md).

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Volgende stappen

Nu u een idee hebt van wat Azure AD B2C is en enkele van de scenario's waarmee het kan helpen, kunt u een beetje dieper in de functies en technische aspecten.

> [!div class="nextstepaction"]
> [Technisch overzicht van Azure AD B2C >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo
