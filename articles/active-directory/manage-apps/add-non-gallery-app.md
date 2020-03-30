---
title: Een niet-galerietoepassing toevoegen - Microsoft-identiteitsplatform | Microsoft Documenten
description: Voeg een niet-galerietoepassing toe aan uw Azure AD-tenant.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5a5f100dbe09c3b82f58183a118ee3bf455f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063608"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Een niet-vermelde (niet-galerie)toepassing toevoegen aan uw Azure AD-organisatie

Naast de opties in de [Azure AD-toepassingsgalerie](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)hebt u de mogelijkheid om een **niet-galerietoepassing**toe te voegen. U een toepassing toevoegen die al in uw organisatie bestaat, of een toepassing van derden van een leverancier die nog geen deel uitmaakt van de Azure AD-galerie. Afhankelijk van uw [licentieovereenkomst](https://azure.microsoft.com/pricing/details/active-directory/)zijn de volgende mogelijkheden beschikbaar:

- Selfservice-integratie van elke toepassing die [beveiligingsbeweringmarkeringstaal (SAML) 2.0-identiteitsproviders](https://wikipedia.org/wiki/SAML_2.0) ondersteunt (SP-geïnitieerd of idp-geïnitieerd)
- Selfservice-integratie van elke webtoepassing met een HTML-gebaseerde aanmeldingspagina met behulp van [op een wachtwoord gebaseerde SSO](what-is-single-sign-on.md#password-based-sso)
- Selfservice-verbinding van toepassingen die het [SCIM-protocol (System for Cross-Domain Identity Management)](../app-provisioning/use-scim-to-provision-users-and-groups.md) gebruiken voor gebruikersinrichting
- Mogelijkheid om koppelingen toe te voegen aan een toepassing in het [startprogramma voor Office 365-apps](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) of het [Azure AD-toegangspaneel](what-is-single-sign-on.md#linked-sign-on)

In dit artikel wordt beschreven hoe u een niet-galerietoepassing toevoegt aan **Enterprise-toepassingen** in de Azure-portal zonder code te schrijven. Zie [Verificatiescenario's voor Azure AD](../develop/authentication-scenarios.md)als u in plaats daarvan richtlijnen voor ontwikkelaars zoekt voor het integreren van aangepaste apps met Azure AD. Wanneer u een app ontwikkelt die een modern protocol zoals [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) gebruikt om gebruikers te verifiëren, u deze registreren bij het Microsoft-identiteitsplatform met behulp van de ervaring [met app-registraties](../develop/quickstart-register-app.md) in de Azure-portal.

## <a name="add-a-non-gallery-application"></a>Een niet-galerietoepassing toevoegen

1. Meld u aan bij de [Azure Active Directory-portal](https://aad.portal.azure.com/) met uw Microsoft-account voor identiteitsplatformbeheerders.

2. Selecteer**Nieuwe toepassing** **voor Ondernemingstoepassingen** > .

3. (Optioneel maar aanbevolen) Voer in het zoekvak **Bladeren in Azure AD Gallery** de weergavenaam van de toepassing in. 

4. Selecteer **Uw eigen toepassing maken**. De **pagina Uw eigen toepassing maken** wordt weergegeven.

   ![Toepassing toevoegen](media/add-non-gallery-app/create-your-own-application.png)

5. Begin met het typen van de weergavenaam voor uw nieuwe toepassing. Als er galerijtoepassingen met vergelijkbare namen zijn, worden deze weergegeven in een lijst met zoekresultaten.

   > [!NOTE]
   > We raden u aan de galerijversie van uw toepassing waar mogelijk te gebruiken. Als de toepassing die u wilt toevoegen in de zoekresultaten wordt weergegeven, selecteert u de toepassing en slaat u de rest van deze procedure over.

6. Onder **Wat wilt u doen met uw toepassing?** kies Een andere toepassing integreren die u niet in de galerij **vindt.** Deze optie wordt meestal gebruikt voor SAML- en WS-Fed-toepassingen.

   > [!NOTE]
   > De andere twee opties worden gebruikt in de volgende scenario's:
   >* **Als u toepassingsproxy configureert voor veilige externe toegang tot een on-premises toepassing,** wordt de configuratiepagina voor Azure AD-toepassingsproxy en -connectoren geopend.
   >* **Als u een toepassing registreert waaraan u werkt om te integreren met Azure AD,** wordt de pagina **App-registraties** geopend. Deze optie wordt meestal gebruikt voor OpenID Connect-toepassingen.

7. Selecteer **Maken**. De pagina **Toepassingsoverzicht** wordt geopend.

## <a name="configure-user-sign-in-properties"></a>Eigenschappen voor gebruikersaanmelding configureren

1. Selecteer **Eigenschappen** om het deelvenster Eigenschappen te openen voor bewerking.

    ![Deelvenster Eigenschappen bewerken](media/add-non-gallery-app/edit-properties.png)

2. Stel de volgende opties in om te bepalen hoe gebruikers die zijn toegewezen of niet aan de toepassing zijn toegewezen, zich kunnen aanmelden bij de toepassing en of een gebruiker de toepassing in het toegangspaneel kan zien.

    - Met **Ingeschakeld voor gebruikers voor aanmelden** wordt bepaald of gebruikers die zijn toegewezen aan de toepassing, zich kunnen aanmelden.
    - **Gebruikerstoewijzing vereist** bepaalt of gebruikers die niet zijn toegewezen aan de toepassing zich kunnen aanmelden.
    - Met **Zichtbaar voor gebruiker** wordt bepaald of gebruikers die zijn toegewezen aan een app, deze kunnen zien in het toegangsvenster en het startprogramma voor O365.

      Gedrag voor **toegewezen** gebruikers:

       | Instellingen voor toepassingseigenschappen | | | Toegewezen gebruikerservaring | |
       |---|---|---|---|---|
       | Ingeschakeld voor gebruikers voor aanmelden? | Gebruikerstoewijzing vereist? | Zichtbaar voor gebruiker? | Kunnen toegewezen gebruikers zich aanmelden? | Kunnen toegewezen gebruikers de toepassing zien?* |
       | ja | ja | ja | ja | ja  |
       | ja | ja | nee  | ja | nee   |
       | ja | nee  | ja | ja | ja  |
       | ja | nee  | nee  | ja | nee   |
       | nee  | ja | ja | nee  | nee   |
       | nee  | ja | nee  | nee  | nee   |
       | nee  | nee  | ja | nee  | nee   |
       | nee  | nee  | nee  | nee  | nee   |

      Gedrag voor **niet-toegewezen** gebruikers:

       | Instellingen voor toepassingseigenschappen | | | Niet-toegewezen gebruikerservaring | |
       |---|---|---|---|---|
       | Ingeschakeld voor gebruikers om zich aan te melden? | Gebruikerstoewijzing vereist? | Zichtbaar voor gebruiker? | Kunnen niet-toegewezen gebruikers zich aanmelden? | Kunnen niet-toegewezen gebruikers de toepassing zien?* |
       | ja | ja | ja | nee  | nee   |
       | ja | ja | nee  | nee  | nee   |
       | ja | nee  | ja | ja | nee   |
       | ja | nee  | nee  | ja | nee   |
       | nee  | ja | ja | nee  | nee   |
       | nee  | ja | nee  | nee  | nee   |
       | nee  | nee  | ja | nee  | nee   |
       | nee  | nee  | nee  | nee  | nee   |

     *Kan de gebruiker de toepassing zien in het toegangsvenster en het startprogramma voor Office 365-apps?

3. Als u een aangepast logo wilt gebruiken, maakt u een logo van 215 bij 215 pixels en slaat u het op in PNG-indeling. Blader vervolgens naar uw logo en upload het.

    ![Het logo wijzigen](media/add-non-gallery-app/change-logo.png)

4. Wanneer u klaar bent, selecteert u **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

Nu u de toepassing aan uw Azure AD-organisatie hebt toegevoegd, kiest u [één aanmeldingsmethode](what-is-single-sign-on.md#choosing-a-single-sign-on-method) die u wilt gebruiken en verwijst u naar het juiste artikel hieronder:

- [Eenmalige aanmelding op basis van SAML configureren](configure-single-sign-on-non-gallery-applications.md)
- [Eenmalige aanmelding voor wachtwoorden configureren](configure-password-single-sign-on-non-gallery-applications.md)
- [Gekoppelde aanmelding configureren](configure-linked-sign-on.md)
