---
title: Een niet-galerie toepassing toevoegen-micro soft Identity-platform | Microsoft Docs
description: Voeg een niet-galerie toepassing toe aan uw Azure AD-Tenant.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063608"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Een niet-gevermeldde (niet-Gallery) toepassing toevoegen aan uw Azure AD-organisatie

Naast de opties in de [Azure AD-toepassings galerie](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/), hebt u de mogelijkheid om een **niet-galerie toepassing**toe te voegen. U kunt elke toepassing toevoegen die al bestaat in uw organisatie of een toepassing van derden van een leverancier die nog geen deel uitmaakt van de Azure AD-galerie. Afhankelijk van uw [gebruiksrecht overeenkomst](https://azure.microsoft.com/pricing/details/active-directory/)zijn de volgende mogelijkheden beschikbaar:

- Selfservice-integratie van elke toepassing die ondersteuning biedt voor services van [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) (door SP geïnitieerd of IDP)
- Self-Service-integratie van elke webtoepassing met een op een HTML gebaseerde aanmeldings pagina met [SSO op basis van wacht woorden](what-is-single-sign-on.md#password-based-sso)
- Selfservice verbinding van toepassingen die gebruikmaken van het [systeem voor het scim-Protocol (Cross-Domain Identity Management) voor het inrichten van gebruikers](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Mogelijkheid om koppelingen toe te voegen aan een toepassing in het [Start programma voor Office 365-apps](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) of het [Azure AD-toegangs venster](what-is-single-sign-on.md#linked-sign-on)

In dit artikel wordt beschreven hoe u een niet-galerie toepassing kunt toevoegen aan **bedrijfs toepassingen** in het Azure Portal zonder code te schrijven. Zie [verificatie scenario's voor Azure AD](../develop/authentication-scenarios.md)als u in plaats daarvan de richt lijnen voor ontwikkel aars zoekt over het integreren van aangepaste apps met Azure AD. Wanneer u een App ontwikkelt die gebruikmaakt van een modern protocol zoals [OpenID Connect Connect/OAuth](../develop/active-directory-v2-protocols.md) om gebruikers te verifiëren, kunt u dit registreren bij het micro soft Identity-platform met behulp van de [app-registraties](../develop/quickstart-register-app.md) -ervaring in de Azure Portal.

## <a name="add-a-non-gallery-application"></a>Een niet-galerie toepassing toevoegen

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com/) met behulp van uw micro soft Identity platform Administrator-account.

2. Selecteer **bedrijfs toepassingen** > **nieuwe toepassing**.

3. (Optioneel, maar aanbevolen) Voer in het zoekvak **Bladeren in azure AD galerie** de weergave naam van de toepassing in. 

4. Selecteer **uw eigen toepassing maken**. De pagina **uw eigen toepassing maken** wordt weer gegeven.

   ![Toepassing toevoegen](media/add-non-gallery-app/create-your-own-application.png)

5. Begin met het typen van de weergave naam voor de nieuwe toepassing. Als er galerie toepassingen met vergelijk bare namen zijn, worden ze weer gegeven in een lijst met zoek resultaten.

   > [!NOTE]
   > U wordt aangeraden de galerie versie van uw toepassing waar mogelijk te gebruiken. Als de toepassing die u wilt toevoegen, wordt weer gegeven in de zoek resultaten, selecteert u de toepassing en slaat u de rest van deze procedure over.

6. Onder **Wat wilt u doen met uw toepassing?** Kies **een andere toepassing integreren die u niet in de galerie vindt**. Deze optie wordt doorgaans gebruikt voor SAML-en WS-inschakel toepassingen.

   > [!NOTE]
   > De andere twee opties worden gebruikt in de volgende scenario's:
   >* **Toepassings proxy configureren voor beveiligde externe toegang tot een on-premises toepassing** Hiermee opent u de pagina configuratie voor Azure AD-toepassingsproxy en connectors.
   >* **Registreer een toepassing waaraan u werkt om te integreren met Azure AD** de **app-registraties** -pagina wordt geopend. Deze optie wordt doorgaans gebruikt voor OpenID Connect Connect-toepassingen.

7. Selecteer **Maken**. De **overzichts** pagina van de toepassing wordt geopend.

## <a name="configure-user-sign-in-properties"></a>Eigenschappen voor gebruikersaanmelding configureren

1. Selecteer **Eigenschappen** om het deel venster Eigenschappen te openen om het te bewerken.

    ![Eigenschappen venster bewerken](media/add-non-gallery-app/edit-properties.png)

2. Stel de volgende opties in om te bepalen hoe gebruikers die zijn toegewezen aan of niet zijn toegewezen aan de toepassing, zich kunnen aanmelden bij de toepassing en of een gebruiker de toepassing kan zien in het toegangs venster.

    - Met **Ingeschakeld voor gebruikers voor aanmelden** wordt bepaald of gebruikers die zijn toegewezen aan de toepassing, zich kunnen aanmelden.
    - **Gebruikers toewijzing vereist** bepaalt of gebruikers die niet zijn toegewezen aan de toepassing zich kunnen aanmelden.
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

3. Als u een aangepast logo wilt gebruiken, maakt u een logo van 215 x 215 pixels en slaat u het op in PNG-indeling. Blader vervolgens naar uw logo en upload het.

    ![Het logo wijzigen](media/add-non-gallery-app/change-logo.png)

4. Wanneer u klaar bent, selecteert u **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

Nu u de toepassing aan uw Azure AD-organisatie hebt toegevoegd, [kiest u de methode voor eenmalige aanmelding](what-is-single-sign-on.md#choosing-a-single-sign-on-method) die u wilt gebruiken en raadpleegt u het juiste artikel hieronder:

- [Eenmalige aanmelding op basis van SAML configureren](configure-single-sign-on-non-gallery-applications.md)
- [Eenmalige aanmelding met een wacht woord configureren](configure-password-single-sign-on-non-gallery-applications.md)
- [Gekoppelde aanmelding configureren](configure-linked-sign-on.md)
