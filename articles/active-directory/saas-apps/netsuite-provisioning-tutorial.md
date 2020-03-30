---
title: 'Zelfstudie: Netsuite OneWorld configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Netsuite OneWorld.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c9a823e6515c2bfe09e1ab7bcef471eb8169e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063292"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Zelfstudie: Netsuite configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in Netsuite OneWorld en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar Netsuite.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

*   Een Azure Active directory tenant.
*   Een Netsuite OneWorld-abonnement. Houd er rekening mee dat automatische gebruikersinrichting momenteel alleen wordt ondersteund met NetSuite OneWorld.
*   Een gebruikersaccount in Netsuite met beheerdersmachtigingen.

## <a name="assigning-users-to-netsuite-oneworld"></a>Gebruikers toewijzen aan Netsuite OneWorld

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw Netsuite-app. Eenmaal besloten, u deze gebruikers toewijzen aan uw Netsuite app door het volgen van de instructies hier:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Belangrijke tips voor het toewijzen van gebruikers aan Netsuite OneWorld

*   Het wordt aanbevolen dat één Azure AD-gebruiker aan Netsuite wordt toegewezen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

*   Wanneer u een gebruiker aan Netsuite toewijst, moet u een geldige gebruikersrol selecteren. De rol 'Standaardtoegang' werkt niet voor inrichten.

## <a name="enable-user-provisioning"></a>Gebruikersvoorziening inschakelen

Met deze sectie u uw Azure AD verbinden met de API voor het inrichten van netsuite's gebruikersaccount, en de inrichtingsservice configureren om toegewezen gebruikersaccounts in Netsuite te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzing in Azure AD.

> [!TIP] 
> U er ook voor kiezen om SAML-gebaseerde single sign-On voor Netsuite in te schakelen, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-user-account-provisioning"></a>Ga als een te meer meting in de inrichting van gebruikersaccounts:

Het doel van deze sectie is om te schetsen hoe u gebruikersvoorzieningen voor Active Directory-gebruikersaccounts naar Netsuite inschakelen.

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

1. Als u Netsuite al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw exemplaar van Netsuite met behulp van het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **Netsuite** in de toepassingsgalerie. Selecteer Netsuite in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van Netsuite en selecteer vervolgens het tabblad **Inrichten.**

1. Stel de **inrichtingsmodus** in op **Automatisch**. 

    ![Provisioning](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Geef onder de sectie **Beheerdersreferenties** de volgende configuratie-instellingen op:
   
    a. Typ in het tekstvak **Beheerdersgebruikersnaam** een Netsuite-accountnaam waarop het **systeembeheerdersprofiel** in Netsuite.com is toegewezen.
   
    b. Typ het wachtwoord voor dit account in het tekstvak **Beheerderswachtwoord.**
      
1. Klik in de Azure-portal op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Netsuite-app.

1. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die meldingen van provisioning-fout moet ontvangen en schakel het selectievakje in.

1. Klik **op Opslaan.**

1. Selecteer Azure **Active Directory-gebruikers synchroniseren met Netsuite** onder de sectie Toewijzingen.

1. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Netsuite. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Netsuite te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

1. Als u de Azure AD-inrichtingsservice voor Netsuite wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie Instellingen

1. Klik **op Opslaan.**

Hiermee wordt de eerste synchronisatie gestart van gebruikers en/of groepen die aan Netsuite zijn toegewezen in de sectie Gebruikers en groepen. Houd er rekening mee dat de eerste synchronisatie langer duurt dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te volgen en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtingsservice in uw Netsuite-app.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](tutorial-list.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eén aanmelding configureren](netsuite-tutorial.md)
