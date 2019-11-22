---
title: 'Zelf studie: gebruikers inrichten voor CERN-centraal-Azure AD'
description: Meer informatie over het configureren van Azure Active Directory om automatisch gebruikers in te richten op een schema in CERN-centraal.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e80373fa28f1ea24d6a2d5fc2c147bf81b2b279
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276512"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Zelf studie: configureren van CERN-centraal voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in CERN-en Azure AD om gebruikers accounts van Azure AD automatisch in te richten en uit te voeren in een gebruikers rooster in CERN-centraal.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

* Een Azure Active Directory-tenant
* Een CERN-centrale-Tenant

> [!NOTE]
> Azure Active Directory integreert met CERN-centraal met behulp van het [scim](http://www.simplecloud.info/) -protocol.

## <a name="assigning-users-to-cerner-central"></a>Gebruikers toewijzen aan CERN-centraal

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts, worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd. 

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot de CERN-centrale. Eenmaal besloten, kunt u deze gebruikers toewijzen aan CERN-centraal door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Belang rijke tips voor het toewijzen van gebruikers aan CERN-centraal

* Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan CERN-centraal om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als de eerste test voor één gebruiker is voltooid, wordt door CERN-centraal aangeraden de volledige lijst met gebruikers toe te wijzen die zijn bedoeld voor toegang tot een CERN-oplossing (niet alleen voor CERN-centraal), zodat deze kan worden ingericht aan het gebruikers schema van de CERN.  Andere CERN-oplossingen maken gebruik van deze lijst met gebruikers in het rooster van de gebruiker.

* Wanneer u een gebruiker toewijst aan CERN-centraal, moet **u de gebruikersrol** selecteren in het dialoog venster toewijzing. Gebruikers met de rol ' standaard toegang ' zijn uitgesloten van het inrichten.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Gebruikers inrichten configureren voor CERN-centraal

In deze sectie wordt u begeleid bij het koppelen van uw Azure AD aan het gebruikers rooster van de gebruiker met behulp van de SCIM-API van de gebruikers account van Cerner en het configureren van de inrichtings service om toegewezen gebruikers accounts te maken, bij te werken en uit te scha kelen in CERN-centraal op basis van gebruikers-en groeps toewijzing in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor CERN-centraal, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen. Zie voor meer informatie de [zelf studie voor de eenmalige aanmelding in de CERN-centraal](cernercentral-tutorial.md).

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Automatische toewijzing van gebruikers accounts configureren voor CERN-centraal in azure AD:

Als u gebruikers accounts wilt inrichten voor CERN-centraal, moet u een CERN-centraal systeem account aanvragen van CERN en een OAuth Bearer-token genereren dat door Azure AD kan worden gebruikt om verbinding te maken met het SCIM-eind punt van de CERN. Het wordt ook aangeraden de integratie in een CERN-sandbox-omgeving uit te voeren voordat u de productie implementeert.

1. De eerste stap is om ervoor te zorgen dat de mensen die de CERN en Azure AD-integratie beheren, een CernerCare-account hebben dat is vereist voor toegang tot de documentatie die nodig is om de instructies te volt ooien. Gebruik, indien nodig, de onderstaande Url's voor het maken van CernerCare-accounts in elke toepasselijke omgeving.

   * Sandbox: https://sandboxcernercare.com/accounts/create

   * Productie: https://cernercare.com/accounts/create  

2. Vervolgens moet er een systeem account worden gemaakt voor Azure AD. Gebruik de onderstaande instructies om een systeem account aan te vragen voor uw sandbox-en productie omgeving.

   * Instructies: https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Productie: https://cernercentral.com/system-accounts/

3. Genereer vervolgens een OAuth Bearer-token voor elk van uw systeem accounts. Volg hiervoor de onderstaande instructies.

   * Instructies: https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Productie: https://cernercentral.com/system-accounts/

4. Ten slotte moet u gebruikers rooster-Id's voor de sandbox-en productie omgeving ophalen om de configuratie te volt ooien. Zie https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIMvoor meer informatie over hoe u dit kunt verkrijgen. 

5. Nu kunt u Azure AD configureren om gebruikers accounts in te richten op CERN. Meld u aan bij de [Azure Portal](https://portal.azure.com)en blader naar de sectie **Azure Active Directory > Enter prise-apps > alle toepassingen** .

6. Als u de CERN-centrale voor eenmalige aanmelding al hebt geconfigureerd, zoekt u naar uw exemplaar van CERN-centrale met behulp van het zoek veld. Selecteer anders **toevoegen** en zoeken naar **CERN-centraal** in de toepassings galerie. Selecteer CERN-centraal in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

7. Selecteer uw exemplaar van CERN-centraal en selecteer vervolgens het tabblad **inrichten** .

8. Stel de **inrichtings modus** in op **automatisch**.

   ![Multivisioning van CERN-centraal](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Vul de volgende velden in onder **beheerders referenties**:

   * Voer in het veld **Tenant-URL** een URL in de onderstaande notatie in, waarbij ' User-rooster-id ' wordt vervangen door de realm-id die u hebt verkregen in stap #4.

    > Sandbox: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Productie: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * Voer in het veld **geheim token** het OAuth Bearer-token in dat u in stap #3 hebt gegenereerd en klik op **verbinding testen**.

   * U ziet een geslaagde melding aan de upperright-zijde van uw portal.

1. Voer het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen in het veld **e-mail melding** en schakel het selectie vakje hieronder in.

1. Klik op **Opslaan**.

1. Controleer in de sectie **kenmerk toewijzingen** de gebruikers-en groeps kenmerken die moeten worden gesynchroniseerd vanuit Azure AD naar CERN-centraal. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts en-groepen in CERN-centraal voor update bewerkingen. Selecteer de knop Opslaan om door te voeren van eventuele wijzigingen.

1. Als u de Azure AD-inrichtings service voor CERN-centraal wilt inschakelen, wijzigt u de **inrichtings status** **in in het** gedeelte **instellingen**

1. Klik op **Opslaan**.

Hiermee start u de initiële synchronisatie van gebruikers en/of groepen die zijn toegewezen aan CERN-centraal in de sectie gebruikers en groepen. Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie gegevens** gebruiken voor het bewaken van de voortgang en het volgen van koppelingen naar de inrichtings activiteiten logboeken, die alle acties beschrijven die worden uitgevoerd door de inrichtings service in uw CERN-Central-app.

Zie voor meer informatie over het lezen van de Azure AD inrichting logboeken [rapportage over het inrichten van automatische gebruikersaccounts](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Aanvullende bronnen

* [CERN-centraal: identiteits gegevens publiceren met Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Zelf studie: CERN-centraal configureren voor eenmalige aanmelding met Azure Active Directory](cernercentral-tutorial.md)
* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over inrichtings activiteiten](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
