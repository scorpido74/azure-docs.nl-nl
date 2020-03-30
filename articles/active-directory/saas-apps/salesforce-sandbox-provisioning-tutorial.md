---
title: 'Zelfstudie: Salesforce Sandbox configureren voor automatische gebruikersinrichting met Azure Active Directory| Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48df954f680c6774b0aa189453615156f4f0b4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063268"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Zelfstudie: Salesforce Sandbox configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in Salesforce Sandbox en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar Salesforce Sandbox.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

*   Een Azure Active directory tenant.
*   Een geldige tenant voor Salesforce Sandbox for Work of Salesforce Sandbox for Education. U een gratis proefaccount gebruiken voor beide diensten.
*   Een gebruikersaccount in Salesforce Sandbox met machtigingen voor teambeheer.

## <a name="assigning-users-to-salesforce-sandbox"></a>Gebruikers toewijzen aan Salesforce Sandbox

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers of groepen in Azure AD toegang nodig hebben tot uw Salesforce Sandbox-app. Nadat u deze beslissing hebt genomen, u deze gebruikers toewijzen aan uw Salesforce Sandbox-app door de instructies te volgen in [Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Belangrijke tips voor het toewijzen van gebruikers aan Salesforce Sandbox

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Salesforce Sandbox om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker toewijst aan Salesforce Sandbox, moet u een geldige gebruikersrol selecteren. De rol 'Standaardtoegang' werkt niet voor inrichten.

> [!NOTE]
> Deze app importeert aangepaste rollen uit Salesforce Sandbox als onderdeel van het inrichtingsproces, dat de klant mogelijk wil selecteren bij het toewijzen van gebruikers.

## <a name="enable-automated-user-provisioning"></a>Geautomatiseerde gebruikersinrichting inschakelen

In deze sectie u uw Azure AD verbinden met de API voor het inrichten van het gebruikersaccount van Salesforce Sandbox en de inrichtingsservice configureren om toegewezen gebruikersaccounts in Salesforce Sandbox te maken, bij te werken en uit te schakelen op basis van gebruikers- en groep toewijzing in Azure AD.

>[!Tip]
>U er ook voor kiezen om SAML-gebaseerde single sign-On in te schakelen voor Salesforce Sandbox, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="configure-automatic-user-account-provisioning"></a>Automatische gebruikersaccountinrichting configureren

Het doel van deze sectie is om te schetsen hoe u gebruikersvoorzieningen voor Active Directory-gebruikersaccounts in Salesforce Sandbox inschakelen.

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

1. Als u Salesforce Sandbox al hebt geconfigureerd voor één aanmelding, zoekt u naar uw instantie van Salesforce Sandbox met behulp van het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **Salesforce Sandbox** in de toepassingsgalerie. Selecteer Salesforce Sandbox in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van Salesforce Sandbox en selecteer vervolgens het tabblad **Inrichten.**

1. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Provisioning](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. Geef onder de sectie **Beheerdersreferenties** de volgende configuratie-instellingen op:
   
    a. Typ in het tekstvak **Beheerdersgebruikersnaam** een Salesforce Sandbox-accountnaam waarop het **systeembeheerdersprofiel** in Salesforce.com is toegewezen.
   
    b. Typ het wachtwoord voor dit account in het tekstvak **Beheerderswachtwoord.**

1. Als u uw Salesforce Sandbox-beveiligingstoken wilt openen, opent u een nieuw tabblad en meldt u zich aan bij hetzelfde Salesforce Sandbox-beheerdersaccount. Klik in de rechterbovenhoek van de pagina op uw naam en klik vervolgens op **Instellingen**.

     ![Automatische gebruikersvoorziening inschakelen](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Automatische gebruikersvoorziening inschakelen")

1. Klik in het linkernavigatiedeelvenster op **Mijn persoonlijke gegevens** om de gerelateerde sectie uit te vouwen en klik vervolgens op Mijn **beveiligingstoken opnieuw instellen**.
  
    ![Automatische gebruikersvoorziening inschakelen](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Automatische gebruikersvoorziening inschakelen")

1. Klik op de pagina **Beveiligingstoken opnieuw instellen** op de knop **Beveiligingstoken opnieuw instellen.**

    ![Automatische gebruikersvoorziening inschakelen](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Automatische gebruikersvoorziening inschakelen")

1. Schakel het e-mailpostvak in dat aan dit beheerdersaccount is gekoppeld. Zoek naar een e-mail van Salesforce Sandbox.com met het nieuwe beveiligingstoken.

1. Kopieer het token, ga naar het Azure AD-venster en plak het in het veld **Secret Token.**

1. Klik in de Azure-portal op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Salesforce Sandbox-app.

1. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die meldingen van provisioning-fout moet ontvangen en schakel het selectievakje in.

1. Klik **op Opslaan.**  
    
1.  Selecteer Azure **Active Directory-gebruikers synchroniseren met Salesforce Sandbox** onder de sectie Toewijzingen.

1. Controleer in de sectie **Toewijzingen van kenmerken** de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Salesforce Sandbox. De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de gebruikersaccounts in Salesforce Sandbox te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

1. Als u de Azure AD-inrichtingsservice voor Salesforce Sandbox wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie Instellingen

1. Klik **op Opslaan.**

Hiermee wordt de eerste synchronisatie gestart van gebruikers en/of groepen die zijn toegewezen aan Salesforce Sandbox in de sectie Gebruikers en groepen. De eerste synchronisatie duurt langer om uit te voeren dan de daaropvolgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtingsservice in de Salesforce Sandbox-app.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](tutorial-list.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eén aanmelding configureren](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
