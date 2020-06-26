---
title: 'Quickstart: Een app toevoegen aan uw Azure Active Directory-tenant'
description: In deze quickstart wordt de Azure-portal gebruikt om een galerietoepassing toe te voegen aan uw Azure AD-tenant (Azure Active Directory).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: b89ab7e44bc71fa442f332cbba35af450f7d5155
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760657"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Quickstart: een toepassing toevoegen aan uw Azure Active Directory-tenant

Azure AD (Azure Active Directory) heeft een galerie met duizenden vooraf geïntegreerde toepassingen. Sommige toepassingen die worden gebruikt in uw organisatie, bevinden zich waarschijnlijk in de galerie. In deze quickstart wordt de Azure-portal gebruikt om een galerietoepassing toe te voegen aan uw Azure AD-tenant (Azure Active Directory).

Nadat een toepassing is toegevoegd aan de Azure AD-tenant, kunt u:

- Gebruikerstoegang tot de toepassing beheren met een voorwaardelijk toepassingsbeleid.
- Gebruikers configureren voor eenmalige aanmelding bij de toepassing via hun Azure AD-accounts.

## <a name="before-you-begin"></a>Voordat u begint

Als u een toepassing wilt toevoegen aan uw tenant, hebt u het volgende nodig:

- Een Azure AD-abonnement
- Een abonnement met eenmalige aanmelding voor de toepassing

Meld u bij de [Azure-portal](https://portal.azure.com) aan als globale beheerder van uw Azure AD-tenant, als beheerder van cloudtoepassingen of als toepassingsbeheerder.

Als u de stappen in deze zelfstudie wilt testen, raden we u aan om een niet-productieomgeving te gebruiken. Als u niet beschikt over een niet-productieomgeving in Azure AD, kunt u [een gratis proefversie van één maand krijgen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Een toepassing toevoegen aan uw Azure AD-tenant

Ga als volgt te werk om een galerietoepassing toe te voegen aan de Azure AD-tenant:

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiepaneel aan de linkerkant.

2. Selecteer **Bedrijfstoepassingen** in het deelvenster **Azure Active Directory**. Het deelvenster **Alle toepassingen** wordt geopend en geeft een willekeurige selectie uit de toepassingen in uw Azure AD-tenant weer.

3. Selecteer **Nieuwe toepassing** om een app uit de galerie toe te voegen aan uw tenant. 

    ![Selecteer Nieuwe toepassing om een galerietoepassing toe te voegen aan uw tenant](media/add-application-portal/new-application.png)

 4. Schakel over naar de nieuwe preview-ervaring voor de galerie: Selecteer in de banner bovenaan de **pagina Een toepassing toevoegen** de koppeling **Klik hier om de nieuwe en verbeterde app-galerie te proberen**.

5. Het deelvenster **Bladeren door de Azure AD-galerie (preview)** wordt geopend met tegels voor cloudplatformen, on-premises toepassingen en uitgelichte toepassingen. Houd er rekening mee dat de toepassingen die in de sectie **Aanbevolen toepassingen** staan pictogrammen bevatten die aangeven of federatieve eenmalige aanmelding (SSO) en inrichting worden ondersteund.

    ![Een app zoeken op naam of categorie](media/add-application-portal/browse-gallery.png)

6. U kunt door de galerie bladeren naar de toepassing die u wilt toevoegen, of de toepassing zoeken door de naam ervan in het zoekvak in te voeren. Selecteer vervolgens de toepassing in de resultaten. U kunt in het formulier de naam van de toepassing bewerken zodat deze overeenkomt met de behoeften in uw organisatie. In dit voorbeeld is de naam gewijzigd in **GitHub-test**.

    ![Geeft weer hoe u een toepassing kunt toevoegen vanuit de galerie](media/add-application-portal/create-application.png)

7. Selecteer **Maken**. Er wordt nu een pagina Aan de slag weergegeven met de opties voor het configureren van de toepassing voor uw organisatie.

U bent klaar met het toevoegen van de toepassing. In de volgende secties ziet u hoe u het logo kunt wijzigen en andere eigenschappen voor de toepassing kunt bewerken.

## <a name="find-your-azure-ad-tenant-application"></a>Uw Azure AD-tenanttoepassing zoeken

Laten we aannemen dat u even weg bent gegaan en dat u nu verdergaat met het configureren van de toepassing. U moet allereerst uw toepassing zoeken.

1. Selecteer **Azure Active Directory** in **[Azure Portal](https://portal.azure.com)** in het navigatiepaneel aan de linkerkant.
1. Selecteer **Bedrijfstoepassingen** in het deelvenster **Azure Active Directory**.
1. Selecteer in de vervolgkeuzelijst **Toepassingstype** de optie **Alle toepassingen** en selecteer vervolgens **Toepassen**. Zie [Tenanttoepassingen weergeven](view-applications-portal.md) voor meer informatie over de weergaveopties.
1. U ziet nu een lijst met alle toepassingen in de Azure AD-tenant. De lijst is een willekeurig voorbeeld. Selecteer een of meer keren **Meer weergeven** om meer toepassingen te zien.
1. Als u snel een toepassing wilt vinden in uw tenant, voert u de naam van de toepassing in het zoekvak in en selecteert u **Toepassen**. In dit voorbeeld wordt de toepassing GitHub-test gevonden die we eerder hebben toegevoegd.

    ![Geeft weer hoe een toepassing kan worden gevonden met behulp van het zoekvenster](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Eigenschappen voor gebruikersaanmelding configureren

Nu u de toepassing hebt gevonden, kunt u deze openen en de eigenschappen van de toepassing configureren.

Ga als volgt te werk om de eigenschappen van de toepassing bewerken:

1. Selecteer de toepassing om deze te openen.
2. Selecteer **Eigenschappen** om het deelvenster Eigenschappen te openen voor bewerken.

    ![Geeft het scherm Eigenschappen en bewerkbare app-eigenschappen weer](media/add-application-portal/edit-properties.png)

3. Neem even de tijd om de opties voor aanmelden goed te bestuderen. De opties bepalen hoe gebruikers die zijn toegewezen of die niet zijn toegewezen zich kunnen aanmelden bij de toepassing. De opties bepalen ook of een gebruiker de toepassing kan zien in het toegangsvenster.

    - Met **Ingeschakeld voor gebruikers voor aanmelden?** wordt bepaald of gebruikers die zijn toegewezen aan de toepassing, zich kunnen aanmelden.
    - Met **Gebruikerstoewijzing vereist?** wordt bepaald of gebruikers die niet zijn toegewezen aan de toepassing, zich kunnen aanmelden.
    - Met **Zichtbaar voor gebruikers?** wordt bepaald of gebruikers die zijn toegewezen aan een app, deze kunnen zien in het toegangsvenster en het startprogramma voor O365.

4. Gebruik de volgende tabellen als hulp bij het kiezen van de beste opties voor uw behoeften.

   - Gedrag voor **toegewezen** gebruikers:

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

   - Gedrag voor **niet-toegewezen** gebruikers:

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

## <a name="use-a-custom-logo"></a>Een aangepast logo gebruiken

Ga als volgt te werk om een aangepast logo te gebruiken:

1. Maak een logo van 215 x 215 pixels en sla dit op in de PNG-indeling.
1. Omdat u uw toepassing al hebt gevonden, moet u deze nu selecteren.
1. Selecteer **Eigenschappen** in het linkerdeelvenster.
1. Upload het logo.
1. Selecteer **Opslaan** wanneer u klaar bent. 

    ![Geeft weer hoe het logo kan worden gewijzigd op de pagina Eigenschappen van de app](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > De miniatuur die in het deelvenster **Eigenschappen** wordt weergegeven, wordt niet meteen bijgewerkt. U kunt de eigenschappen sluiten en opnieuw openen om het bijgewerkte pictogram weer te geven.

## <a name="next-steps"></a>Volgende stappen

Nu u de toepassing aan uw Azure AD-organisatie hebt toegevoegd, [kiest u de methode voor eenmalige aanmelding](what-is-single-sign-on.md#choosing-a-single-sign-on-method) die u wilt gebruiken. Raadpleeg hieronder het relevante artikel:

- [Eenmalige aanmelding op basis van SAML configureren](configure-single-sign-on-non-gallery-applications.md)
- [Eenmalige aanmelding op basis van wachtwoord configureren](configure-password-single-sign-on-non-gallery-applications.md)
- [Gekoppelde aanmelding configureren](configure-linked-sign-on.md)
