---
title: Azure AD-app SAML-tokenclaims aanpassen
titleSuffix: Microsoft identity platform
description: Meer informatie over het aanpassen van de claims die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen in Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 87a9632ec2433b8698e3ae3761ba733aa6bc63a5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885681"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Hoe: claims aanpassen die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen

Tegenwoordig ondersteunt Azure Active Directory (Azure AD) eenmalige aanmelding (SSO) met de meeste bedrijfstoepassingen, waaronder beide toepassingen die vooraf zijn geïntegreerd in de azure AD-app-galerie en aangepaste toepassingen. Wanneer een gebruiker zich verifieert naar een toepassing via Azure AD met behulp van het SAML 2.0-protocol, stuurt Azure AD een token naar de toepassing (via een HTTP-BERICHT). En vervolgens valideert de toepassing en gebruikt het token om de gebruiker in te loggen in plaats van te vragen om een gebruikersnaam en wachtwoord. Deze SAML-tokens bevatten stukjes informatie over de gebruiker die bekend staat als *claims.*

Een *claim* is informatie die een identiteitsprovider vermeldt over een gebruiker in het token dat hij voor die gebruiker uitgeeft. In [SAML-token](https://en.wikipedia.org/wiki/SAML_2.0)worden deze gegevens meestal weergegeven in de SAML-kenmerkinstructie. De unieke id van de gebruiker wordt meestal weergegeven in het SAML-onderwerp dat ook wel naam-id wordt genoemd.

Azure AD geeft standaard een SAML-token af `NameIdentifier` aan uw toepassing die een claim bevat met een waarde van de gebruikersnaam van de gebruiker (ook wel de gebruikersnaam genoemd) in Azure AD, die de gebruiker op unieke wijze kan identificeren. Het SAML-token bevat ook aanvullende claims die het e-mailadres, de voornaam en de achternaam van de gebruiker bevatten.

Als u de claims die zijn uitgegeven in het SAML-token voor de toepassing wilt weergeven of bewerken, opent u de toepassing in azure-portal. Open vervolgens de sectie **Gebruikerskenmerken & Claims.**

![De sectie Gebruikerskenmerken & claims openen in de Azure-portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Er zijn twee mogelijke redenen waarom u de claims die zijn uitgegeven in het SAML-token moet bewerken:

* De toepassing `NameIdentifier` vereist dat de claim of NameID iets anders is dan de gebruikersnaam (of gebruikersnaam) die is opgeslagen in Azure AD.
* De aanvraag is geschreven om een andere set claim-URI's of claimwaarden te vereisen.

## <a name="editing-nameid"></a>Naam-ID bewerken

De naam-id bewerken (waarde van de naam-id):

1. Open de **waardepagina naam-id.**
1. Selecteer het kenmerk of de transformatie die u op het kenmerk wilt toepassen. Optioneel u de indeling opgeven die de NaamID-claim moet hebben.

   ![De waarde NameID (naam-id) bewerken](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID-indeling

Als de SAML-aanvraag het element NameIDPolicy met een specifieke indeling bevat, eert Azure AD de indeling in de aanvraag.

Als de SAML-aanvraag geen element voor NameIDPolicy bevat, geeft Azure AD de nameid uit met de indeling die u opgeeft. Als er geen indeling is opgegeven, gebruikt Azure AD de standaardbronindeling die is gekoppeld aan de geselecteerde claimbron.

In de vervolgkeuzelijst **Naam-id-indeling kiezen** u een van de volgende opties selecteren.

| NameID-indeling | Beschrijving |
|---------------|-------------|
| **Standaard** | Azure AD gebruikt de standaardbronindeling. |
| **Permanent** | Azure AD gebruikt Persistent als naam-ID-indeling. |
| **Emailaddress** | Azure AD gebruikt EmailAddress als naam-id-indeling. |
| **Niet opgegeven** | Azure AD gebruikt Niet-gespecificeerd als naam-ID-indeling. |
| **Windows-domeingekwalificeerde naam** | Azure AD gebruikt WindowsDomainQualifiedName als nameid-indeling. |

Tijdelijke nameID wordt ook ondersteund, maar is niet beschikbaar in de vervolgkeuzelijst en kan niet worden geconfigureerd aan azure's kant. Zie [Saml-protocol voor één aanmelding](single-sign-on-saml-protocol.md)voor meer informatie over het kenmerk NameIDPolicy .

### <a name="attributes"></a>Kenmerken

Selecteer de gewenste `NameIdentifier` bron voor de claim (of NameID). U kiezen uit de volgende opties.

| Name | Beschrijving |
|------|-------------|
| Email | Het e-mailadres van de gebruiker |
| userprincipalName | Gebruikersnaam (UPN) van de gebruiker |
| onpremisessamaccount | SAM-accountnaam die is gesynchroniseerd vanaf on-premises Azure AD |
| objectid | Objectid van de gebruiker in Azure AD |
| employeeid | Werknemers-ID van de gebruiker |
| Uitbreidingen van de directory | Directory-extensies [die zijn gesynchroniseerd vanuit on-premises Active Directory met Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Extensiekenmerken 1-15 | On-premises extensiekenmerken die worden gebruikt om het Azure AD-schema uit te breiden |

Zie [Tabel 3: Geldige ID-waarden per bron voor](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)meer informatie.

U ook een constante (statische) waarde toewijzen aan claims die u definieert in Azure AD. Volg de onderstaande stappen om een constante waarde toe te wijzen:

1. Klik in de [Azure-portal](https://portal.azure.com/)in de sectie **Gebruikerskenmerken & claims** op het pictogram **Bewerken** om de claims te bewerken.

1. Klik op de vereiste claim die u wilt wijzigen.

1. Voer de constante waarde zonder aanhalingstekens in het **kenmerk Bron** in per uw organisatie in en klik op **Opslaan**.

    ![De sectie Gebruikerskenmerken & claims openen in de Azure-portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. De constante waarde wordt hieronder weergegeven.

    ![De sectie Gebruikerskenmerken & claims openen in de Azure-portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Speciale claims - transformaties

U ook de functies voor claimtransformaties gebruiken.

| Functie | Beschrijving |
|----------|-------------|
| **ExtractMailPrefix()** | Hiermee verwijdert u het domeinachtervoegsel uit het e-mailadres of de hoofdnaam van de gebruiker. Hiermee wordt alleen het eerste deel van de gebruikersnaam dat wordt doorgegeven (bijvoorbeeld "joe_smith" in plaats van joe_smith@contoso.com). |
| **Word lid()** | Hiermee wordt een kenmerk verbonden met een geverifieerd domein. Als de geselecteerde waarde voor gebruikers-id's een domein heeft, wordt de gebruikersnaam geëxtraheerd om het geselecteerde geverifieerde domein toe te toevoegen. Als u bijvoorbeeld de e-mail (joe_smith@contoso.com) selecteert als de waarde van de joe_smith@contoso.onmicrosoft.comgebruikers-id en contoso.onmicrosoft.com selecteert als het geverifieerde domein, resulteert dit in . |
| **ToLower()** | Hiermee worden de tekens van het geselecteerde kenmerk omgezet in kleine letters. |
| **ToUpper()** | Hiermee worden de tekens van het geselecteerde kenmerk omgezet in hoofdletters. |

## <a name="adding-application-specific-claims"></a>Toepassingsspecifieke claims toevoegen

Ga als het gaat om toepassingsspecifieke claims toe te voegen:

1. Selecteer **in Gebruikerskenmerken & Claims**de optie Nieuwe claim **toevoegen** om de pagina Gebruikersclaims beheren te **openen.**
1. Voer de **naam** van de claims in. De waarde hoeft niet strikt een URI-patroon te volgen, volgens de SAML-spec. Als u een URI-patroon nodig hebt, u dat in het veld **Namespace** plaatsen.
1. Selecteer de **bron** waar de claim de waarde ervan gaat ophalen. U een gebruikerskenmerk selecteren in de vervolgkeuzelijst van het bronkenmerk of een transformatie toepassen op het gebruikerskenmerk voordat u het als claim uitzendt.

### <a name="claim-transformations"></a>Transformaties van claimen

Een transformatie toepassen op een gebruikerskenmerk:

1. Selecteer in **Claim beheren**de optie *Transformatie* als claimbron om de pagina **Transformatie beheren** te openen.
2. Selecteer de functie in de vervolgkeuzelijst transformatie. Afhankelijk van de geselecteerde functie moet u parameters en een constante waarde opgeven om in de transformatie te evalueren. Raadpleeg de onderstaande tabel voor meer informatie over de beschikbare functies.
3. Als u meerdere transformaties wilt toepassen, klikt u op **Transformatie toevoegen**. U maximaal twee transformaties toepassen op een claim. U bijvoorbeeld eerst het e-mailvoorvoegsel van het `user.mail`. Maak vervolgens de bovenste snaar.

   ![De waarde NameID (naam-id) bewerken](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

U de volgende functies gebruiken om claims te transformeren.

| Functie | Beschrijving |
|----------|-------------|
| **ExtractMailPrefix()** | Hiermee verwijdert u het domeinachtervoegsel uit het e-mailadres of de hoofdnaam van de gebruiker. Hiermee wordt alleen het eerste deel van de gebruikersnaam dat wordt doorgegeven (bijvoorbeeld "joe_smith" in plaats van joe_smith@contoso.com). |
| **Word lid()** | Hiermee maakt u een nieuwe waarde door twee kenmerken toe te voegen. Optioneel u een scheidingsteken tussen de twee kenmerken gebruiken. Voor nameID-claimtransformatie is de join beperkt tot een geverifieerd domein. Als de geselecteerde waarde voor gebruikers-id's een domein heeft, wordt de gebruikersnaam geëxtraheerd om het geselecteerde geverifieerde domein toe te toevoegen. Als u bijvoorbeeld de e-mail (joe_smith@contoso.com) selecteert als de waarde van de joe_smith@contoso.onmicrosoft.comgebruikers-id en contoso.onmicrosoft.com selecteert als het geverifieerde domein, resulteert dit in . |
| **ToLower()** | Hiermee worden de tekens van het geselecteerde kenmerk omgezet in kleine letters. |
| **ToUpper()** | Hiermee worden de tekens van het geselecteerde kenmerk omgezet in hoofdletters. |
| **Contains()** | Maakt een kenmerk of een constante uit als de invoer overeenkomt met de opgegeven waarde. Anders u een andere uitvoer opgeven als er geen overeenkomst is.<br/>Als u bijvoorbeeld een claim wilt uitzenden waarbij de waarde het e-mailadres@contoso.comvan de gebruiker is als het domein " bevat", anders wilt u de hoofdnaam van de gebruiker uitvoeren. Om dit te doen, configureert u de volgende waarden:<br/>*Parameter 1(invoer)*: user.email<br/>*Waarde*:@contoso.com" "<br/>Parameter 2 (uitvoer): user.email<br/>Parameter 3 (uitvoer als er geen overeenkomst is): user.userprincipalname |
| **Einde met()** | Hiermee wordt een kenmerk of een constante uitgevoerd als de invoer eindigt met de opgegeven waarde. Anders u een andere uitvoer opgeven als er geen overeenkomst is.<br/>Als u bijvoorbeeld een claim wilt uitzenden waarbij de waarde de werknemers-id van de gebruiker is als de werknemers-id eindigt met '000', wilt u een extensiekenmerk uitvoeren. Om dit te doen, configureert u de volgende waarden:<br/>*Parameter 1(invoer)*: user.employeeid<br/>*Waarde*: "000"<br/>Parameter 2 (uitvoer): user.employeeid<br/>Parameter 3 (uitvoer als er geen overeenkomst is): user.extensionattribute1 |
| **Beginmet()** | Hiermee wordt een kenmerk of een constante uitgevoerd als de invoer begint met de opgegeven waarde. Anders u een andere uitvoer opgeven als er geen overeenkomst is.<br/>Als u bijvoorbeeld een claim wilt uitzenden waarbij de waarde de werknemers-id van de gebruiker is als het land/de regio begint met 'VS', wilt u een extensiekenmerk uitvoeren. Om dit te doen, configureert u de volgende waarden:<br/>*Parameter 1(invoer)*: user.country<br/>*Waarde*: "US"<br/>Parameter 2 (uitvoer): user.employeeid<br/>Parameter 3 (uitvoer als er geen overeenkomst is): user.extensionattribute1 |
| **Extract() - Na matching** | Geeft als resultaat de subtekenreeks nadat deze overeenkomt met de opgegeven waarde.<br/>Als de waarde van de invoer bijvoorbeeld 'Finance_BSimon' is, is de overeenkomende waarde 'Finance_', dan is de uitvoer van de claim 'BSimon'. |
| **Extract() - Voor matching** | Geeft als resultaat de subtekenreeks totdat deze overeenkomt met de opgegeven waarde.<br/>Als de waarde van de invoer bijvoorbeeld 'BSimon_US' is, is de overeenkomende waarde '_US', dan is de uitvoer van de claim 'BSimon'. |
| **Extract() - Tussen matching** | Geeft als resultaat de subtekenreeks totdat deze overeenkomt met de opgegeven waarde.<br/>Als de waarde van de invoer bijvoorbeeld 'Finance_BSimon_US' is, is de eerste overeenkomende waarde 'Finance_', de tweede matchingswaarde '_US', dan is de uitvoer van de claim 'BSimon'. |
| **ExtractAlpha() - Voorvoegsel** | Geeft als resultaat het alfabetische voorvoegsel van de tekenreeks.<br/>Als de waarde van de invoer bijvoorbeeld 'BSimon_123' is, wordt 'BSimon' geretourneerd. |
| **ExtractAlpha() - Achtervoegsel** | Geeft als resultaat het alfabetische achtervoegsel van de tekenreeks.<br/>Als de waarde van de invoer bijvoorbeeld '123_Simon' is, wordt 'Simon' geretourneerd. |
| **ExtractNumeric() - Voorvoegsel** | Geeft als resultaat het numerieke gedeelte van de tekenreeks.<br/>Als de waarde van de invoer bijvoorbeeld '123_BSimon' is, wordt '123' geretourneerd. |
| **ExtractNumeric() - Achtervoegsel** | Geeft als resultaat het numerieke deel van de tekenreeks.<br/>Als de waarde van de invoer bijvoorbeeld 'BSimon_123' is, wordt '123' geretourneerd. |
| **IfEmpty()** | Maakt een kenmerk of een constante als de invoer nietig of leeg is.<br/>Als u bijvoorbeeld een kenmerk wilt uitvoeren dat is opgeslagen in een extensiekenmerk als de werknemers-id voor een bepaalde gebruiker leeg is. Om dit te doen, configureert u de volgende waarden:<br/>Parameter 1(invoer): user.employeeid<br/>Parameter 2 (uitvoer): user.extensionattribute1<br/>Parameter 3 (uitvoer als er geen overeenkomst is): user.employeeid |
| **IfNotEmpty()** | Maakt een kenmerk of een constante uit als de invoer niet null of leeg is.<br/>Als u bijvoorbeeld een kenmerk wilt uitvoeren dat is opgeslagen in een extensiekenmerk als de werknemers-id voor een bepaalde gebruiker niet leeg is. Om dit te doen, configureert u de volgende waarden:<br/>Parameter 1(invoer): user.employeeid<br/>Parameter 2 (uitvoer): user.extensionattribute1 |

Als u extra transformaties nodig hebt, dient u uw idee in op het [feedbackforum in Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) onder de categorie *SaaS-toepassingen.*

## <a name="emitting-claims-based-on-conditions"></a>Claims uitzenden op basis van voorwaarden

U de bron van een claim opgeven op basis van het gebruikerstype en de groep waartoe de gebruiker behoort. 

Het gebruikerstype kan zijn:
- **Alle**gebruikers hebben toegang tot de toepassing.
- **Leden**: Native lid van de huurder
- **Alle gasten**: De gebruiker wordt overgehaald van een externe organisatie met of zonder Azure AD.
- **AAD-gasten**: Gastgebruiker behoort tot een andere organisatie die Azure AD gebruikt.
- **Externe gasten**: gastgebruiker behoort tot een externe organisatie die geen Azure AD heeft.


Een scenario waarin dit nuttig is, is wanneer de bron van een claim anders is voor een gast en een werknemer die toegang heeft tot een toepassing. U opgeven dat als de gebruiker een werknemer is, de NameID afkomstig is van user.email, maar als de gebruiker een gast is, wordt de NameID afkomstig van user.extensionattribute1.

Ga als lid van het nieuwe bedrijf over:

1. Vouw in **Claim beheren**de claimvoorwaarden uit.
2. Selecteer het gebruikerstype.
3. Selecteer de groep(en) waartoe de gebruiker behoort. U maximaal 10 unieke groepen selecteren in alle claims voor een bepaalde toepassing. 
4. Selecteer de **bron** waar de claim de waarde ervan gaat ophalen. U een gebruikerskenmerk selecteren in de vervolgkeuzelijst van het bronkenmerk of een transformatie toepassen op het gebruikerskenmerk voordat u het als claim uitzendt.

De volgorde waarin u de voorwaarden toevoegt zijn belangrijk. Azure AD evalueert de voorwaarden van boven naar beneden om te beslissen welke waarde in de claim wordt uitstraalt. 

Brita Simon is bijvoorbeeld gastgebruiker in de Contoso-tenant. Ze behoort tot een andere organisatie die ook Azure AD gebruikt. Gezien de onderstaande configuratie voor de Fabrikam-toepassing, zal Azure AD de voorwaarden als volgt evalueren wanneer Brita zich probeert aan te melden bij Fabrikam.

Azure AD controleert eerst of het gebruikerstype `All guests`van Brita . Aangezien dit waar is, wijst Azure AD de `user.extensionattribute1`bron voor de claim toe aan . Ten tweede controleert Azure AD of het `AAD guests`gebruikerstype van Brita dat is, omdat dit `user.mail`ook waar is, dan wijst Azure AD de bron voor de claim toe aan . Ten slotte wordt de claim `user.email` met waarde voor Brita uitgezonden.

![Voorwaardelijke configuratie van claims](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Volgende stappen

* [Toepassingsbeheer in Azure AD](../manage-apps/what-is-application-management.md)
* [Eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure AD-toepassingsgalerie bevinden](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Problemen oplossen met saml-gebaseerde enkele aanmelding](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
