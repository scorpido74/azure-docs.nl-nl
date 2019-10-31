---
title: Een galerie-app toevoegen-Azure Active Directory | Microsoft Docs
description: Meer informatie over het toevoegen van een app uit de Azure AD-galerie aan uw Azure-bedrijfs toepassingen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0a826c499790c86a20ea7061075a6c3e66b3a4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062600"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Een galerie-app toevoegen aan uw Azure AD-organisatie

Azure Active Directory (Azure AD) bevat een galerie met duizenden vooraf geïntegreerde toepassingen die zijn ingeschakeld met eenmalige aanmelding in de onderneming. In dit artikel worden de algemene stappen beschreven voor het toevoegen van een app uit de galerie aan uw Azure AD-organisatie.

> [!IMPORTANT]
> Controleer eerst op uw app in de [lijst met zelf studies over het integreren van SaaS-apps met Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). U vindt waarschijnlijk stapsgewijze richt lijnen voor het toevoegen en configureren van de galerie-app die u wilt toevoegen.

## <a name="add-a-gallery-application"></a>Een galerie toepassing toevoegen

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan als globale beheerder van uw Azure AD-tenant, als beheerder van cloudtoepassingen of als toepassingsbeheerder.

1. In de [Azure Portal](https://portal.azure.com), in het navigatie venster aan de linkerkant, selecteert u **Azure Active Directory**.

1. Selecteer in het deel venster Azure Active Directory **bedrijfs toepassingen**.

    ![Bedrijfstoepassingen openen](media/add-gallery-app/open-enterprise-apps.png)


3. Selecteer **nieuwe toepassing**om een galerie-app toe te voegen aan uw Tenant.

    ![Selecteer nieuwe toepassing om een galerie-app toe te voegen aan uw Tenant](media/add-gallery-app/new-application.png)

 4. Overschakelen naar de nieuwe preview-versie van de galerie: Selecteer in de banner boven aan de **pagina een toepassing toevoegen**de koppeling die **hier klikt om de nieuwe en verbeterde app-galerie uit te proberen**.

5. Het deel venster **Bladeren in azure AD galerie** wordt geopend en toont tegels voor Cloud platforms, on-premises toepassingen en aanbevolen toepassingen. Houd er rekening mee dat de toepassingen die worden vermeld in de sectie **Aanbevolen toepassingen** pictogrammen bevatten die aangeven of ze federatieve eenmalige aanmelding (SSO) en het inrichten ondersteunen.

    ![Zoeken naar een app op naam of categorie](media/add-gallery-app/browse-gallery.png)

6. Blader door de galerie voor de toepassing die u wilt toevoegen of zoek naar de toepassing door de naam in het zoekvak in te voeren. Selecteer vervolgens de toepassing uit de resultaten. Beschrijving In het formulier kunt u de naam van de toepassing bewerken zodat deze overeenkomt met de behoeften van uw organisatie.

    ![Laat zien hoe u een toepassing kunt toevoegen vanuit de galerie](media/add-gallery-app/create-application.png)

7. Selecteer **Maken**. Er wordt nu een pagina Aan de slag weergegeven met de opties voor het configureren van de toepassing voor uw organisatie.

## <a name="configure-user-sign-in-properties"></a>Eigenschappen voor gebruikersaanmelding configureren

1. Selecteer **Eigenschappen** om het deel venster Eigenschappen te openen om het te bewerken.

    ![Eigenschappen venster bewerken](media/add-gallery-app/edit-properties.png)

1. Stel de volgende opties in om te bepalen hoe gebruikers die zijn toegewezen aan of niet zijn toegewezen aan de toepassing, zich kunnen aanmelden bij de toepassing en of een gebruiker de toepassing kan zien in het toegangs venster.

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

1. Als u een aangepast logo wilt gebruiken, maakt u een logo van 215 x 215 pixels en slaat u het op in PNG-indeling. Blader vervolgens naar uw logo en upload het.

    ![Het logo wijzigen](media/add-gallery-app/change-logo.png)

1. Wanneer u klaar bent, selecteert u **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

Nu u de toepassing aan uw Azure AD-organisatie hebt toegevoegd, [kiest u de methode voor eenmalige aanmelding](what-is-single-sign-on.md#choosing-a-single-sign-on-method) die u wilt gebruiken en raadpleegt u het juiste artikel hieronder:

- [Eenmalige aanmelding op basis van SAML configureren](configure-single-sign-on-non-gallery-applications.md)
- [Eenmalige aanmelding met een wacht woord configureren](configure-password-single-sign-on-non-gallery-applications.md)
- [Gekoppelde aanmelding configureren](configure-linked-sign-on.md)

