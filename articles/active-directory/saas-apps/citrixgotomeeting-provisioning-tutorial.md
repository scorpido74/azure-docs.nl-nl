---
title: 'Zelf studie: GoToMeeting configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en GoToMeeting configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 6cb2c90658a69f3e63e9ebe08db41eea0a9bc7fa
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91299694"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Zelf studie: GoToMeeting configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in GoToMeeting en Azure AD om gebruikers accounts van Azure AD automatisch in te richten en te deactiveren naar GoToMeeting.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

*   Een Azure Active Directory-Tenant.
*   Een GoToMeeting-abonnement met eenmalige aanmelding.
*   Een gebruikers account in GoToMeeting met team beheerders machtigingen.

## <a name="assigning-users-to-gotomeeting"></a>Gebruikers toewijzen aan GoToMeeting

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw GoToMeeting-app. Nadat u hebt besloten, kunt u deze gebruikers toewijzen aan uw GoToMeeting-app door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Belang rijke tips voor het toewijzen van gebruikers aan GoToMeeting

*   U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan GoToMeeting om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

*   Wanneer u een gebruiker toewijst aan GoToMeeting, moet u een geldige gebruikersrol selecteren. De rol ' standaard toegang ' werkt niet voor het inrichten.

## <a name="enable-automated-user-provisioning"></a>Automatische gebruikers inrichting inschakelen

In deze sectie vindt u instructies voor het verbinden van uw Azure AD-GoToMeeting en het configureren van de inrichtings service om toegewezen gebruikers accounts in GoToMeeting te maken, bij te werken en uit te scha kelen op basis van de gebruikers-en groeps toewijzing in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor GoToMeeting, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies gelden voor elkaar.

### <a name="to-configure-automatic-user-account-provisioning"></a>Automatische toewijzing van gebruikers accounts configureren:

1. Blader in het [Azure Portal](https://portal.azure.com)naar het gedeelte **Azure Active Directory > Enter prise-apps > alle toepassingen** .

1. Als u GoToMeeting al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van GoToMeeting met behulp van het zoek veld. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **GoToMeeting** in de toepassings galerie. Selecteer GoToMeeting in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van GoToMeeting en selecteer vervolgens het tabblad **inrichten** .

1. Stel de **inrichtings** modus in op **automatisch**. 

    ![Scherm afbeelding van het tabblad inrichten voor GoToMeeting in Azure Portal. De inrichtings modus is ingesteld op automatisch en beheerder gebruikers naam, wacht woord en test verbinding zijn gemarkeerd.](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. Voer de volgende stappen uit in de sectie beheerders referenties:
   
    a. Typ in het tekstvak **GoToMeeting beheer gebruikers naam** de gebruikers naam van een beheerder.

    b. Het wacht woord van de beheerder in het tekstvak **GoToMeeting Administrator-wacht woord** .

1. Klik in het Azure Portal op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw GoToMeeting-app. Als de verbinding mislukt, zorgt u ervoor dat uw GoToMeeting-account beschikt over team beheerders machtigingen en voert u de stap **beheerders referenties** opnieuw uit.

1. Voer het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen in het veld **e-mail melding** en schakel het selectie vakje in.

1. Klik op **opslaan.**

1. Selecteer in de sectie toewijzingen de optie **Azure Active Directory gebruikers synchroniseren met GoToMeeting.**

1. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar GoToMeeting. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in GoToMeeting voor bijwerk bewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

1. Als u de Azure AD-inrichtings service voor GoToMeeting wilt inschakelen, wijzigt u de **inrichtings status** **in in het** gedeelte instellingen

1. Klik op **opslaan.**

Hiermee start u de initiële synchronisatie van gebruikers en/of groepen die zijn toegewezen aan GoToMeeting in de sectie gebruikers en groepen. Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden, zolang de service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen te volgen voor het inrichtings logboek, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtings service in uw GoToMeeting-app.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


