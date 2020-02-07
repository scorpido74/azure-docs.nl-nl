---
title: 'Zelf studie: DocuSign configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058175"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Zelf studie: DocuSign configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in DocuSign en Azure AD om gebruikers accounts van Azure AD automatisch in te richten en te deactiveren naar DocuSign.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

*   Een Azure Active Directory-Tenant.
*   Een DocuSign-abonnement met eenmalige aanmelding.
*   Een gebruikers account in DocuSign met team beheerders machtigingen.

## <a name="assigning-users-to-docusign"></a>Gebruikers toewijzen aan DocuSign

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts, worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw DocuSign-app. Nadat u hebt besloten, kunt u deze gebruikers toewijzen aan uw DocuSign-app door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Belang rijke tips voor het toewijzen van gebruikers aan DocuSign

*   U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan DocuSign om de inrichtings configuratie te testen. Extra gebruikers kunnen later worden toegewezen.

*   Wanneer u een gebruiker toewijst aan DocuSign, moet u een geldige gebruikersrol selecteren. De rol ' standaard toegang ' werkt niet voor het inrichten.

> [!NOTE]
> Azure AD biedt geen ondersteuning voor het inrichten van groepen met de Docusign-toepassing, alleen gebruikers kunnen worden ingericht.

## <a name="enable-user-provisioning"></a>Gebruikers inrichten inschakelen

In deze sectie vindt u instructies voor het verbinden van uw Azure AD-DocuSign en het configureren van de inrichtings service om toegewezen gebruikers accounts in DocuSign te maken, bij te werken en uit te scha kelen op basis van de gebruikers-en groeps toewijzing in azure AD.

> [!Tip]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor DocuSign, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies gelden voor elkaar.

### <a name="to-configure-user-account-provisioning"></a>Het inrichten van een gebruikers account configureren:

Het doel van deze sectie is het maken van een overzicht van de gebruikers inrichting van Active Directory gebruikers accounts in te stellen op DocuSign.

1. Blader in het [Azure Portal](https://portal.azure.com)naar het gedeelte **Azure Active Directory > Enter prise-apps > alle toepassingen** .

1. Als u DocuSign al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van DocuSign met behulp van het zoek veld. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **DocuSign** in de toepassings galerie. Selecteer DocuSign in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw exemplaar van DocuSign en selecteer vervolgens het tabblad **inrichten** .

1. Stel de **inrichtings modus** in op **automatisch**. 

    ![inrichtings](./media/docusign-provisioning-tutorial/provisioning.png)

1. Geef onder de sectie **beheerders referenties** de volgende configuratie-instellingen op:
   
    a. Typ in het tekstvak **gebruikers naam beheerder** een DocuSign-account naam waaraan het profiel van de **systeem beheerder** is toegewezen in DocuSign.com.
   
    b. Typ in het tekstvak **beheerders wachtwoord** het wacht woord voor dit account.

1. Klik in het Azure Portal op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw DocuSign-app.

1. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in.

1. Klik op **opslaan.**

1. Selecteer in de sectie toewijzingen de optie **Azure Active Directory gebruikers synchroniseren met DocuSign.**

1. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar DocuSign. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in DocuSign voor bijwerk bewerkingen. Selecteer de knop Opslaan om door te voeren van eventuele wijzigingen.

1. Als u de Azure AD-inrichtings service voor DocuSign wilt inschakelen, wijzigt u de **inrichtings status** **in in het** gedeelte instellingen

1. Klik op **opslaan.**

Hiermee start u de initiële synchronisatie van gebruikers die zijn toegewezen aan DocuSign in de sectie gebruikers en groepen. De eerste synchronisatie langer duren om uit te voeren dan het volgende wordt gesynchroniseerd, die ongeveer elke 40 minuten optreden als de service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen te volgen voor het inrichtings logboek, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtings service in uw DocuSign-app.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](docusign-tutorial.md)
