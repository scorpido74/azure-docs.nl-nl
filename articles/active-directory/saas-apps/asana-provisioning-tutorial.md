---
title: 'Zelf studie: gebruikers inrichten voor asana-Azure AD'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op asana.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: abeac030db419f7fb7d561df5dcd407684f20ca2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058904"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Zelf studie: asana configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in asana en Azure Active Directory (Azure AD) om gebruikers accounts van Azure AD automatisch in te richten en te deactiveren naar asana.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

* Een Azure AD-Tenant
* Een asana-Tenant met een [ondernemings](https://www.asana.com/pricing) plan of beter in te scha kelen
* Een gebruikers account in asana met beheerders machtigingen

> [!NOTE]
> Integratie van Azure AD-inrichting is afhankelijk van de [asana-API](https://asana.com/developers/api-reference/users), die beschikbaar is voor asana.

## <a name="assign-users-to-asana"></a>Gebruikers toewijzen aan asana

Azure AD gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers gesynchroniseerd die zijn toegewezen aan een toepassing in azure AD.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers in azure AD toegang nodig hebben tot uw asana-app. U kunt deze gebruikers vervolgens aan uw asana-app toewijzen door de volgende instructies te volgen:

[Een gebruiker toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Belang rijke tips voor het toewijzen van gebruikers aan asana

We raden u aan één Azure AD-gebruiker toe te wijzen aan asana om de inrichtings configuratie te testen. Extra gebruikers kunnen later worden toegewezen.

## <a name="configure-user-provisioning-to-asana"></a>Gebruikers inrichten configureren voor asana

In deze sectie wordt u begeleid bij het verbinden van uw Azure AD to asana user account Provisioning API. U kunt de inrichtings service ook configureren om toegewezen gebruikers accounts in asana te maken, bij te werken en uit te scha kelen op basis van gebruikers toewijzingen in azure AD.

> [!TIP]
> Als u eenmalige aanmelding op basis van SAML voor asana wilt inschakelen, volgt u de instructies in de [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Automatische toewijzing van gebruikers accounts configureren voor asana in azure AD

1. Ga in het [Azure Portal](https://portal.azure.com)naar de sectie **Azure Active Directory**  >  **Enter prise apps**  >  **all applications** .

1. Als u asana al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van asana met behulp van het zoek veld. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **asana** in de toepassings galerie. Selecteer **asana** in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van asana en selecteer vervolgens het tabblad **inrichten** .

1. Stel de **inrichtings modus** in op **automatisch**.

    ![Asana-inrichting](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. Volg de instructies in de sectie **beheerders referenties** om het token te genereren en in te voeren in een **geheim token**:

    a. Meld u aan bij [asana](https://app.asana.com) met uw beheerders account.

    b. Selecteer de profiel foto in de bovenste balk en selecteer de huidige instellingen voor de organisatie naam.

    c. Ga naar het tabblad **service accounts** .

    d. Selecteer **Service account toevoegen**.

    e. Update de **naam** en de **informatie over** de profiel foto, indien nodig. Kopieer het token in **token**en selecteer dit in **wijzigingen opslaan**.

1. In de Azure Portal selecteert u **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw asana-app. Als de verbinding mislukt, zorgt u ervoor dat uw asana-account beheerders machtigingen heeft en voert u de stap **verbinding testen** opnieuw uit.

1. Voer het e-mail adres in van een persoon of groep die u wilt ontvangen van de inrichtings fout meldingen in de **e-mail melding**. Schakel het selectie vakje eronder in.

1. Selecteer **Opslaan**.

1. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met asana**.

1. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die moeten worden gesynchroniseerd van Azure AD naar asana. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in asana voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren. Zie [aangepaste kenmerk toewijzingen voor gebruikers inrichten aanpassen](../app-provisioning/customize-application-attributes.md)voor meer informatie.

1. Als u de Azure AD-inrichtings service voor asana wilt inschakelen, wijzigt u de **inrichtings status** in het gedeelte **instellingen** in **op aan**.

1. Selecteer **Opslaan**.

Nu wordt de eerste synchronisatie gestart voor alle gebruikers die zijn toegewezen aan asana in de sectie **gebruikers** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden, zolang de service wordt uitgevoerd. Gebruik de sectie **synchronisatie Details** om de voortgang te bewaken en de koppelingen naar de activiteiten logboeken te volgen. In de audit logboeken worden alle acties beschreven die worden uitgevoerd door de inrichtings service in uw asana-app.

Zie [rapport over automatische toewijzing van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Het inrichten van gebruikers accounts beheren voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](asana-tutorial.md)
