---
title: 'Zelf studie: Sales Force configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32f3c439460ddc61dbf08fc4e8d7b7a000aa20f9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849170"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Zelf studie: Sales Force configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is het weer geven van de stappen die nodig zijn voor het uitvoeren van Sales Force en Azure AD om gebruikers accounts van Azure AD naar Sales Force automatisch in te richten en te deactiveren.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

* Een Azure Active Directory-Tenant
* Een Salesforce.com-Tenant

> [!IMPORTANT]
> Als u een Salesforce.com-proef account gebruikt, kunt u het automatisch inrichten van gebruikers niet configureren. Voor proef accounts is de benodigde API-toegang niet ingeschakeld tot ze zijn gekocht. U kunt deze beperking omzeilen door een gratis [ontwikkelaars account](https://developer.salesforce.com/signup) te gebruiken om deze zelf studie te volt ooien.

Als u een Sales Force-sandbox-omgeving gebruikt, raadpleegt u de zelf studie voor de integratie van de [Sales Force-sandbox](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Gebruikers toewijzen aan Sales Force

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers of groepen in azure AD toegang nodig hebben tot uw Sales Force-app. Nadat u deze beslissing hebt genomen, kunt u deze gebruikers toewijzen aan uw Sales Force-app door de instructies in [een gebruiker of groep toewijzen aan een bedrijfs-app te](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) volgen

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Belang rijke tips voor het toewijzen van gebruikers aan Sales Force

* U wordt aangeraden één Azure AD-gebruiker aan Sales Force toe te wijzen om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker aan Sales Force toewijst, moet u een geldige gebruikersrol selecteren. De rol ' standaard toegang ' werkt niet voor het inrichten

    > [!NOTE]
    > Met deze app worden profielen uit Sales Force geïmporteerd als onderdeel van het inrichtings proces, wat de klant mogelijk wil selecteren bij het toewijzen van gebruikers in azure AD. De profielen die worden geïmporteerd uit Sales Force, worden weer gegeven als rollen in azure AD.

## <a name="enable-automated-user-provisioning"></a>Automatische gebruikers inrichting inschakelen

In deze sectie wordt u begeleid bij het koppelen [van de API-V40 van uw Azure ad-account voor Sales Force](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)en het configureren van de inrichtings service om toegewezen gebruikers accounts in Sales Force te maken, bij te werken en uit te scha kelen op basis van de gebruikers-en groeps toewijzing in azure AD.

> [!Tip]
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding voor Sales Force in te scha kelen, gevolgd door de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies gelden voor elkaar.

### <a name="configure-automatic-user-account-provisioning"></a>Automatisch inrichten van gebruikers accounts configureren

Het doel van deze sectie is het maken van een overzicht van het inschakelen van de gebruikers inrichting van Active Directory gebruikers accounts in Sales Force.

1. Blader in het [Azure Portal](https://portal.azure.com)naar het gedeelte **Azure Active Directory > Enter prise-apps > alle toepassingen** .

2. Als u Sales Force al hebt geconfigureerd voor eenmalige aanmelding, zoekt u in het zoek veld naar uw exemplaar van Sales Force. Selecteer anders **toevoegen** en zoeken naar **Sales Force** in de toepassings galerie. Selecteer Sales Force in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

3. Selecteer uw exemplaar van Sales Force en selecteer vervolgens het tabblad **inrichten** .

4. Stel de **inrichtings modus** in op **automatisch**.

    ![inrichtings](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Geef onder de sectie **beheerders referenties** de volgende configuratie-instellingen op:

    a. Typ in het tekstvak **Administrator-gebruikers** naam een Sales Force-account naam waaraan het **systeem beheerders** profiel in Salesforce.com is toegewezen.

    b. Typ in het tekstvak **beheerders wachtwoord** het wacht woord voor dit account.

6. Als u uw Sales Force-beveiligings token wilt ophalen, opent u een nieuw tabblad en meldt u zich aan bij hetzelfde Sales Force-beheerders account. Klik in de rechter bovenhoek van de pagina op uw naam en klik vervolgens op **instellingen**.

    ![Automatische gebruikers inrichting inschakelen](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Automatische gebruikers inrichting inschakelen")

7. Klik in het navigatie deel venster aan de linkerkant op **persoonlijke gegevens** om de gerelateerde sectie uit te vouwen en klik vervolgens op **mijn beveiligings token opnieuw instellen**.
  
    ![Automatische gebruikers inrichting inschakelen](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Automatische gebruikers inrichting inschakelen")

8. Klik op de pagina **beveiligings token opnieuw** instellen op de knop **beveiligings token opnieuw instellen** .

    ![Automatische gebruikers inrichting inschakelen](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Automatische gebruikers inrichting inschakelen")

9. Controleer het postvak in van de e-mail die is gekoppeld aan dit beheerders account. Zoek naar een e-mail bericht van Salesforce.com dat het nieuwe beveiligings token bevat.

10. Kopieer het token, ga naar uw Azure AD-venster en plak dit in het veld **geheime token** .

11. De **URL** van de Tenant moet worden ingevoerd als het exemplaar van Sales Force zich in de Sales Force Government-Cloud bevindt. Anders is het optioneel. Voer de URL van de Tenant in met de indeling ' https://\<your-instance\>. my.salesforce.com, waarbij u \<uw-instantie\> vervangt door de naam van uw Sales Force-exemplaar.

12. Klik in het Azure Portal op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Sales Force-app.

13. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen en schakel het selectie vakje hieronder in.

14. Klik op **opslaan.**  

15. Selecteer in de sectie toewijzingen de optie **Azure Active Directory gebruikers synchroniseren met Sales Force.**

16. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Sales Force. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om te voldoen aan de gebruikers accounts in Sales Force voor bijwerk bewerkingen. Selecteer de knop Opslaan om door te voeren van eventuele wijzigingen.

17. Als u de Azure AD-inrichtings service voor Sales Force wilt inschakelen, wijzigt **u de** **inrichtings status** in in het gedeelte instellingen

18. Klik op **opslaan.**

> [!NOTE]
> Zodra de gebruikers zijn ingericht in de Sales Force-toepassing, moet de beheerder de taalspecifieke instellingen voor deze toepassingen configureren. Raadpleeg dit artikel voor meer informatie over [de](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) taal configuratie.

Hiermee start u de initiële synchronisatie van gebruikers en/of groepen die zijn toegewezen aan Sales Force in de sectie gebruikers en groepen. Houd er rekening mee dat het uitvoeren van de eerste synchronisatie langer duurt dan volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden, zolang de service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen te volgen voor het inrichtings logboek, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtings service in uw Sales Force-app.

Zie voor meer informatie over het lezen van de Azure AD inrichting logboeken [rapportage over het inrichten van automatische gebruikersaccounts](../manage-apps/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Algemene problemen
* Als u problemen ondervindt met het verlenen van toegang tot Sales Force, moet u het volgende doen:
    * De gebruikte referenties hebben beheerders toegang tot Sales Force.
    * De versie van Sales Force die u gebruikt, ondersteunt webtoegang (bijvoorbeeld ontwikkel aars, ondernemings-, sandbox-en onbeperkte edities van Sales Force.)
    * Web-API-toegang is ingeschakeld voor de gebruiker.
* De Azure AD-inrichtings service biedt ondersteuning voor het inrichten van taal, land instelling en tijd zone voor een gebruiker. Deze kenmerken bevinden zich in de standaard kenmerk toewijzingen, maar hebben geen standaard bron kenmerk. Zorg ervoor dat u het standaard bron kenmerk selecteert en dat het bron kenmerk in de indeling werd verwacht door Sales Force. Zo is localeSidKey voor Engels (Verenigde Staten) en_US. Bekijk de richt lijnen die [hier](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) worden weer gegeven om de juiste localeSidKey-indeling te bepalen. De languageLocaleKey-indelingen kunt u [hier](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5)vinden. U kunt er niet alleen voor zorgen dat de indeling juist is, maar u moet er ook voor zorgen dat de taal voor uw gebruikers is ingeschakeld, zoals [hier](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)wordt beschreven. 
* **SalesforceLicenseLimitExceeded:** De gebruiker kan niet worden gemaakt in de doel toepassing omdat er geen beschik bare licenties voor deze gebruiker zijn. U kunt aanvullende licenties voor de doel toepassing aanschaffen of uw gebruikers toewijzingen en configuratie van kenmerk toewijzing controleren om ervoor te zorgen dat de juiste gebruikers zijn toegewezen met de juiste kenmerken.
* **SalesforceDuplicateUserName:** De gebruiker kan niet worden ingericht omdat deze een Salesforce.com-gebruikers naam heeft die is gedupliceerd in een andere Salesforce.com-Tenant.  In Salesforce.com moet de waarde voor het kenmerk ' username ' uniek zijn in alle Salesforce.com-tenants.  Standaard wordt de userPrincipalName van een gebruiker in Azure Active Directory de gebruikers naam in Salesforce.com.   U hebt twee opties.  U kunt ook de gebruiker met de dubbele gebruikers naam in de andere Salesforce.com-Tenant vinden en de naam ervan wijzigen als u de andere Tenant beheert.  De andere optie is om de toegang van de Azure Active Directory gebruiker te verwijderen tot de Salesforce.com-Tenant waarmee uw directory is geïntegreerd. Deze bewerking wordt opnieuw geprobeerd tijdens de volgende synchronisatie poging. 
* **SalesforceRequiredFieldMissing:** Voor Sales Force moeten bepaalde kenmerken aanwezig zijn op de gebruiker om de gebruiker te kunnen maken of bij te werken. Er ontbreekt een van de vereiste kenmerken voor deze gebruiker. Zorg ervoor dat kenmerken zoals e-mail en alias worden ingevuld voor alle gebruikers die u wilt inrichten voor Sales Force. U kunt gebruikers die deze kenmerken niet hebben, bereiken met behulp [van filtering op basis van kenmerken](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 
* De standaard kenmerk toewijzing voor het inrichten van Sales Force bevat de SingleAppRoleAssignments-expressie voor het toewijzen van appRoleAssignments in azure AD aan profilenaam in Sales Force. Zorg ervoor dat de gebruikers niet meerdere app-roltoewijzingen in azure AD hebben omdat de kenmerk toewijzing alleen ondersteuning biedt voor het inrichten van één rol. 
* Voor Sales Force moeten e-mail updates hand matig worden goedgekeurd voordat ze worden gewijzigd. Als gevolg hiervan ziet u mogelijk meerdere vermeldingen in de inrichtings Logboeken om het e-mail bericht van de gebruiker bij te werken (totdat de wijziging in het e-mail bericht is goedgekeurd).


## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
