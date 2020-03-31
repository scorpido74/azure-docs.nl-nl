---
title: 'Zelfstudie: Jive configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602eed65745eea1fd9096508c442a27ea79bcba9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057731"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Zelfstudie: Jive configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in Jive en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar Jive.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

*   Een Azure Active directory tenant.
*   Een Jive-abonnement met één teken op ingeschakeld.
*   Een gebruikersaccount in Jive met machtigingen voor teambeheer.

## <a name="assigning-users-to-jive"></a>Gebruikers toewijzen aan Jive

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw Jive-app. Eenmaal besloten, u deze gebruikers toewijzen aan uw Jive app door het volgen van de instructies hier:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Belangrijke tips voor het toewijzen van gebruikers aan Jive

*   Het wordt aanbevolen dat één Azure AD-gebruiker aan Jive wordt toegewezen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

*   Wanneer u een gebruiker aan Jive toewijst, moet u een geldige gebruikersrol selecteren. De rol 'Standaardtoegang' werkt niet voor inrichten.

## <a name="enable-user-provisioning"></a>Gebruikersvoorziening inschakelen

In deze sectie u uw Azure AD verbinden met de API voor het inrichten van gebruikersaccounts van Jive en de inrichtingsservice configureren om toegewezen gebruikersaccounts in Jive te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzing in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-On voor Jive in te schakelen, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-user-account-provisioning"></a>Ga als een te meer meting in de inrichting van gebruikersaccounts:

Het doel van deze sectie is om te schetsen hoe u gebruikersvoorzieningen voor Active Directory-gebruikersaccounts naar Jive inschakelen.
Als onderdeel van deze procedure moet u een gebruikersbeveiligingstoken verstrekken dat u bij Jive.com moet aanvragen.

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

1. Als u Jive al hebt geconfigureerd voor één aanmelding, zoekt u naar uw exemplaar van Jive met behulp van het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **Jive** in de toepassingsgalerie. Selecteer Jive in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van Jive en selecteer vervolgens het tabblad **Inrichten.**

1. Stel de **inrichtingsmodus** in op **Automatisch**. 

    ![Provisioning](./media/jive-provisioning-tutorial/provisioning.png)

1. Geef onder de sectie **Beheerdersreferenties** de volgende configuratie-instellingen op:
   
    a. Typ in het tekstvak **Gebruikersnaam van de Jive-beheerder** een Jive-accountnaam waarop het **systeembeheerdersprofiel** in Jive.com is toegewezen.
   
    b. Typ het wachtwoord voor dit account in het tekstvak **Jive-beheerderswachtwoord.**
   
    c. Typ in het tekstvak **URL van de jive-tenant** de URL van de jive-tenant.
      
      > [!NOTE]
      > De URL van de Jive-tenant is URL die door uw organisatie wordt gebruikt om in te loggen op Jive.  
      > Meestal heeft de URL de volgende indeling: **www.\< organisatie\>.jive.com**.          

1. Klik in de Azure-portal op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Jive-app.

1. Voer het e-mailadres in van een persoon of groep die meldingen van provisioning-fouten moet ontvangen in het veld **E-mail melden** en schakel het selectievakje hieronder in.

1. Klik **op Opslaan.**

1. Selecteer Azure **Active Directory-gebruikers synchroniseren met Jive** onder de sectie Toewijzingen.

1. Controleer in de sectie **Toewijzingen van kenmerken** de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Jive. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Jive te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

1. Als u de Azure AD-inrichtingsservice voor Jive wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie Instellingen

1. Klik **op Opslaan.**

Hiermee wordt de eerste synchronisatie gestart van gebruikers en/of groepen die aan Jive zijn toegewezen in de sectie Gebruikers en groepen. De eerste synchronisatie duurt langer om uit te voeren dan de daaropvolgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtingsservice in uw Jive-app.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](tutorial-list.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eén aanmelding configureren](jive-tutorial.md)
