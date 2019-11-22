---
title: 'Zelf studie: gebruikers inrichten voor een toegestane vertraging-Azure AD'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op toegestane vertraging.
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
ms.openlocfilehash: a90151679b71364d93446d1acc46a461d2a9d8f9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278170"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Zelf studie: toegestane vertraging voor automatische gebruikers inrichting configureren

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren, en hoe u in azure AD automatisch gebruikers accounts van Azure AD kunt inrichten en de inrichting van een toegestane vertraging.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

* Een Azure Active Directory-tenant
* Een toegestane vertragings Tenant met het [plus plan](https://aadsyncfabric.slack.com/pricing) of beter ingeschakeld
* Een ontoegestane gebruikers account met beheerders machtigingen voor het team

Opmerking: de integratie van Azure AD-inrichting is afhankelijk van de SCIM-API van de [toegestane vertraging](https://api.slack.com/scim), die beschikbaar is voor de vertraging van teams op het plus-of betere niveau.

## <a name="assigning-users-to-slack"></a>Gebruikers toewijzen aan toegestane vertraging

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw app voor toegestane vertraging. Nadat u hebt besloten, kunt u deze gebruikers toewijzen aan uw app voor toegestane vertraging door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Belang rijke tips voor het toewijzen van gebruikers aan een toegestane vertraging

* Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan een toegestane vertraging om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer een gebruiker wordt toegewezen aan een toegestane vertraging, moet u de rol **gebruiker** of groep selecteren in het dialoog venster toewijzing. De rol ' standaard toegang ' werkt niet voor het inrichten.

## <a name="configuring-user-provisioning-to-slack"></a>Gebruikers inrichten configureren voor toegestane vertraging 

In deze sectie wordt u begeleid bij het verbinden van de API voor het inrichten van uw Azure AD-gebruikers account en het configureren van de inrichtings service om toegewezen gebruikers accounts in toegestane vertraging te maken, bij te werken en uit te scha kelen op basis van de gebruikers-en groeps toewijzing in azure AD.

**Tip:** U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te scha kelen voor een toegestane vertraging, gevolgd door de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies gelden voor elkaar.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Automatische toewijzing van gebruikers accounts configureren voor toegestane vertraging in azure AD:

1. Blader in het [Azure Portal](https://portal.azure.com)naar het gedeelte **Azure Active Directory > Enter prise-apps > alle toepassingen** .

2. Als u al een toegestane vertraging voor eenmalige aanmelding hebt geconfigureerd, zoekt u in het zoek veld naar uw exemplaar van de toegestane vertraging. Selecteer anders **toevoegen** en zoeken naar **toegestane vertraging** in de toepassings galerie. Selecteer toegestane vertraging van de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

3. Selecteer uw exemplaar van toegestane vertraging en selecteer vervolgens het tabblad **inrichten** .

4. Stel de **inrichtings modus** in op **automatisch**.

   ![Inrichting van toegestane vertraging](./media/slack-provisioning-tutorial/Slack1.PNG)

5. Klik onder de sectie **beheerders referenties** op **autoriseren**. Hiermee opent u een dialoog venster voor een toegestane autorisatie in een nieuw browser venster.

6. Meld u in het nieuwe venster aan met de beheerders account van uw team. Selecteer in het dialoog venster resulterende autorisatie het uitstel team waarvoor u het inrichten wilt inschakelen en selecteer vervolgens **autoriseren**. Als u klaar bent, keert u terug naar de Azure Portal om de inrichtings configuratie te volt ooien.

    ![Autorisatie dialoogvenster](./media/slack-provisioning-tutorial/Slack3.PNG)

7. Klik in het Azure Portal op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw toegestane marge-app. Als de verbinding mislukt, zorg er dan voor dat uw toegestane account beheerders machtigingen voor het team heeft en probeer de stap ' autoriseren ' opnieuw uit te voeren.

8. Voer het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen in het veld **e-mail melding** en schakel het selectie vakje hieronder in.

9. Klik op **Opslaan**.

10. Selecteer in de sectie toewijzingen de optie **synchronisatie van Azure Active Directory gebruikers naar een toegestane vertraging**.

11. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die vanuit Azure AD worden gesynchroniseerd op toegestane vertraging. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikers accounts in de toegestane vertraging voor update bewerkingen te vergelijken. Selecteer de knop Opslaan om door te voeren van eventuele wijzigingen.

12. Als u de Azure AD-inrichtings service wilt inschakelen voor een toegestane vertraging, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen**

13. Klik op **Opslaan**.

Hiermee wordt de eerste synchronisatie gestart van gebruikers en/of groepen die zijn toegewezen aan toegestane vertraging in de sectie gebruikers en groepen. Houd er rekening mee dat de initiële synchronisatie langer duurt dan volgende synchronisaties, wat ongeveer elke 10 minuten gebeurt, zolang de service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar de inrichtings activiteiten rapporten te volgen. hierin worden alle acties beschreven die worden uitgevoerd door de inrichtings service in de app voor toegestane vertraging.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>Beschrijving Inrichten van groeps object configureren voor toegestane vertraging

U kunt eventueel het inrichten van groeps objecten van Azure AD inschakelen voor een toegestane vertraging. Dit wijkt af van het toewijzen van groepen gebruikers, in dat het daad werkelijke groeps object naast de leden van Azure AD wordt gerepliceerd naar een toegestane vertraging. Als u bijvoorbeeld een groep hebt met de naam ' mijn groep ' in azure AD, wordt een identieke groep met de naam ' mijn groep ' in een toegestane vertraging gemaakt.

### <a name="to-enable-provisioning-of-group-objects"></a>Inrichten van groeps objecten inschakelen:

1. Selecteer in de sectie toewijzingen de optie **Azure Active Directory groepen synchroniseren naar toegestane vertraging**.

2. Stel op de Blade kenmerk toewijzing ingeschakeld in op Ja.

3. Controleer in de sectie **kenmerk toewijzingen** de groeps kenmerken die worden gesynchroniseerd vanuit Azure AD naar een toegestane vertraging. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, overeenkomen met de groepen in de toegestane vertraging voor bijwerk bewerkingen. 

4. Klik op **Opslaan**.

Dit heeft tot gevolg dat groeps objecten die zijn toegewezen aan toegestane vertraging in de sectie **gebruikers en groepen** , volledig worden gesynchroniseerd van Azure AD naar een toegestane vertraging. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen te volgen voor het inrichtings logboek, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtings service in de app voor de toegestane vertraging.

Zie voor meer informatie over het lezen van de Azure AD inrichting logboeken [rapportage over het inrichten van automatische gebruikersaccounts](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector beperkingen

* Bij het configureren van het kenmerk **DisplayName** van de toegestane vertraging moet u rekening houden met het volgende gedrag:

  * Waarden zijn niet helemaal uniek (bijvoorbeeld 2 gebruikers kunnen dezelfde weergave naam hebben)

  * Ondersteunt niet-Engelse tekens, spaties en hoofd letters. 
  
  * Toegestane interpunctie omvat punten, onderstrepings tekens, afbreek streepjes, apostrofs, haakjes (bijvoorbeeld **[{}]** )) en scheidings tekens (bijvoorbeeld **/;** ).
  
  * Alleen updates als deze twee instellingen zijn geconfigureerd in de werk plek/organisatie- **profiel synchronisatie is ingeschakeld** en **gebruikers kunnen de weergave naam niet wijzigen**.
  
* Het kenmerk van de **gebruikers naam** van de toegestane vertraging moet minder dan 21 tekens bevatten en moet een unieke waarde hebben.

* De toegestane vertraging kan alleen overeenkomen met de kenmerken **username** en **e-mail**.  

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
