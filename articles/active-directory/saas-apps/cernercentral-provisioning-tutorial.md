---
title: 'Zelfstudie: Gebruikersinrichting voor Cerner Central - Azure AD'
description: Meer informatie over het configureren van Azure Active Directory om gebruikers automatisch in te richten op een rooster in Cerner Central.
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
ms.openlocfilehash: 5ed04d8fdcc2d79c66e2ebc53c737c78664e4621
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058313"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Zelfstudie: Cerner Central configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in Cerner Central en Azure AD om gebruikersaccounts van Azure AD automatisch in te richten en te de-provisionen naar een gebruikersrooster in Cerner Central.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

* Een Azure Active Directory-tenant
* Een Cerner Centrale huurder

> [!NOTE]
> Azure Active Directory integreert met Cerner Central met behulp van het [SCIM-protocol.](http://www.simplecloud.info/)

## <a name="assigning-users-to-cerner-central"></a>Gebruikers toewijzen aan Cerner Central

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd. 

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot Cerner Central. Eenmaal besloten, u deze gebruikers toewijzen aan Cerner Central door het volgen van de instructies hier:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Belangrijke tips voor het toewijzen van gebruikers aan Cerner Central

* Het wordt aanbevolen om één Azure AD-gebruiker aan Cerner Central toe te wijzen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Zodra de eerste tests voor één gebruiker zijn voltooid, raadt Cerner Central aan om de volledige lijst van gebruikers toe te voegen die bestemd zijn om toegang te krijgen tot een Cerner-oplossing (niet alleen Cerner Central) die moet worden ingericht in het gebruikersrooster van Cerner.  Andere Cerner-oplossingen maken gebruik van deze lijst met gebruikers in het gebruikersrooster.

* Wanneer u een gebruiker aan Cerner Central toewijst, moet u de rol **Gebruiker** selecteren in het toewijzingsdialoogvenster. Gebruikers met de rol 'Standaardtoegang' zijn uitgesloten van inrichten.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Gebruikersvoorziening configureren voor Cerner Central

In deze sectie u uw Azure AD verbinden met het gebruikersrooster van Cerner Central met behulp van de SCIM-gebruikersaccountvoorzienings-API van Cerner en configureren van de inrichtingsservice voor het maken, bijwerken en uitschakelen van toegewezen gebruikersaccounts in Cerner Central op basis van gebruikers- en groepstoewijzing in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-On in te schakelen voor Cerner Central, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen. Zie voor meer informatie de [zelfstudie van Cerner Central single sign-on.](cernercentral-tutorial.md)

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Ga als een automatisch beheer van een gebruikersaccount naar Cerner Central in Azure AD:

Als u gebruikersaccounts wilt inrichten bij Cerner Central, moet u een Cerner Central-systeemaccount aanvragen bij Cerner en een OAuth-token aan toonder genereren dat Azure AD kan gebruiken om verbinding te maken met het SCIM-eindpunt van Cerner. Het wordt ook aanbevolen dat de integratie wordt uitgevoerd in een Cerner sandbox-omgeving voordat deze wordt geïmplementeerd in productie.

1. De eerste stap is ervoor te zorgen dat de mensen die de Cerner- en Azure AD-integratie beheren, een CernerCare-account hebben, dat nodig is om toegang te krijgen tot de documentatie die nodig is om de instructies te voltooien. Gebruik indien nodig de onderstaande URL's om CernerCare-accounts aan te maken in elke toepasselijke omgeving.

   * Sandbox:https://sandboxcernercare.com/accounts/create

   * Productie:https://cernercare.com/accounts/create  

2. Vervolgens moet een systeemaccount worden gemaakt voor Azure AD. Gebruik de onderstaande instructies om een systeemaccount aan te vragen voor uw sandbox- en productieomgevingen.

   * Instructies:https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Sandbox:https://sandboxcernercentral.com/system-accounts/

   * Productie:https://cernercentral.com/system-accounts/

3. Geneer vervolgens een OAuth-token aan toonder voor elk van uw systeemaccounts. Volg hiervoor de onderstaande instructies.

   * Instructies:https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Sandbox:https://sandboxcernercentral.com/system-accounts/

   * Productie:https://cernercentral.com/system-accounts/

4. Ten slotte moet je User Roster Realm-id's aanschaffen voor zowel de sandbox- als productieomgevingen in Cerner om de configuratie te voltooien. Zie voor informatie over het https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIMaanschaffen van dit, zie: . 

5. Nu u Azure AD configureren om gebruikersaccounts in te richten op Cerner. Meld u aan bij de [Azure-portal](https://portal.azure.com)en blader naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

6. Als u Cerner Central al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw voorbeeld van Cerner Central via het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **Cerner Central** in de toepassingsgalerie. Selecteer Cerner Central in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

7. Selecteer uw exemplaar van Cerner Central en selecteer vervolgens het tabblad **Inrichten.**

8. Stel de **inrichtingsmodus** in op **Automatisch**.

   ![Cerner Centrale Inrichting](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Vul de volgende velden in onder **Beheerdersreferenties:**

   * Voer in het veld **URL van tenant** een URL in de onderstaande indeling in, waarbij u 'User-Roster-Realm-ID' vervangt door de realm-ID die u in stap #4 hebt verkregen.

    > Sandbox:https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Productie:https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * Voer in het veld **Secret Token** het OAuth-token aan toonder in dat u hebt gegenereerd in stap #3 en klik op **Verbinding testen**.

   * U ziet een melding voor succes aan de rechterbovenhoek van uw portal.

1. Voer het e-mailadres in van een persoon of groep die meldingen van provisioning-fouten moet ontvangen in het veld **E-mail melden** en schakel het selectievakje hieronder in.

1. Klik op **Opslaan**.

1. Controleer in de sectie **Toewijzingen van kenmerken** de gebruikers- en groepskenmerken die moeten worden gesynchroniseerd van Azure AD naar Cerner Central. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts en -groepen in Cerner Central te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

1. Als u de Azure AD-inrichtingsservice voor Cerner Central wilt inschakelen, wijzigt u de **ininrichtingsstatus** in **Aan** in de sectie **Instellingen**

1. Klik op **Opslaan**.

Hiermee wordt gestart met de eerste synchronisatie van gebruikers en/of groepen die zijn toegewezen aan Cerner Central in de sectie Gebruikers en groepen. De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te volgen en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtingsservice op uw Cerner Central-app.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Cerner Central: Identiteitsgegevens publiceren met Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Zelfstudie: Cerner Central configureren voor eenmalige aanmelding met Azure Active Directory](cernercentral-tutorial.md)
* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
