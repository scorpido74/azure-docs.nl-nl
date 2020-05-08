---
title: 'Zelf studie: Sales Force sandbox configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen de sandbox van Azure Active Directory en Sales Force.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063268"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Zelf studie: Sales Force sandbox configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in Sales Force en Azure AD om automatisch gebruikers accounts in te richten en de inrichting uit te voeren vanuit Azure AD naar Sales Force sandbox.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

*   Een Azure Active Directory-Tenant.
*   Een geldige Tenant voor de sandbox voor werk of Sales Force voor onderwijs. U kunt een gratis proef account voor beide services gebruiken.
*   Een gebruikers account in de sandbox Sales Force met team beheerders machtigingen.

## <a name="assigning-users-to-salesforce-sandbox"></a>Gebruikers toewijzen aan de Sales Force-sandbox

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts, worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers of groepen in azure AD toegang nodig hebben tot uw Sales Force-sandbox-app. Nadat u deze beslissing hebt genomen, kunt u deze gebruikers toewijzen aan uw Sales Force-sandbox-app door de instructies in [een gebruiker of groep toewijzen aan een bedrijfs-app te](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) volgen

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Belang rijke tips voor het toewijzen van gebruikers aan Sales Force sandbox

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan de Sales Force-sandbox om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker aan de Sales Force-sandbox toewijst, moet u een geldige gebruikersrol selecteren. De rol ' standaard toegang ' werkt niet voor het inrichten.

> [!NOTE]
> Met deze app worden aangepaste rollen uit Sales Force sandbox geïmporteerd als onderdeel van het inrichtings proces, wat de klant mogelijk wil selecteren wanneer ze gebruikers toewijzen.

## <a name="enable-automated-user-provisioning"></a>Automatische gebruikers inrichting inschakelen

In deze sectie wordt u begeleid bij het koppelen van de API voor het inrichten van de gebruikers account van Azure AD naar Sales Force en het configureren van de inrichtings service om toegewezen gebruikers accounts te maken, bij te werken en uit te scha kelen in Sales Force-sandbox op basis van gebruikers-en groeps toewijzing in azure AD.

>[!Tip]
>U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding voor Sales Force in te scha kelen, gevolgd door de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies gelden voor elkaar.

### <a name="configure-automatic-user-account-provisioning"></a>Automatisch inrichten van gebruikers accounts configureren

Het doel van deze sectie is het maken van een overzicht van het inschakelen van de gebruikers inrichting van Active Directory gebruikers accounts in Sales Force sandbox.

1. Blader in het [Azure Portal](https://portal.azure.com)naar het gedeelte **Azure Active Directory > Enter prise-apps > alle toepassingen** .

1. Als u al een Sales Force-sandbox hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw exemplaar van Sales Force-sandbox met het zoek veld. Als dat niet het geval is, selecteert u de sandbox **toevoegen** en zoeken naar **Sales Force** in de toepassings galerie. Selecteer Sales Force sandbox uit de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van Sales Force sandbox en selecteer vervolgens het tabblad **inrichten** .

1. Stel de **inrichtings modus** in op **automatisch**.

    ![inrichtings](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. Geef onder de sectie **beheerders referenties** de volgende configuratie-instellingen op:
   
    a. Typ in het tekstvak **Administrator-gebruikers** naam een account naam voor de Sales Force-sandbox waaraan het profiel van de **systeem beheerder** is toegewezen in Salesforce.com.
   
    b. Typ in het tekstvak **beheerders wachtwoord** het wacht woord voor dit account.

1. Als u een beveiligings token voor de Sales Force-sandbox wilt ophalen, opent u een nieuw tabblad en meldt u zich aan met hetzelfde account voor de sandbox-beheerder. Klik in de rechter bovenhoek van de pagina op uw naam en klik vervolgens op **instellingen**.

     ![Automatische gebruikers inrichting inschakelen](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Automatische gebruikers inrichting inschakelen")

1. Klik in het navigatie deel venster aan de linkerkant op **persoonlijke gegevens** om de gerelateerde sectie uit te vouwen en klik vervolgens op **mijn beveiligings token opnieuw instellen**.
  
    ![Automatische gebruikers inrichting inschakelen](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Automatische gebruikers inrichting inschakelen")

1. Klik op de pagina **beveiligings token opnieuw instellen** op de knop **beveiligings token opnieuw instellen** .

    ![Automatische gebruikers inrichting inschakelen](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Automatische gebruikers inrichting inschakelen")

1. Controleer het postvak in van de e-mail die is gekoppeld aan dit beheerders account. Zoek naar een e-mail bericht van Sales Force Sandbox.com dat het nieuwe beveiligings token bevat.

1. Kopieer het token, ga naar uw Azure AD-venster en plak dit in het veld **geheime token** .

1. Klik in het Azure Portal op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Sales Force-sandbox-app.

1. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in.

1. Klik op **opslaan.**  
    
1.  Selecteer in de sectie toewijzingen de optie **Azure Active Directory gebruikers synchroniseren met Sales Force.**

1. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Sales Force sandbox. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Sales Force voor bijwerk bewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

1. Als u de Azure AD Provisioning Service voor Sales Force-sandbox wilt inschakelen, wijzigt **u de** **inrichtings status** in in het gedeelte instellingen

1. Klik op **opslaan.**

De eerste synchronisatie van gebruikers en/of groepen die zijn toegewezen aan Sales Force sandbox wordt gestart in de sectie gebruikers en groepen. Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden, zolang de service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen te volgen voor het inrichtings logboek, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtings service op de sandbox-app van Sales Force.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Extra resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](tutorial-list.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eenmalige aanmelding configureren](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
