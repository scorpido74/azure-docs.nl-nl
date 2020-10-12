---
title: 'Zelf studie: gebruikers inrichten voor Pingboard-Azure AD'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Pingboard.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: 29e28e20bd9b471604a450ddb36ef867f0608e06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553759"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Zelf studie: Pingboard configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om u te laten zien welke stappen u moet volgen om automatische inrichting en het ongedaan maken van de inrichting van gebruikers accounts van Azure Active Directory (Azure AD) naar Pingboard in te scha kelen.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

* Een Azure AD-Tenant
* Een Pingboard Tenant [Pro-account](https://pingboard.com/pricing)
* Een gebruikers account in Pingboard met beheerders machtigingen

> [!NOTE]
> Integratie van Azure AD-inrichting is afhankelijk van de [Pingboard-API](https://pingboard.docs.apiary.io/#), die beschikbaar is voor uw account.

## <a name="assign-users-to-pingboard"></a>Gebruikers toewijzen aan Pingboard

Azure AD gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde toepassingen. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers gesynchroniseerd die zijn toegewezen aan een toepassing in azure AD. 

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers in azure AD toegang nodig hebben tot uw Pingboard-app. U kunt deze gebruikers vervolgens aan uw Pingboard-app toewijzen door de volgende instructies te volgen:

[Een gebruiker toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Belang rijke tips voor het toewijzen van gebruikers aan Pingboard

We raden u aan één Azure AD-gebruiker toe te wijzen aan Pingboard om de inrichtings configuratie te testen. Extra gebruikers kunnen later worden toegewezen.

## <a name="configure-user-provisioning-to-pingboard"></a>Gebruikers inrichten configureren voor Pingboard 

In deze sectie wordt u begeleid bij het koppelen van uw Azure AD aan de inrichtings-API van de Pingboard-gebruikers account. U kunt de inrichtings service ook configureren om toegewezen gebruikers accounts te maken, bij te werken en uit te scha kelen in Pingboard die zijn gebaseerd op gebruikers toewijzingen in azure AD.

> [!TIP]
> Als u eenmalige aanmelding op basis van SAML voor Pingboard wilt inschakelen, volgt u de instructies in de [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Automatische toewijzing van gebruikers accounts configureren voor Pingboard in azure AD

1. Ga in het [Azure Portal](https://portal.azure.com)naar de sectie **Azure Active Directory**  >  **Enter prise apps**  >  **all applications** .

1. Als u Pingboard al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van Pingboard met behulp van het zoek veld. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **Pingboard** in de toepassings galerie. Selecteer **Pingboard** in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van Pingboard en selecteer vervolgens het tabblad **inrichten** .

1. Stel de **inrichtings modus** in op **automatisch**.

    ![Pingboard-inrichting](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. Gebruik de volgende stappen onder de sectie **beheerders referenties** :

    a. Voer in **Tenant-URL**, `https://your_domain.pingboard.com/scim/v2` en vervang ' your_domain ' door uw echte domein.

    b. Meld u aan bij [Pingboard](https://pingboard.com/) met uw beheerders account.

    c. Selecteer **invoeg toepassingen**  >  **integraties**  >  **Azure Active Directory**.

    d. Ga naar het tabblad **configureren** en selecteer **gebruikers inrichting van Azure inschakelen**.

    e. Kopieer het token in een **OAuth Bearer-token**en voer dit in als een **geheim token**.

1. In de Azure Portal selecteert u **verbinding testen** om Azure ad te testen kan verbinding maken met uw Pingboard-app. Als de verbinding mislukt, test u of uw Pingboard-account beheerders machtigingen heeft en voert u de stap **verbinding testen** opnieuw uit.

1. Voer het e-mail adres in van een persoon of groep die u wilt ontvangen van de inrichtings fout meldingen in de **e-mail melding**. Schakel het selectie vakje eronder in.

1. Selecteer **Opslaan**.

1. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Pingboard**.

1. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die moeten worden gesynchroniseerd van Azure AD naar Pingboard. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Pingboard voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen toe te passen. Zie [toewijzings kenmerk toewijzingen voor gebruikers aanpassen](../app-provisioning/customize-application-attributes.md)voor meer informatie.

1. Als u de Azure AD-inrichtings service voor Pingboard wilt inschakelen, wijzigt u de **inrichtings status** in het gedeelte **instellingen** in **op aan**.

1. Selecteer **Opslaan** om de eerste synchronisatie te starten van gebruikers die zijn toegewezen aan Pingboard.

Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden, zolang de service wordt uitgevoerd. Gebruik de sectie **synchronisatie Details** om de voortgang te bewaken en de koppelingen naar de activiteiten logboeken te volgen. In de logboeken worden alle acties beschreven die worden uitgevoerd door de inrichtings service in uw Pingboard-app.

Zie [rapport over automatische toewijzing van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Het inrichten van gebruikers accounts beheren voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](pingboard-tutorial.md)
