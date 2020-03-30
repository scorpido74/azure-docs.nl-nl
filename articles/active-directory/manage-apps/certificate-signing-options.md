---
title: Geavanceerde SAML-tokencertificaatondertekeningsopties voor Azure AD-apps
description: Meer informatie over het gebruik van geavanceerde opties voor het ondertekenen van certificaten in het SAML-token voor vooraf geïntegreerde apps in Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc911ff06208b1fd0af7651c8274a45c958bf0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159196"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Geavanceerde opties voor certificaatondertekening in het SAML-token voor galerie-apps in Azure Active Directory

Tegenwoordig ondersteunt Azure Active Directory (Azure AD) duizenden vooraf geïntegreerde toepassingen in de Azure Active Directory App Gallery. Meer dan 500 van de toepassingen ondersteunen eenmalige aanmelding met behulp van het SAML-protocol [(Security Assertion Markup Language)](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) 2.0, zoals de [NetSuite-toepassing.](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) Wanneer een klant zich verifieert naar een toepassing via Azure AD met SAML, stuurt Azure AD een token naar de toepassing (via een HTTP-POST). De toepassing valideert en gebruikt het token vervolgens om de klant aan te melden in plaats van om een gebruikersnaam en wachtwoord te vragen. Deze SAML-tokens zijn ondertekend met het unieke certificaat dat wordt gegenereerd in Azure AD en door specifieke standaardalgoritmen.

Azure AD gebruikt een aantal standaardinstellingen voor de galerietoepassingen. De standaardwaarden worden ingesteld op basis van de vereisten van de toepassing.

In Azure AD u opties voor certificaatondertekening en het algoritme voor het ondertekenen van certificaten instellen.

## <a name="certificate-signing-options"></a>Opties voor certificaatondertekening

Azure AD ondersteunt drie opties voor het ondertekenen van certificaten:

* **Teken SAML bewering**. Deze standaardoptie is ingesteld voor de meeste galerietoepassingen. Als u deze optie selecteert, ondertekent Azure AD als identiteitsprovider (IdP) de SAML-bewering en het certificaat met het [X.509-certificaat](https://wikipedia.org/wiki/X.509) van de toepassing.

* **Teken SAML-antwoord**. Als u deze optie selecteert, ondertekent Azure AD als IdP het SAML-antwoord met het X.509-certificaat van de toepassing.

* **Teken SAML-reactie en -bewering**. Als u deze optie selecteert, ondertekent Azure AD als IdP het volledige SAML-token met het X.509-certificaat van de toepassing.

## <a name="certificate-signing-algorithms"></a>Algoritmes voor certificaatondertekening

Azure AD ondersteunt twee ondertekeningsalgoritmen of beveiligde hash-algoritmen (SHA's) om de SAML-reactie te ondertekenen:

* **SHA-256**. Azure AD gebruikt dit standaardalgoritme om de SAML-respons te ondertekenen. Het is het nieuwste algoritme en is veiliger dan SHA-1. De meeste toepassingen ondersteunen het SHA-256-algoritme. Als een toepassing alleen SHA-1 als ondertekeningsalgoritme ondersteunt, u dit wijzigen. Anders raden we u aan het SHA-256-algoritme te gebruiken voor het ondertekenen van de SAML-respons.

* **SHA-1**. Dit algoritme is ouder en wordt behandeld als minder veilig dan SHA-256. Als een toepassing alleen dit ondertekeningsalgoritme ondersteunt, u deze optie selecteren in de vervolgkeuzelijst **Ondertekeningsalgoritme.** Azure AD ondertekent vervolgens de SAML-reactie met het SHA-1-algoritme.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Opties voor certificaatondertekening en ondertekeningsalgoritme wijzigen

Als u de saml-certificaatondertekeningsopties en het algoritme voor certificaatondertekening van een toepassing wilt wijzigen, selecteert u de betreffende toepassing:

1. Meld u in de [Azure Active Directory-portal](https://aad.portal.azure.com/)aan bij uw account. De pagina **Azure Active Directory-beheercentrum** wordt weergegeven.
1. Selecteer in het linkerdeelvenster **Enterprise-toepassingen**. Er wordt een lijst weergegeven met de bedrijfstoepassingen in uw account.
1. Selecteer een toepassing. Er verschijnt een overzichtspagina voor de toepassing.

   ![Voorbeeld: pagina Toepassingsoverzicht](./media/certificate-signing-options/application-overview-page.png)

Wijzig vervolgens de opties voor certificaatondertekening in het SAML-token voor die toepassing:

1. Selecteer In het linkerdeelvenster van de pagina toepassingsoverzicht de optie **Eén aanmelding**.
1. Als de pagina **Eén aanmelding instellen met SAML - Voorbeeld** wordt weergegeven, gaat u naar stap 5.
1. Als de pagina **Een enkele aanmeldingsmethode** selecteren niet wordt weergegeven, selecteert u **Enkele aanmeldingsmodi wijzigen** om die pagina weer te geven.
1. Selecteer **SAML** op de pagina **Eén aanmeldingsmethode** selecteren indien beschikbaar. (Als **SAML** niet beschikbaar is, ondersteunt de toepassing SAML niet en u de rest van deze procedure en artikel negeren.)
1. Zoek in de pagina **Eén aanmelding instellen met SAML - Voorbeeld** de kop **SAML-ondertekeningscertificaat** en selecteer het pictogram **Bewerken** (een potlood). De pagina **SAML-ondertekeningscertificaat** wordt weergegeven.

   ![Voorbeeld: SAML-certificaatpagina voor ondertekenen](./media/certificate-signing-options/saml-signing-page.png)

1. Kies in de vervolgkeuzelijst **Ondertekeningsoptie** de optie **SamL-antwoord ondertekenen,** **de bewering van Sign SAML**of De reactie en bewering van Sign **SAML**. Beschrijvingen van deze opties worden eerder in dit artikel weergegeven in de [opties voor certificaatondertekening](#certificate-signing-options).
1. Kies in de vervolgkeuzelijst **Ondertekeningsalgoritme** de optie **SHA-1** of **SHA-256**. Beschrijvingen van deze opties worden eerder in dit artikel weergegeven in de sectie [Algoritmen voor certificaatondertekening.](#certificate-signing-algorithms)
1. Als u tevreden bent met uw keuzes, selecteert u **Opslaan** om de nieuwe SAML-certificaatinstellingen voor ondertekenen toe te passen. Selecteer anders de **X** om de wijzigingen te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [Eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory App-galerie bevinden](configure-federated-single-sign-on-non-gallery-applications.md)
* [Problemen oplossen met saml-gebaseerde enkele aanmelding](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
