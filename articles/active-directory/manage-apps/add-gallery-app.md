---
title: Een galerie-app toevoegen-Azure Active Directory | Microsoft Docs
description: Meer informatie over het toevoegen van een app uit de Azure AD-galerie aan uw Azure-bedrijfs toepassingen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77b1794f5dde4d7e346e45c2da5d24195d69b50e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555197"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Een galerie-app toevoegen aan uw Azure AD-organisatie

Azure Active Directory (Azure AD) bevat een galerie met duizenden vooraf geïntegreerde toepassingen die zijn ingeschakeld met eenmalige aanmelding in de onderneming. In dit artikel worden de algemene stappen beschreven voor het toevoegen van een app uit de galerie aan uw Azure AD-organisatie.

> [!IMPORTANT]
> Controleer eerst op uw app in de [lijst met zelf studies over het integreren van SaaS-apps met Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). U vindt waarschijnlijk stapsgewijze richt lijnen voor het toevoegen en configureren van de galerie-app die u wilt toevoegen.

## <a name="add-a-gallery-application"></a>Een galerie toepassing toevoegen

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan als globale beheerder van uw Azure AD-tenant, als beheerder van cloudtoepassingen of als toepassingsbeheerder.

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiepaneel aan de linkerkant.

1. Selecteer **Bedrijfstoepassingen** in het deelvenster **Azure Active Directory**.

    ![Bedrijfstoepassingen openen](media/add-gallery-app/open-enterprise-apps.png)


3. Selecteer **Nieuwe toepassing** om een app uit de galerie toe te voegen aan uw tenant.

    ![Selecteer Nieuwe toepassing om een galerietoepassing toe te voegen aan uw tenant](media/add-gallery-app/new-application.png)

 4. Schakel over naar de nieuwe preview-ervaring voor de galerie: Selecteer in de banner bovenaan de **pagina Een toepassing toevoegen** de koppeling **Klik hier om de nieuwe en verbeterde app-galerie te proberen**.

5. Het deel venster **Bladeren in azure AD galerie** wordt geopend en toont tegels voor Cloud platforms, on-premises toepassingen en aanbevolen toepassingen. Houd er rekening mee dat de toepassingen die in de sectie **Aanbevolen toepassingen** staan pictogrammen bevatten die aangeven of federatieve eenmalige aanmelding (SSO) en inrichting worden ondersteund.

    ![Een app zoeken op naam of categorie](media/add-gallery-app/browse-gallery.png)

6. Blader door de galerie voor de toepassing die u wilt toevoegen of zoek naar de toepassing door de naam in het zoekvak in te voeren. Selecteer vervolgens de toepassing in de resultaten. Beschrijving In het formulier kunt u de naam van de toepassing bewerken zodat deze overeenkomt met de behoeften van uw organisatie.

    ![Geeft weer hoe u een toepassing kunt toevoegen vanuit de galerie](media/add-gallery-app/create-application.png)

7. Selecteer **Maken**. Er wordt nu een pagina Aan de slag weergegeven met de opties voor het configureren van de toepassing voor uw organisatie.

## <a name="configure-user-sign-in-properties"></a>Eigenschappen voor gebruikersaanmelding configureren

1. Selecteer **Eigenschappen** om het deelvenster Eigenschappen te openen voor bewerken.

    ![Eigenschappen venster bewerken](media/add-gallery-app/edit-properties.png)

1. Stel de volgende opties in om te bepalen hoe gebruikers die zijn toegewezen aan of niet zijn toegewezen aan de toepassing, zich kunnen aanmelden bij de toepassing en of een gebruiker de toepassing kan zien in het toegangs venster.

    - **Ingeschakeld voor gebruikers om zich aan te melden,** wordt bepaald of gebruikers die zijn toegewezen aan de toepassing zich kunnen aanmelden.
    - **Gebruikers toewijzing vereist** bepaalt of gebruikers die niet zijn toegewezen aan de toepassing zich kunnen aanmelden.
    - Met **Zichtbaar voor gebruiker** wordt bepaald of gebruikers die zijn toegewezen aan een app, deze kunnen zien in het toegangsvenster en het startprogramma voor O365.

      Gedrag voor **toegewezen** gebruikers:

       | Toepassings eigenschap | Toepassings eigenschap | Toepassings eigenschap | Toegewezen gebruikerservaring | Toegewezen gebruikerservaring |
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
       | Ingeschakeld voor gebruikers voor aanmelden? | Gebruikerstoewijzing vereist? | Zichtbaar voor gebruiker? | Kunnen niet-toegewezen gebruikers zich aanmelden? | Kunnen niet-toegewezen gebruikers de toepassing zien?* |
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

1. Selecteer **Opslaan** wanneer u klaar bent.

## <a name="next-steps"></a>Volgende stappen

Nu u de toepassing aan uw Azure AD-organisatie hebt toegevoegd, [kiest u de methode voor eenmalige aanmelding](what-is-single-sign-on.md#choosing-a-single-sign-on-method) die u wilt gebruiken. Raadpleeg hieronder het relevante artikel:

- [Eenmalige aanmelding op basis van SAML configureren](configure-single-sign-on-non-gallery-applications.md)
- [Eenmalige aanmelding op basis van wachtwoord configureren](configure-password-single-sign-on-non-gallery-applications.md)
- [Gekoppelde aanmelding configureren](configure-linked-sign-on.md)

