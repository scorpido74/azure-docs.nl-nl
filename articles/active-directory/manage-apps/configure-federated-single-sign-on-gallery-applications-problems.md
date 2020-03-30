---
title: Problemen met het configureren van federatieve SSO voor Azure AD Gallery-apps
description: Een aantal van de veelvoorkomende problemen aanpakken die u ondervinden bij het configureren van federatieve enkele aanmelding met SAML voor toepassingen die worden vermeld in de Azure AD Application Gallery
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87c2497a781b0d46b3b2f1e281a3d7b327b60952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274645"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Probleem met het configureren van federatieve enkele aanmelding voor een Azure AD Gallery-toepassing

Als u een probleem ondervindt bij het configureren van een toepassing. Controleer of u alle stappen in de zelfstudie voor de toepassing hebt gevolgd. In de configuratie van de toepassing beschikt u over inlinedocumentatie over het configureren van de toepassing. U hebt ook toegang tot de [lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) voor een gedetailleerde stapsgewijze richtlijnen.

## <a name="cant-add-another-instance-of-the-application"></a>Kan geen andere instantie van de toepassing toevoegen

Als u een tweede instantie van een toepassing wilt toevoegen, moet u het:

-   Een unieke id configureren voor de tweede instantie. U niet dezelfde id configureren die voor de eerste instantie wordt gebruikt.

-   Configureer een ander certificaat dan het certificaat dat in eerste instantie wordt gebruikt.

Als de toepassing geen van de bovenstaande ondersteunt. Vervolgens u geen tweede instantie configureren.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Kan de id of de url van het antwoord niet toevoegen

Als u de id of de URL van het antwoord niet configureren, bevestigt u de URL-waarden Id en Antwoord overeenkomen met de patronen die vooraf zijn geconfigureerd voor de toepassing.

Ga als u de vooraf geconfigureerde patronen voor de toepassing kennen:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.** Ga naar stap 7. Als u zich al in het configuratieblad van de toepassing op Azure AD bevindt.

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u wilt configureren als u zich wilt aanmelden.

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Selecteer **OP SAML gebaseerde aanmelding** in de vervolgkeuzelijst **Modus.**

9. Ga naar het tekstvak **Id-** of **Url-antwoord** onder de **sectie Domein en URL's.**

10. Er zijn drie manieren om de ondersteunde patronen voor de toepassing te kennen:

    * In het tekstvak ziet u het ondersteunde patroon(en) als *een aanduidingvoorbeeld:* <https://contoso.com>.

    * Als het patroon niet wordt ondersteund, ziet u een rood uitroepteken wanneer u de waarde in het tekstvak probeert in te voeren. Als u met uw muis over het rode uitroepteken zweeft, ziet u de ondersteunde patronen.

    * In de zelfstudie voor de toepassing u ook informatie krijgen over de ondersteunde patronen. Onder de sectie **Azure AD-aanmelding configureren.** Ga naar de stap voor het configureren van de waarden onder de sectie **Domein en URL's.**

Als de waarden niet overeenkomen met de patronen die vooraf zijn geconfigureerd in Azure AD. U kunt:

-   Samenwerken met de toepassingsleverancier om waarden te krijgen die overeenkomen met het patroon dat vooraf is geconfigureerd op Azure AD

-   U ook contact opnemen <aadapprequest@microsoft.com> met het Azure AD-team op of een opmerking achterlaten in de zelfstudie om de update van de ondersteunde patronen voor de toepassing te vragen

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Waar stel ik de entiteits-id-indeling in

U de entiteits-id-indeling (User Identifier) die Azure AD naar de toepassing verzendt in het antwoord na gebruikersverificatie niet selecteren.

Azure AD selecteert de indeling voor het kenmerk NameID (User Identifier) op basis van de geselecteerde waarde of de indeling die door de toepassing is aangevraagd in de SAML AuthRequest. Ga voor meer informatie naar het artikel [Single Sign-On SAML protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) onder de sectie NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>De Azure AD-metagegevens kunnen niet worden gevonden om de configuratie met de toepassing te voltooien

Voer de volgende stappen uit om de metagegevens of het certificaat van de toepassing te downloaden van Azure AD:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u hebt geconfigureerd voor eenmalige aanmelding.

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Ga naar de sectie **SAML-ondertekeningscertificaat** en klik op **Kolomwaarde downloaden.** Afhankelijk van wat de toepassing vereist voor het configureren van één aanmelding, ziet u de optie om de XML met ametjes of het certificaat te downloaden.

Azure AD geeft geen URL om de metagegevens op te halen. De metagegevens kunnen alleen worden opgehaald als een XML-bestand.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Weet niet hoe saml-claims die naar een toepassing worden verzonden, aan te passen

Zie Claimtoewijzing in Azure Active Directory voor meer informatie voor meer informatie voor het aanpassen van de SAML-attribuutclaims die naar uw toepassing zijn [verzonden.](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

## <a name="next-steps"></a>Volgende stappen
[Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
