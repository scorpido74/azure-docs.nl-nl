---
title: 'Quickstart: Een toepassing toevoegen aan uw Azure Active Directory-tenant (Azure AD)'
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
ms.openlocfilehash: 044c55e64c6532bf08d31b2c052e4ee9f5069ff1
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956097"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Een toepassing toevoegen aan uw Azure Active Directory-tenant (Azure AD)

Azure AD (Azure Active Directory) heeft een galerie met duizenden vooraf geïntegreerde toepassingen. Veel van de toepassingen die in uw organisatie worden gebruikt, bevinden zich waarschijnlijk al in de galerie. In deze quickstart wordt Azure Portal gebruikt om een galerietoepassing toe te voegen aan uw Azure AD-tenant. Deze quickstart is gericht op het toevoegen van een app die al deel uitmaakt van de galerie. Apps die niet zijn opgenomen in de galerie, kunnen ook worden geïntegreerd met Azure AD, maar dat valt niet binnen het bereik van deze quickstart. 

Nadat een toepassing is toegevoegd aan de Azure AD-tenant, kunt u:

- Eigenschappen voor de app configureren.
- Gebruikerstoegang voor de toepassing beheren met beleid voor voorwaardelijke toegang.
- Configureer eenmalige aanmelding (SSO), zodat gebruikers zich met hun Azure AD-referenties kunnen aanmelden bij de app.

## <a name="prerequisites"></a>Vereisten

Een toepassing toevoegen aan uw Azure AD-tenant:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een van de volgende rollen: Globale beheerder, Cloudtoepassingsbeheerder, Toepassingsbeheerder of eigenaar van de service-principal.
- (Optioneel: Voltooiing van [Uw apps weergeven](view-applications-portal.md)).

>[!IMPORTANT]
>U kunt het beste een niet-productieomgeving gebruiken om de stappen in deze quickstart te testen.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Een app toevoegen aan uw Azure AD-tenant

Ga als volgt te werk om een galerietoepassing toe te voegen aan de Azure AD-tenant:

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiepaneel aan de linkerkant.
2. Selecteer **Bedrijfstoepassingen** in het deelvenster **Azure Active Directory**. Het deelvenster **Alle toepassingen** wordt geopend en geeft een willekeurige selectie uit de toepassingen in uw Azure AD-tenant weer.
3. Selecteer in het deelvenster **Ondernemingstoepassingen** de optie **Nieuwe toepassing**. 
    ![Selecteer Nieuwe toepassing om een galerietoepassing toe te voegen aan uw tenant](media/add-application-portal/new-application.png)
4. Schakel over naar de nieuwe preview-ervaring voor de galerie: Selecteer in de banner bovenaan de **pagina Een toepassing toevoegen** de koppeling **Klik hier om de nieuwe en verbeterde app-galerie te proberen**.
5. Het deelvenster **Bladeren door de Azure AD-galerie (preview)** wordt geopend met tegels voor cloudplatformen, on-premises toepassingen en uitgelichte toepassingen. Toepassingen in de sectie **Aanbevolen toepassingen** hebben pictogrammen die aangeven of federatieve eenmalige aanmelding (SSO) en inrichting worden ondersteund.
    ![Een app zoeken op naam of categorie](media/add-application-portal/browse-gallery.png)
6. U kunt door de galerie bladeren naar de toepassing die u wilt toevoegen, of de toepassing zoeken door de naam ervan in het zoekvak in te voeren. Selecteer vervolgens de toepassing in de resultaten. U kunt in het formulier de naam van de toepassing bewerken zodat deze overeenkomt met de behoeften in uw organisatie. In dit voorbeeld is de naam gewijzigd in **GitHub-test**.
    ![Geeft weer hoe u een toepassing kunt toevoegen vanuit de galerie](media/add-application-portal/create-application.png)
7. Selecteer **Maken**. Er wordt nu een pagina Aan de slag weergegeven met de opties voor het configureren van de toepassing voor uw organisatie.

U hebt een toepassing toegevoegd. In de volgende quickstart ziet u hoe u het logo kunt wijzigen en andere eigenschappen van de toepassing kunt bewerken.

## <a name="next-steps"></a>Volgende stappen

- [Een app configureren](add-application-portal-configure.md)
- [Eenmalige aanmelding instellen](add-application-portal-setup-sso.md)
- [Een app verwijderen](delete-application-portal.md)