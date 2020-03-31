---
title: Snelstart - Een app toevoegen aan uw Azure Active Directory-tenant
description: In deze quickstart wordt de Azure-portal gebruikt om een galerietoepassing toe te voegen aan uw Azure AD-tenant (Azure Active Directory).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 650a4f643f170fc64dd0e0643d645a7dcbffdfbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240490"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Quickstart: Een toepassing toevoegen aan uw Azure Active Directory-tenant

Azure AD (Azure Active Directory) heeft een galerie met duizenden vooraf geïntegreerde toepassingen. Sommige toepassingen die worden gebruikt in uw organisatie, bevinden zich waarschijnlijk in de galerie. In deze quickstart wordt de Azure-portal gebruikt om een galerietoepassing toe te voegen aan uw Azure AD-tenant (Azure Active Directory).

Nadat een toepassing is toegevoegd aan de Azure AD-tenant, kunt u:

- Beheer de toegang van gebruikers tot de toepassing met een beleid voor voorwaardelijke toegang.
- Gebruikers configureren voor eenmalige aanmelding bij de toepassing via hun Azure AD-accounts.

## <a name="before-you-begin"></a>Voordat u begint

Als u een toepassing wilt toevoegen aan uw tenant, hebt u het volgende nodig:

- Een Azure AD-abonnement
- Een abonnement met eenmalige aanmelding voor de toepassing

Meld u bij de [Azure-portal](https://portal.azure.com) aan als globale beheerder van uw Azure AD-tenant, als beheerder van cloudtoepassingen of als toepassingsbeheerder.

Als u de stappen in deze zelfstudie wilt testen, raden we u aan om een niet-productieomgeving te gebruiken. Als u niet beschikt over een niet-productieomgeving in Azure AD, kunt u [een gratis proefversie van één maand krijgen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Een toepassing toevoegen aan uw Azure AD-tenant

Ga als volgt te werk om een galerietoepassing toe te voegen aan de Azure AD-tenant:

1. Selecteer **Azure Active Directory**in de [Azure-portal](https://portal.azure.com)in het linkernavigatiedeelvenster .

2. Selecteer **Enterprise-toepassingen**in het deelvenster **Azure Active Directory** . Het deelvenster **Alle toepassingen** wordt geopend en toont een willekeurig voorbeeld van de toepassingen in uw Azure AD-tenant.

3. Als u een galerie-app aan uw tenant wilt toevoegen, selecteert u **Nieuwe toepassing**. 

    ![Nieuwe toepassing selecteren om een galerie-app aan uw tenant toe te voegen](media/add-application-portal/new-application.png)

 4. Overschakelen naar de nieuwe galerij preview-ervaring: Selecteer in de banner boven aan de **pagina Een toepassing toevoegen**de koppeling met de tekst Klik hier om de nieuwe en verbeterde **app-galerij uit te proberen.**

5. In het deelvenster **Bladeren in Azure AD Gallery (Preview)** worden tegels geopend en weergegeven voor cloudplatforms, on-premises toepassingen en aanbevolen toepassingen. Houd er rekening mee dat de toepassingen in de sectie **Aanbevolen toepassingen** pictogrammen hebben die aangeven of ze een ondersteuning bieden voor federatieve eenmalige aanmelding (SSO) en inprovisioning.

    ![Zoeken naar een app op naam of categorie](media/add-application-portal/browse-gallery.png)

6. U door de galerie bladeren naar de toepassing die u wilt toevoegen of naar de toepassing zoeken door de naam in het zoekvak in te voeren. Selecteer vervolgens de toepassing in de resultaten. In het formulier u de naam van de toepassing bewerken om aan de behoeften van uw organisatie te voldoen. In dit voorbeeld hebben we de naam gewijzigd in **GitHub-test.**

    ![Toont hoe u een toepassing vanuit de galerie toevoegt](media/add-application-portal/create-application.png)

7. Selecteer **Maken**. Er wordt nu een pagina Aan de slag weergegeven met de opties voor het configureren van de toepassing voor uw organisatie.

U bent klaar met het toevoegen van uw toepassing. In de volgende secties ziet u hoe u het logo kunt wijzigen en andere eigenschappen voor de toepassing kunt bewerken.

## <a name="find-your-azure-ad-tenant-application"></a>Uw Azure AD-tenanttoepassing zoeken

Laten we aannemen dat u even weg bent gegaan en dat u nu verdergaat met het configureren van de toepassing. Het eerste ding om te doen is het vinden van uw aanvraag.

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .
1. Selecteer **Enterprise-toepassingen**in het deelvenster **Azure Active Directory** .
1. Selecteer alle **toepassingen**in het vervolgkeuzemenu **Toepassingstype** en selecteer **Vervolgens Toepassen**. Zie [Tenanttoepassingen weergeven](view-applications-portal.md) voor meer informatie over de weergaveopties.
1. U ziet nu een lijst met alle toepassingen in de Azure AD-tenant. De lijst is een willekeurig voorbeeld. Als u meer toepassingen wilt zien, selecteert u Meerdere of meer keren **weergeven.**
1. Als u snel een toepassing in uw tenant wilt vinden, voert u de toepassingsnaam in het zoekvak in en selecteert u **Toepassen**. In dit voorbeeld wordt de GitHub-testtoepassing eerder toegevoegd.

    ![Toont hoe u een toepassing vinden met behulp van het zoekvak](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Eigenschappen voor gebruikersaanmelding configureren

Nu u de toepassing hebt gevonden, u deze openen en toepassingseigenschappen configureren.

Ga als u de toepassingseigenschappen bewerken:

1. Selecteer de toepassing om deze te openen.
2. Selecteer **Eigenschappen** om het deelvenster Eigenschappen te openen voor bewerking.

    ![Toont het scherm Eigenschappen en de eigenschappen van de bewerkbare app](media/add-application-portal/edit-properties.png)

3. Neem even de tijd om de opties voor aanmelden goed te bestuderen. De opties bepalen hoe gebruikers die zijn toegewezen of niet aan de toepassing zijn toegewezen, zich bij de toepassing kunnen aanmelden. En de opties bepalen ook of een gebruiker de toepassing in het toegangspaneel kan zien.

    - **Ingeschakeld voor gebruikers om zich aan te melden?** bepaalt of gebruikers die aan de toepassing zijn toegewezen, zich kunnen aanmelden.
    - **Gebruikerstoewijzing vereist?** hiermee wordt bepaald of gebruikers die niet aan de toepassing zijn toegewezen, zich kunnen aanmelden.
    - **Zichtbaar voor gebruikers?** hiermee wordt bepaald of gebruikers die aan een app zijn toegewezen, deze kunnen zien in het toegangspaneel en het O365-startprogramma.

4. Gebruik de volgende tabellen om u te helpen bij het kiezen van de beste opties voor uw behoeften.

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

## <a name="use-a-custom-logo"></a>Een aangepast logo gebruiken

Ga als volgt te werk om een aangepast logo te gebruiken:

1. Maak een logo van 215 x 215 pixels en sla dit op in de PNG-indeling.
1. Aangezien u uw toepassing al hebt gevonden, selecteert u de toepassing.
1. Selecteer **Eigenschappen**in het linkerdeelvenster .
1. Upload het logo.
1. Wanneer u klaar bent, selecteert u **Opslaan**. 

    ![Toont hoe u het logo wijzigen op de pagina Eigenschappen van de app](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > De miniatuur die in dit deelvenster **Eigenschappen** wordt weergegeven, wordt niet meteen bijgewerkt. U de eigenschappen sluiten en opnieuw openen om het bijgewerkte pictogram te bekijken.

## <a name="next-steps"></a>Volgende stappen

Nu u de toepassing aan uw Azure AD-organisatie hebt toegevoegd, kiest u [één aanmeldingsmethode](what-is-single-sign-on.md#choosing-a-single-sign-on-method) die u wilt gebruiken en verwijst u naar het juiste artikel hieronder:

- [Eenmalige aanmelding op basis van SAML configureren](configure-single-sign-on-non-gallery-applications.md)
- [Eenmalige aanmelding voor wachtwoorden configureren](configure-password-single-sign-on-non-gallery-applications.md)
- [Gekoppelde aanmelding configureren](configure-linked-sign-on.md)
