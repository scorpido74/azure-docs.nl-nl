---
title: Problemen met het configureren van federatieve SSO voor Azure AD Gallery-apps
description: Adresseer een aantal veelvoorkomende problemen die kunnen optreden bij het configureren van federatieve eenmalige aanmelding met behulp van SAML voor toepassingen die worden vermeld in de Azure AD-toepassings galerie
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274645"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Probleem bij het configureren van federatieve eenmalige aanmelding voor een toepassing in de Azure AD-galerie

Als er een probleem optreedt bij het configureren van een toepassing. Controleer of u alle stappen in de zelf studie voor de toepassing hebt gevolgd. In de configuratie van de toepassing hebt u inline-documentatie over het configureren van de toepassing. U kunt ook toegang krijgen tot de [lijst met zelf studies over het integreren van SaaS-apps met Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) voor een gedetailleerde stapsgewijze uitleg.

## <a name="cant-add-another-instance-of-the-application"></a>Kan geen ander exemplaar van de toepassing toevoegen

Als u een tweede exemplaar van een toepassing wilt toevoegen, moet u het volgende kunnen doen:

-   Configureer een unieke id voor het tweede exemplaar. U kunt niet dezelfde id configureren die wordt gebruikt voor het eerste exemplaar.

-   Configureer een ander certificaat dan dat voor het eerste exemplaar.

Als de toepassing geen van de bovenstaande voor keuren ondersteunt. Daarna kunt u een tweede exemplaar niet configureren.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>De id of de antwoord-URL kan niet worden toegevoegd

Als u de id of de antwoord-URL niet kunt configureren, bevestigt u dat de id-en antwoord-URL-waarden overeenkomen met de patronen die vooraf zijn geconfigureerd voor de toepassing.

Als u wilt weten welke patronen vooraf zijn geconfigureerd voor de toepassing:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.** Gaat u naar stap 7. Als u zich al in de Blade toepassings configuratie op Azure AD bevindt.

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer **op SAML gebaseerde aanmelding** in de **modus** vervolg keuzelijst.

9. Ga naar het tekstvak **id** of **antwoord-URL** , onder de **sectie domein en url's.**

10. Er zijn drie manieren om de ondersteunde patronen voor de toepassing te kennen:

    * In het tekstvak ziet u de ondersteunde patroon (en) als tijdelijke aanduiding *voor beeld:* <https://contoso.com>.

    * Als het patroon niet wordt ondersteund, ziet u een rood uitroep teken wanneer u probeert de waarde in het tekstvak in te voeren. Als u de muis aanwijzer boven het rode uitroep teken houdt, ziet u de ondersteunde patronen.

    * In de zelf studie voor de toepassing kunt u ook informatie over de ondersteunde patronen ophalen. Onder de sectie **eenmalige aanmelding voor Azure AD configureren** . Ga naar de stap voor het configureren van de waarden in de sectie **domein en url's** .

Als de waarden niet overeenkomen met de patronen die vooraf zijn geconfigureerd in azure AD. U kunt:

-   Werk samen met de leverancier van de toepassing om waarden op te halen die overeenkomen met het patroon dat vooraf is geconfigureerd in azure AD

-   U kunt ook contact opnemen met het Azure AD-team op <aadapprequest@microsoft.com> of een opmerking in de zelf studie laten staan om de update van de ondersteunde patronen voor de toepassing aan te vragen

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Waar kan ik de EntityID-indeling (gebruikers-id) instellen

U kunt de EntityID-indeling (gebruikers-id) niet selecteren die door Azure AD wordt verzonden naar de toepassing in het antwoord na de verificatie van de gebruiker.

Azure AD Selecteer de indeling voor het NameID-kenmerk (gebruikers-id) op basis van de geselecteerde waarde of de indeling die door de toepassing is aangevraagd in het SAML-AuthRequest. Ga voor meer informatie naar het artikel [SAML-protocol voor eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) in de sectie NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>De Azure AD-meta gegevens zijn niet gevonden voor het volt ooien van de configuratie met de toepassing

Voer de volgende stappen uit om de meta gegevens of het certificaat van de toepassing te downloaden uit Azure AD:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding hebt geconfigureerd.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Ga naar de sectie **SAML-handtekening certificaat** en klik vervolgens op kolom waarde **downloaden** . Afhankelijk van wat de toepassing nodig heeft om eenmalige aanmelding te configureren, ziet u de optie voor het downloaden van de meta gegevens-XML of het certificaat.

Azure AD biedt geen URL om de meta gegevens op te halen. De meta gegevens kunnen alleen worden opgehaald als een XML-bestand.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Weet u niet hoe u de SAML-claims kunt aanpassen die naar een toepassing worden verzonden

Zie [claim toewijzing in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) voor meer informatie over het aanpassen van de SAML-kenmerk claims die naar uw toepassing worden verzonden.

## <a name="next-steps"></a>Volgende stappen
[Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
