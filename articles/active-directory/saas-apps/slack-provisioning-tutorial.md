---
title: 'Zelfstudie: Gebruikersinrichting voor Slack - Azure AD'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Slack.
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
ms.openlocfilehash: cdc912c2df435f9b7e591d7c5475e126e6b0aeb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062826"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Zelfstudie: Slack configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in Slack en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar Slack.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

* Een Azure Active Directory-tenant
* Een Slack-tenant met het [Plus-abonnement](https://aadsyncfabric.slack.com/pricing) of beter ingeschakeld
* Een gebruikersaccount in Slack met machtigingen voor teambeheer

Opmerking: De integratie voor azure AD-inrichting is gebaseerd op de [Slack SCIM-API](https://api.slack.com/scim), die beschikbaar is voor Slack-teams op het Plus-abonnement of beter.

## <a name="assigning-users-to-slack"></a>Gebruikers toewijzen aan Slack

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw Slack-app. Zodra u hebt besloten, u deze gebruikers toewijzen aan uw Slack-app door de instructies hier te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Belangrijke tips voor het toewijzen van gebruikers aan Slack

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Slack wordt toegewezen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Slack toewijs, moet u de rol **Gebruiker** of Groep selecteren in het toewijzingsdialoogvenster. De rol 'Standaardtoegang' werkt niet voor inrichten.

## <a name="configuring-user-provisioning-to-slack"></a>Gebruikersvoorziening configureren voor Slack 

In deze sectie u uw Azure AD verbinden met de API voor het inrichten van gebruikersaccounts van Slack en de inrichtingsservice configureren om toegewezen gebruikersaccounts in Slack te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzing in Azure AD.

**Tip:** U er ook voor kiezen om SAML-gebaseerde single sign-On voor Slack in te schakelen, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersaccountvoorziening voor Slack in Azure AD:

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

2. Als u Slack al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van Slack met behulp van het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **Slack** in de toepassingsgalerie. Selecteer Slack in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

3. Selecteer uw exemplaar van Slack en selecteer vervolgens het tabblad **Inrichten.**

4. Stel de **inrichtingsmodus** in op **Automatisch**.

   ![Slack Provisioning](./media/slack-provisioning-tutorial/slack1.png)

5. Klik onder de sectie **Beheerdersreferenties** op **Autoriseren**. Hiermee wordt een dialoogvenster slackautorisatie geopend in een nieuw browservenster.

6. Meld u in het nieuwe venster aan bij Slack met uw Teamadmin-account. selecteer in het dialoogvenster resulterende autorisatie het Slack-team waarvoor u de inrichting wilt inschakelen en selecteer vervolgens **Toestaan**. Ga na voltooiing terug naar de Azure-portal om de inrichtingsconfiguratie te voltooien.

    ![Dialoogvenster Autorisatie](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Klik in de Azure-portal op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Slack-app. Als de verbinding mislukt, moet u ervoor zorgen dat uw Slack-account machtigingen voor teambeheer heeft en probeert u de stap 'Autoriseren' opnieuw.

8. Voer het e-mailadres in van een persoon of groep die meldingen van provisioning-fouten moet ontvangen in het veld **E-mail melden** en schakel het selectievakje hieronder in.

9. Klik op **Opslaan**.

10. Selecteer Azure **Active Directory-gebruikers synchroniseren met Slack**in de sectie Toewijzingen .

11. Controleer in de sectie **Toewijzingen van kenmerken** de gebruikerskenmerken die worden gesynchroniseerd van Azure AD naar Slack. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **eigenschappen matching,** worden gebruikt om de gebruikersaccounts in Slack te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

12. Als u de Azure AD-inrichtingsservice voor Slack wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen**

13. Klik op **Opslaan**.

Hiermee wordt gestart met de eerste synchronisatie van gebruikers en/of groepen die zijn toegewezen aan Slack in de sectie Gebruikers en groepen. Houd er rekening mee dat de eerste synchronisatie langer duurt dan de volgende synchronisaties, die ongeveer elke 10 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te volgen en koppelingen naar het inrichten van activiteitenrapporten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtingsservice in uw Slack-app.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Optioneel] Groepsobjectvoorziening configureren voor Slack

Optioneel u het inrichten van groepsobjecten inschakelen van Azure AD naar Slack. Dit is anders dan het 'toewijzen van groepen gebruikers', omdat het werkelijke groepsobject naast de leden wordt gerepliceerd van Azure AD naar Slack. Als u bijvoorbeeld een groep met de naam 'Mijn groep' in Azure AD hebt, wordt er een identieke groep met de naam 'Mijn groep' gemaakt in Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Ga als gevolg aan het inrichten van groepsobjecten:

1. Selecteer Azure **Active Directory-groepen synchroniseren in**de sectie Toewijzingen in de sectie Azure Active Directory Groups synchroniseren met Slack .

2. Stel in het blad Attribute Mapping in Ingeschakeld op Ja.

3. Controleer in de sectie **Toewijzingen van kenmerken** de groepskenmerken die worden gesynchroniseerd van Azure AD naar Slack. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepen in Slack te matchen voor updatebewerkingen. 

4. Klik op **Opslaan**.

Dit resulteert in groepsobjecten die aan Slack zijn toegewezen in de sectie **Gebruikers en groepen** die volledig worden gesynchroniseerd van Azure AD naar Slack. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te volgen en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtingsservice in uw Slack-app.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

* Houd bij het configureren van het **kenmerk displayName** van Slack rekening met de volgende gedragingen:

  * Waarden zijn niet geheel uniek (bijvoorbeeld 2 gebruikers kunnen dezelfde weergavenaam hebben)

  * Ondersteunt niet-Engelse tekens, spaties, hoofdletters. 
  
  * Toegestane interpunctie omvat perioden, underscores, koppeltekens, apostrofs, beugels (bijvoorbeeld **[ { } )**) ) en afscheiders (bijv. , / **;**).
  
  * Alleen updates als deze twee instellingen zijn geconfigureerd in de werkplek/organisatie van Slack - **Profielsynchronisatie is ingeschakeld** en **gebruikers hun weergavenaam niet kunnen wijzigen.**
  
* Het **kenmerk userName** van Slack moet onder 21 tekens staan en een unieke waarde hebben.

* Slack staat alleen matching toe met de kenmerken **userName** en **e-mail.**  

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
