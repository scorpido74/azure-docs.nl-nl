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
ms.openlocfilehash: f93a25854bc8b93e9bd75ea71037f6363d143914
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145727"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Een toepassing toevoegen aan uw Azure Active Directory-tenant (Azure AD)

Azure AD (Azure Active Directory) heeft een galerie met duizenden vooraf geïntegreerde toepassingen. Veel van de toepassingen die in uw organisatie worden gebruikt, bevinden zich waarschijnlijk al in de galerie.

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

Om een toepassing toevoegen aan uw Azure Active Directory-tenant:

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiepaneel aan de linkerkant.
2. Selecteer **Bedrijfstoepassingen** in het deelvenster **Azure Active Directory**. Het deelvenster **Alle toepassingen** wordt geopend en geeft een willekeurige selectie uit de toepassingen in uw Azure AD-tenant weer.
3. Selecteer in het deelvenster **Ondernemingstoepassingen** de optie **Nieuwe toepassing**. 
    ![Selecteer Nieuwe toepassing om een galerietoepassing toe te voegen aan uw tenant](media/add-application-portal/new-application.png)
4. Schakel over naar de nieuwe preview-ervaring voor de galerie: Selecteer in de banner bovenaan de **pagina Een toepassing toevoegen** de koppeling **Klik hier om de nieuwe en verbeterde app-galerie te proberen**.
5. Het deelvenster **Bladeren door de Azure AD-galerie (preview)** wordt geopend met tegels voor cloudplatformen, on-premises toepassingen en uitgelichte toepassingen. Toepassingen in de sectie **Aanbevolen toepassingen** hebben pictogrammen die aangeven of federatieve eenmalige aanmelding (SSO) en inrichting worden ondersteund.
    ![Een app zoeken op naam of categorie](media/add-application-portal/browse-gallery.png)
6. U kunt door de galerie bladeren naar de toepassing die u wilt toevoegen, of de toepassing zoeken door de naam ervan in het zoekvak in te voeren. Selecteer vervolgens de toepassing in de resultaten. U kunt in het formulier de naam van de toepassing bewerken zodat deze overeenkomt met de behoeften in uw organisatie. In dit voorbeeld hebben we GitHub geselecteerd en de naam gewijzigd in **GitHub-test**.
    ![Geeft weer hoe u een toepassing kunt toevoegen vanuit de galerie](media/add-application-portal/create-application.png)
    >[!TIP]
    >Als de toepassing die u zoekt zich niet in de galerie bevindt, dan kunt u klikken op de koppeling **Uw eigen toepassing maken**. Microsoft heeft al met veel ontwikkelaars van toepassingen samengewerkt om ze voor te configureren voor Azure AD. Dit zijn de apps die in de galerie worden weergegeven. Maar als de app die u wilt toevoegen niet wordt weergegeven, dan kunt u een nieuwe, algemene app maken en deze vervolgens zelf configureren of de richtlijnen van de ontwikkelaar volgen om dat te doen.
7. Selecteer **Maken**. Er wordt nu een pagina Aan de slag weergegeven met de opties voor het configureren van de toepassing voor uw organisatie.

U hebt een toepassing toegevoegd. In de volgende quickstart ziet u hoe u het logo kunt wijzigen en andere eigenschappen van de toepassing kunt bewerken.

## <a name="next-steps"></a>Volgende stappen

- [Een app configureren](add-application-portal-configure.md)
- [Eenmalige aanmelding instellen](add-application-portal-setup-sso.md)
- [Een app verwijderen](delete-application-portal.md)