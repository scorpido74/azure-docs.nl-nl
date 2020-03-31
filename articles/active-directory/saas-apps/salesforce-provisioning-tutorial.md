---
title: 'Zelfstudie: Salesforce configureren voor automatische gebruikersvoorziening met Azure Active Directory| Microsoft Documenten'
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
ms.openlocfilehash: 5b8038896a11b65e835ce71f5fc34e85723cc91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060518"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Zelfstudie: Salesforce configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen weer te geven die nodig zijn om uit te voeren in Salesforce en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar Salesforce.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

* Een Azure Active directory tenant
* Een Salesforce.com huurder

> [!IMPORTANT]
> Als u een Salesforce.com proefaccount gebruikt, u de geautomatiseerde gebruikersinrichting niet configureren. Proefaccounts hebben pas de benodigde API-toegang ingeschakeld nadat ze zijn gekocht. U rond deze beperking door een gratis [ontwikkelaarsaccount](https://developer.salesforce.com/signup) te gebruiken om deze zelfstudie te voltooien.

Als u een Salesforce Sandbox-omgeving gebruikt, raadpleegt u de [zelfstudie voor Salesforce Sandbox-integratie](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Gebruikers toewijzen aan Salesforce

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers of groepen in Azure AD toegang tot uw Salesforce-app nodig hebben. Nadat u deze beslissing hebt genomen, u deze gebruikers toewijzen aan uw Salesforce-app door de instructies te volgen in [Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Belangrijke tips voor het toewijzen van gebruikers aan Salesforce

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Salesforce wordt toegewezen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Salesforce toewijst, moet u een geldige gebruikersrol selecteren. De rol 'Standaardtoegang' werkt niet voor het inrichten

    > [!NOTE]
    > Met deze app worden profielen van Salesforce geïmporteerd als onderdeel van het inrichtingsproces dat de klant mogelijk wil selecteren bij het toewijzen van gebruikers in Azure AD. Houd er rekening mee dat de profielen die worden geïmporteerd uit Salesforce worden weergegeven als Rollen in Azure AD.

## <a name="enable-automated-user-provisioning"></a>Geautomatiseerde gebruikersinrichting inschakelen

In deze sectie u uw Azure AD verbinden met [de API voor het inrichten van salesforce- v40-](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)en de inrichtingsservice configureren om toegewezen gebruikersaccounts in Salesforce te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzing in Azure AD.

> [!Tip]
> U er ook voor kiezen om SAML-gebaseerde single sign-On voor Salesforce in te schakelen, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="configure-automatic-user-account-provisioning"></a>Automatische gebruikersaccountinrichting configureren

Het doel van deze sectie is om te schetsen hoe u gebruikersvoorzieningen voor Active Directory-gebruikersaccounts naar Salesforce inschakelen.

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

2. Als u Salesforce al hebt geconfigureerd voor één aanmelding, zoekt u naar uw instantie van Salesforce via het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **Salesforce** in de toepassingsgalerie. Selecteer Salesforce in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

3. Selecteer uw exemplaar van Salesforce en selecteer vervolgens het tabblad **Inrichten.**

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Provisioning](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Geef onder de sectie **Beheerdersreferenties** de volgende configuratie-instellingen op:

    a. Typ in het tekstvak **Beheerdersgebruikersnaam** een Salesforce-accountnaam waarop het **systeembeheerdersprofiel** in Salesforce.com is toegewezen.

    b. Typ het wachtwoord voor dit account in het tekstvak **Beheerderswachtwoord.**

6. Als u uw Salesforce-beveiligingstoken wilt openen, opent u een nieuw tabblad en meldt u zich aan bij hetzelfde Salesforce-beheerdersaccount. Klik in de rechterbovenhoek van de pagina op uw naam en klik vervolgens op **Instellingen**.

    ![Automatische gebruikersvoorziening inschakelen](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Automatische gebruikersvoorziening inschakelen")

7. Klik in het linkernavigatiedeelvenster op **Mijn persoonlijke gegevens** om de gerelateerde sectie uit te vouwen en klik vervolgens op Mijn **beveiligingstoken opnieuw instellen**.
  
    ![Automatische gebruikersvoorziening inschakelen](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Automatische gebruikersvoorziening inschakelen")

8. Klik op de pagina **Beveiligingstoken opnieuw instellen** op de knop **Beveiligingstoken opnieuw instellen.**

    ![Automatische gebruikersvoorziening inschakelen](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Automatische gebruikersvoorziening inschakelen")

9. Schakel het e-mailpostvak in dat aan dit beheerdersaccount is gekoppeld. Zoek naar een e-mail van Salesforce.com die het nieuwe beveiligingstoken bevat.

10. Kopieer het token, ga naar het Azure AD-venster en plak het in het veld **Secret Token.**

11. De **URL van de tenant** moet worden ingevoerd als de instantie van Salesforce zich in de Salesforce Government Cloud bevindt. Anders is het optioneel. Voer de tenant-URL in\<met de\>indeling 'https:// \<uw\> instantie .my.salesforce.com' en vervang uw exemplaar door de naam van uw Salesforce-exemplaar.

12. Klik in de Azure-portal op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Salesforce-app.

13. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje hieronder in.

14. Klik **op Opslaan.**  

15. Selecteer Azure **Active Directory-gebruikers synchroniseren met Salesforce** onder de sectie Toewijzingen.

16. Controleer in de sectie **Toewijzingen van kenmerken** de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Salesforce. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Salesforce te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

17. Als u de Azure AD-inrichtingsservice voor Salesforce wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie Instellingen

18. Klik **op Opslaan.**

> [!NOTE]
> Zodra de gebruikers zijn ingericht in de Salesforce-toepassing, moet de beheerder de taalspecifieke instellingen voor hen configureren. Zie [dit](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) artikel voor meer informatie over taalconfiguratie.

Hiermee wordt de eerste synchronisatie gestart van gebruikers en/of groepen die zijn toegewezen aan Salesforce in de sectie Gebruikers en groepen. Houd er rekening mee dat de eerste synchronisatie langer duurt dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te volgen en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtingsservice in uw Salesforce-app.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="common-issues"></a>Algemene problemen
* Als u problemen ondervindt bij het verlenen van toegang tot Salesforce, moet u het volgende garanderen:
    * De gebruikte referenties hebben beheerderstoegang tot Salesforce.
    * De versie van Salesforce die u gebruikt, ondersteunt Web Access (bijvoorbeeld Ontwikkelaars, Enterprise, Sandbox en Unlimited-edities van Salesforce.)
    * Web API-toegang is ingeschakeld voor de gebruiker.
* De Azure AD-inrichtingsservice ondersteunt het inrichten van taal, landtaal en tijdzone voor een gebruiker. Deze kenmerken bevinden zich in de standaardtoewijzingen van kenmerken, maar hebben geen standaardbronkenmerk. Controleer of u het standaardbronkenmerk selecteert en dat het bronkenmerk zich bevindt in de indeling die door SalesForce wordt verwacht. LocaleSidKey voor het Engels (Verenigde Staten) is bijvoorbeeld en_US. Bekijk de richtlijnen [die hier](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) worden verstrekt om het juiste localeSidKey-formaat te bepalen. De languageLocaleKey formaten zijn [hier](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5)te vinden. Naast het feit dat de indeling correct is, moet u er mogelijk ook voor zorgen dat de taal is ingeschakeld voor uw gebruikers, zoals [hier](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)beschreven. 
* **SalesforceLicenseLimitoverschreed:** De gebruiker kan niet worden gemaakt in de doeltoepassing omdat er geen beschikbare licenties voor deze gebruiker zijn. U extra licenties aanschaffen voor de doeltoepassing of uw gebruikerstoewijzingen en toewijzingsconfiguratie controleren om ervoor te zorgen dat de juiste gebruikers de juiste kenmerken hebben toegewezen.
* **SalesforceDuplicateUserName:** De gebruiker kan niet worden ingericht omdat deze een Salesforce.com 'Gebruikersnaam' heeft die wordt gedupliceerd in een andere Salesforce.com tenant.In Salesforce.com moeten waarden voor het kenmerk 'Gebruikersnaam' uniek zijn voor alle Salesforce.com huurders.Standaard wordt de userPrincipalName van een gebruiker in Azure Active Directory hun 'Gebruikersnaam' in Salesforce.com.U hebt twee opties.Een optie is om de gebruiker te vinden en de naam van de gebruiker met de dubbele 'Gebruikersnaam' in de andere Salesforce.com tenant, als u het beheer van die andere huurder ook.De andere optie is om de toegang van de Azure Active Directory-gebruiker te verwijderen naar de Salesforce.com tenant waarmee uw directory is geïntegreerd. We zullen deze bewerking opnieuw proberen bij de volgende synchronisatiepoging. 
* **Salesforcerequiredfieldmissing:** Salesforce vereist dat bepaalde kenmerken aanwezig zijn op de gebruiker om de gebruiker te maken of bij te werken. Deze gebruiker mist een van de vereiste kenmerken. Zorg ervoor dat kenmerken zoals e-mail en alias worden ingevuld op alle gebruikers die u in Salesforce wilt hebben. U gebruikers die deze kenmerken niet hebben, uitgebruiken met [op kenmerken gebaseerde scopingfilters.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 
* De standaardtoewijzing voor kenmerktoewijzing voor het inrichten van Salesforce omvat de expressie SingleAppRoleAssignments om appRoleAssignments in Azure AD toe te stellen aan Profielnaam in Salesforce. Controleer of de gebruikers niet meerdere app-roltoewijzingen in Azure AD hebben, omdat de kenmerktoewijzing slechts het inrichten van één rol ondersteunt. 
* Salesforce vereist dat e-mailupdates handmatig worden goedgekeurd voordat ze worden gewijzigd. Als gevolg hiervan ziet u mogelijk meerdere vermeldingen in de inrichtingslogboeken om de e-mail van de gebruiker bij te werken (totdat de e-mailwijziging is goedgekeurd).


## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](tutorial-list.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eén aanmelding configureren](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
