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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82a3310f6fc2169a515b4b13d81c88d187bd0f9c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956149"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Eigenschappen configureren voor een toepassing in uw Azure Active Directory-tenant (Azure AD)

In de vorige quickstart hebt u een toepassing toegevoegd aan uw Azure AD-tenant. Wanneer u een toepassing toevoegt, laat u uw Azure AD-tenant weten dat deze de ID-provider voor de app is. Nu gaat u een aantal eigenschappen voor de app configureren.
 
## <a name="prerequisites"></a>Vereisten

Als u de eigenschappen van een toepassing in uw Azure AD-Tenant wilt configureren, hebt u het volgende nodig:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een van de volgende rollen: Globale beheerder, Cloudtoepassingsbeheerder, Toepassingsbeheerder of eigenaar van de service-principal.
- (Optioneel: Voltooiing van [Uw apps weergeven](view-applications-portal.md)).
- (Optioneel: Voltooiing van [Een app toevoegen](add-application-portal.md)).

>[!IMPORTANT]
>U kunt het beste een niet-productieomgeving gebruiken om de stappen in deze quickstart te testen.

## <a name="configure-app-properties"></a>App-eigenschappen configureren

Wanneer u klaar bent met het toevoegen van een toepassing aan uw Azure AD-tenant, wordt er onmiddellijk een overzichtspagina weergegeven. Als u een toepassing configureert die al is toegevoegd, kijkt u naar de eerste quickstart, waarin u leert hoe u de toepassingen weergeeft die aan uw tenant zijn toegevoegd. 

Ga als volgt te werk om de eigenschappen van de toepassing bewerken:

1. Selecteer **Ondernemingstoepassingen** in Azure AD en zoek en selecteer vervolgens de toepassing die u wilt configureren.
2. Selecteer **Eigenschappen** in de sectie Beheren om het deelvenster Eigenschappen te openen voor bewerken.
    ![Geeft het scherm Eigenschappen en bewerkbare app-eigenschappen weer](media/add-application-portal/edit-properties.png)
3. Neem even de tijd om de opties die beschikbaar zijn voor configuratie te bestuderen.
    - Met **Ingeschakeld voor gebruikers voor aanmelden?** wordt bepaald of gebruikers die zijn toegewezen aan de toepassing, zich kunnen aanmelden.
    - Met **Gebruikerstoewijzing vereist?** wordt bepaald of gebruikers die niet zijn toegewezen aan de toepassing, zich kunnen aanmelden.
    - **Zichtbaar voor gebruikers?** bepaalt of gebruikers die zijn toegewezen aan een app deze kunnen zien in het toegangsvenster (https://myapps.microsoft.com) en O365 App Launcher (het wafelmenu in de linkerbovenhoek van een Office 365- of Microsoft 365-website).
4. Gebruik de volgende tabellen als hulp bij het kiezen van de beste opties voor uw behoeften.

   - Gedrag voor **toegewezen** gebruikers:

       | Toepassingseigenschap | Toepassingseigenschap | Toepassingseigenschap | Toegewezen gebruikerservaring | Toegewezen gebruikerservaring |
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

       | Toepassingseigenschap | Toepassingseigenschap | Toepassingseigenschap | Niet-toegewezen gebruikerservaring | Niet-toegewezen gebruikerservaring |
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
2. Selecteer **Ondernemingstoepassingen** in Azure AD en zoek en selecteer vervolgens de toepassing die u wilt configureren.
3. Selecteer **Eigenschappen** in de sectie Beheren om het deelvenster Eigenschappen te openen voor bewerken. 
4. Selecteer het pictogram om het logo te uploaden.
5. Selecteer **Opslaan** wanneer u klaar bent. 
    ![Geeft weer hoe het logo kan worden gewijzigd op de pagina Eigenschappen van de app](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > De miniatuur die in het deelvenster **Eigenschappen** wordt weergegeven, wordt niet meteen bijgewerkt. U kunt de eigenschappen sluiten en opnieuw openen om het bijgewerkte pictogram weer te geven.

## <a name="next-steps"></a>Volgende stappen

Nu u de eigenschappen van een toepassing hebt geconfigureerd, kunt u doorgaan met het instellen van eenmalige aanmelding.

- [Eenmalige aanmelding instellen](add-application-portal-setup-sso.md)
- [Een app verwijderen](delete-application-portal.md)