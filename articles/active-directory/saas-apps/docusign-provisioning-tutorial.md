---
title: 'Zelfstudie: DocuSign configureren voor automatische gebruikersinrichting met Azure Active Directory| Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88b65c8e8962ad8420ded47da1a343672123c589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058175"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Zelfstudie: DocuSign configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in DocuSign en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen, van Azure AD naar DocuSign.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

*   Een Azure Active directory tenant.
*   Een met een voormelding ingeschakeld met Een Met EenMalige aanmelding ingeschakeld.
*   Een gebruikersaccount in DocuSign met machtigingen voor teambeheer.

## <a name="assigning-users-to-docusign"></a>Gebruikers toewijzen aan DocuSign

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw DocuSign-app. Eenmaal besloten, u deze gebruikers toewijzen aan uw DocuSign-app door de instructies hier te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Belangrijke tips voor het toewijzen van gebruikers aan DocuSign

*   Het wordt aanbevolen dat één Azure AD-gebruiker aan DocuSign wordt toegewezen om de inrichtingsconfiguratie te testen. Extra gebruikers kunnen later worden toegewezen.

*   Wanneer u een gebruiker aan DocuSign toewijs, moet u een geldige gebruikersrol selecteren. De rol 'Standaardtoegang' werkt niet voor inrichten.

> [!NOTE]
> Azure AD ondersteunt geen groepsinrichting met de Docusign-toepassing, alleen gebruikers kunnen worden ingericht.

## <a name="enable-user-provisioning"></a>Gebruikersvoorziening inschakelen

Met deze sectie u uw Azure AD verbinden met de API voor het inrichten van het gebruikersaccount van DocuSign en de inrichtingsservice configureren om toegewezen gebruikersaccounts in DocuSign te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzing in Azure AD.

> [!Tip]
> U er ook voor kiezen om SAML-gebaseerde single sign-On in te schakelen voor DocuSign, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-user-account-provisioning"></a>Ga als een te meer meting in de inrichting van gebruikersaccounts:

Het doel van deze sectie is om te schetsen hoe u gebruikersvoorzieningen voor Active Directory-gebruikersaccounts naar DocuSign inschakelen.

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

1. Als u DocuSign al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw exemplaar van DocuSign met behulp van het zoekveld. Selecteer Anders **DocuSign** **toevoegen** en zoeken in de toepassingsgalerie. Selecteer DocuSign in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van DocuSign en selecteer vervolgens het tabblad **Inrichten.**

1. Stel de **inrichtingsmodus** in op **Automatisch**. 

    ![Provisioning](./media/docusign-provisioning-tutorial/provisioning.png)

1. Geef onder de sectie **Beheerdersreferenties** de volgende configuratie-instellingen op:
   
    a. Typ in het tekstvak **Beheerdersgebruikersnaam** een DocuSign-accountnaam waarop het **systeembeheerdersprofiel** in DocuSign.com is toegewezen.
   
    b. Typ het wachtwoord voor dit account in het tekstvak **Beheerderswachtwoord.**

1. Klik in de Azure-portal op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw DocuSign-app.

1. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die meldingen van provisioning-fout moet ontvangen en schakel het selectievakje in.

1. Klik **op Opslaan.**

1. Selecteer Azure **Active Directory-gebruikers synchroniseren met DocuSign** onder de sectie Toewijzingen.

1. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar DocuSign. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in DocuSign te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

1. Als u de Azure AD-inrichtingsservice voor DocuSign wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie Instellingen

1. Klik **op Opslaan.**

Hiermee wordt de eerste synchronisatie gestart van alle gebruikers die aan DocuSign zijn toegewezen in de sectie Gebruikers en groepen. De eerste synchronisatie duurt langer om uit te voeren dan de daaropvolgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te volgen en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtingsservice in uw DocuSign-app.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](tutorial-list.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eén aanmelding configureren](docusign-tutorial.md)
