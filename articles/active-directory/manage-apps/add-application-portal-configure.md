---
title: 'Quickstart: Eigenschappen configureren voor een toepassing in uw Azure Active Directory-tenant (Azure AD)'
description: In deze quickstart wordt Azure Portal gebruikt om een toepassing te configureren die is geregistreerd bij uw Azure AD-tenant (Azure Active Directory).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: eb4a150a1680dd4101249458894ded652cde15a0
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338041"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Eigenschappen configureren voor een toepassing in uw Azure Active Directory-tenant (Azure AD)

In de vorige quickstart hebt u een toepassing toegevoegd aan uw Azure Active Directory-tenant (Azure AD). Wanneer u een toepassing toevoegt, laat u uw Azure AD-tenant weten dat deze de ID-provider voor de app is. Nu gaat u een aantal eigenschappen voor de app configureren.
 
## <a name="prerequisites"></a>Vereisten

Als u de eigenschappen van een toepassing in uw Azure AD-Tenant wilt configureren, hebt u het volgende nodig:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een van de volgende rollen: Globale beheerder, Cloudtoepassingsbeheerder, Toepassingsbeheerder of eigenaar van de service-principal.
- Optioneel: Voltooiing van [Uw apps weergeven](view-applications-portal.md).
- Optioneel: Voltooiing van [Een app toevoegen](add-application-portal.md).

>[!IMPORTANT]
>Gebruik een niet-productieomgeving om de stappen in deze quickstart te testen.

## <a name="configure-app-properties"></a>App-eigenschappen configureren

Wanneer u klaar bent met het toevoegen van een toepassing aan uw Azure AD-tenant, verschijnt de overzichtspagina. Als u een toepassing wilt configureren die al is toegevoegd, bekijk dan de eerste quickstart. U krijgt een overzicht te zien van de toepassingen die aan uw tenant zijn toegevoegd. 

Ga als volgt te werk om de eigenschappen van de toepassing bewerken:

1. Selecteer in het Azure AD-portal **Bedrijfstoepassingen**. Zoek en selecteer vervolgens de toepassing die u wilt configureren.
2. Selecteer **Eigenschappen** in de sectie **Beheren** om het deelvenster **Eigenschappen** te openen voor bewerken.

    ![Schermopname van het scherm Eigenschappen waarop de eigenschappen van de bewerkbare app te zien zijn.](media/add-application-portal/edit-properties.png)

3. Neem even de tijd om de opties die beschikbaar zijn voor configuratie te bestuderen:
    - Met **Ingeschakeld voor gebruikers voor aanmelden?** wordt bepaald of gebruikers die zijn toegewezen aan de toepassing, zich kunnen aanmelden.
    - Met **Gebruikerstoewijzing vereist?** wordt bepaald of gebruikers die niet zijn toegewezen aan de toepassing, zich kunnen aanmelden.
    - Met **Zichtbaar voor gebruikers?** wordt bepaald of gebruikers die zijn toegewezen aan een app, deze kunnen zien in het [toegangsvenster](https://myapps.microsoft.com) en het startprogramma voor de Office 365-app. (Bekijk het manu in de linkerbovenhoek van een Office 365- of Microsoft 365-website.)
4. Gebruik de volgende tabellen als hulp bij het kiezen van de beste opties voor uw behoeften.

   - Gedrag voor *toegewezen* gebruikers:

       | Toepassingseigenschap | Toepassingseigenschap | Toepassingseigenschap | Toegewezen gebruikerservaring | Toegewezen gebruikerservaring |
       |---|---|---|---|---|
       | Ingeschakeld voor gebruikers voor aanmelden? | Gebruikerstoewijzing vereist? | Zichtbaar voor gebruiker? | Kunnen toegewezen gebruikers zich aanmelden? | Kunnen toegewezen gebruikers de toepassing zien?* |
       | Ja | Ja | Ja | Ja | Ja  |
       | Ja | Ja | Nee  | Ja | Nee   |
       | Ja | Nee  | Ja | Ja | Ja  |
       | Ja | Nee  | Nee  | Ja | Nee   |
       | Nee  | Ja | Ja | Nee  | Nee   |
       | Nee  | Ja | Nee  | Nee  | Nee   |
       | Nee  | Nee  | Ja | Nee  | Nee   |
       | Nee  | Nee  | Nee  | Nee  | Nee   |

   - Gedrag voor *niet-toegewezen* gebruikers:

       | Toepassingseigenschap | Toepassingseigenschap | Toepassingseigenschap | Niet-toegewezen gebruikerservaring | Niet-toegewezen gebruikerservaring |
       |---|---|---|---|---|
       | Ingeschakeld voor gebruikers voor aanmelden? | Gebruikerstoewijzing vereist? | Zichtbaar voor gebruiker? | Kunnen niet-toegewezen gebruikers zich aanmelden? | Kunnen niet-toegewezen gebruikers de toepassing zien?* |
       | Ja | Ja | Ja | Nee  | Nee   |
       | Ja | Ja | Nee  | Nee  | Nee   |
       | Ja | Nee  | Ja | Ja | Nee   |
       | Ja | Nee  | Nee  | Ja | Nee   |
       | Nee  | Ja | Ja | Nee  | Nee   |
       | Nee  | Ja | Nee  | Nee  | Nee   |
       | Nee  | Nee  | Ja | Nee  | Nee   |
       | Nee  | Nee  | Nee  | Nee  | Nee   |

     *Kan de gebruiker de toepassing zien in het toegangsvenster en het startprogramma voor Office 365-apps?

## <a name="use-a-custom-logo"></a>Een aangepast logo gebruiken

Ga als volgt te werk om een aangepast logo te gebruiken:

1. Maak een logo van 215 x 215 pixels en sla dit op in de .png-indeling.
2. Selecteer in het Azure AD-portal **Bedrijfstoepassingen**. Zoek en selecteer vervolgens de toepassing die u wilt configureren.
3. Selecteer **Eigenschappen** in de sectie **Beheren** om het deelvenster **Eigenschappen** te openen voor bewerken. 
4. Selecteer het pictogram om het logo te uploaden.
5. Selecteer **Opslaan** wanneer u klaar bent.

    ![Schermopname van het scherm Eigenschappen waarop te zien is hoe u het logo kunt wijzigen.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > De miniatuur die in het deelvenster **Eigenschappen** wordt weergegeven, wordt niet meteen bijgewerkt. U kunt het venster **Eigenschappen** sluiten en opnieuw openen om het bijgewerkte pictogram weer te geven.

## <a name="next-steps"></a>Volgende stappen

Nu u de eigenschappen van een toepassing hebt geconfigureerd, kunt u doorgaan met het instellen van eenmalige aanmelding.

- [Eenmalige aanmelding instellen](add-application-portal-setup-sso.md)
- [Een app verwijderen](delete-application-portal.md)