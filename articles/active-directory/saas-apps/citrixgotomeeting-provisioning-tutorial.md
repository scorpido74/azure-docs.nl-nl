---
title: 'Zelfstudie: GoToMeeting configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en GoToMeeting configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0ac06fc3018b4230cbf32712067c48400599082
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058260"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Zelfstudie: GoToMeeting configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in GoToMeeting en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar GoToMeeting.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

*   Een Azure Active directory tenant.
*   Een abonnement met eenmalige aanmelding voor GoToMeeting.
*   Een gebruikersaccount in GoToMeeting met machtigingen voor teambeheer.

## <a name="assigning-users-to-gotomeeting"></a>Gebruikers toewijzen aan GoToMeeting

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw GoToMeeting-app. Zodra u hebt besloten, u deze gebruikers toewijzen aan uw GoToMeeting-app door de instructies hier te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Belangrijke tips voor het toewijzen van gebruikers aan GoToMeeting

*   Het wordt aanbevolen dat één Azure AD-gebruiker aan GoToMeeting wordt toegewezen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

*   Wanneer u een gebruiker aan GoToMeeting toewijs, moet u een geldige gebruikersrol selecteren. De rol 'Standaardtoegang' werkt niet voor inrichten.

## <a name="enable-automated-user-provisioning"></a>Geautomatiseerde gebruikersinrichting inschakelen

In deze sectie u uw Azure AD verbinden met de API voor het inrichten van gebruikersaccounts van GoToMeeting en de inrichtingsservice configureren om toegewezen gebruikersaccounts in GoToMeeting te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzing in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-On in te schakelen voor GoToMeeting, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-account-provisioning"></a>Ga als een automatisch beheer van het gebruikersaccount:

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

1. Als u GoToMeeting al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van GoToMeeting met behulp van het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **GoToMeeting** in de toepassingsgalerie. Selecteer GoToMeeting in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van GoToMeeting en selecteer vervolgens het tabblad **Inrichten.**

1. Stel de **inrichtingsmodus** in op **Automatisch**. 

    ![Provisioning](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. Voer onder de sectie Beheerdersreferenties de volgende stappen uit:
   
    a. Typ in het tekstvak **gebruikersnaam van de GoToMeeting-beheerder** de gebruikersnaam van een beheerder.

    b. In het tekstvak **Wachtwoord voor gotovergaderingsbeheer** wordt het wachtwoord van de beheerder weergegeven.

1. Klik in de Azure-portal op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw GoToMeeting-app. Als de verbinding mislukt, moet u ervoor zorgen dat uw GoToMeeting-account machtigingen voor teambeheer heeft en probeert u de stap **'Beheerdersreferenties'** opnieuw.

1. Voer het e-mailadres in van een persoon of groep die meldingen van provisioning-fouten moet ontvangen in het veld **E-mail melden** en schakel het selectievakje in.

1. Klik **op Opslaan.**

1. Selecteer Azure **Active Directory-gebruikers synchroniseren met GoToMeeting** onder de sectie Toewijzingen.

1. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar GoToMeeting. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in GoToMeeting te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

1. Als u de Azure AD-inrichtingsservice voor GoToMeeting wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie Instellingen

1. Klik **op Opslaan.**

Hiermee wordt de eerste synchronisatie gestart van gebruikers en/of groepen die zijn toegewezen aan GoToMeeting in de sectie Gebruikers en groepen. De eerste synchronisatie duurt langer om uit te voeren dan de daaropvolgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtingsservice in uw GoToMeeting-app.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](tutorial-list.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eén aanmelding configureren](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


