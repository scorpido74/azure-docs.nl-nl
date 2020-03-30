---
title: 'Zelfstudie: IPass SmartConnect configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor iPass SmartConnect.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: fb3a5d03f390b88f9856f03bdc97a35b845874ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057518"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Zelfstudie: IPass SmartConnect configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in iPass SmartConnect en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen naar iPass SmartConnect.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* [Een iPass SmartConnect-tenant](https://www.ipass.com/buy-ipass/).
* Een gebruikersaccount in iPass SmartConnect met beheerdersmachtigingen.

## <a name="assigning-users-to-ipass-smartconnect"></a>Gebruikers toewijzen aan iPass SmartConnect

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot iPass SmartConnect nodig hebben. Zodra u hebt besloten, u deze gebruikers en/of groepen toewijzen aan iPass SmartConnect door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Belangrijke tips voor het toewijzen van gebruikers aan iPass SmartConnect

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan iPass SmartConnect om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan iPass SmartConnect toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>IPass SmartConnect instellen voor inrichten

Voordat u iPass SmartConnect configureert voor automatische gebruikersvoorziening met Azure AD, moet u configuratiegegevens ophalen van de iPass SmartConnect-beheerconsole:

1. Als u het token aan de drager wilt ophalen dat nodig is om te verifiëren tegen uw iPass SmartConnect SCIM-eindpunt, verwijst u naar de allereerste keer dat u iPass SmartConnect instelt, omdat deze waarde alleen dan wordt opgegeven. 
2. Als je het token aan de drager niet hebt, neem dan contact op met [het ondersteuningsteam van iPass SmartConnect](mailto:help@ipass.com) om een nieuw token op te halen.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>IPass SmartConnect toevoegen vanuit de galerie

Als u iPass SmartConnect wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u iPass SmartConnect vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om iPass SmartConnect toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **iPass SmartConnect**in het zoekvak, selecteer **iPass SmartConnect** in het deelvenster Resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![iPass SmartConnect in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Automatische gebruikersvoorziening configureren voor iPass SmartConnect 

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in iPass SmartConnect te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
>  U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor BitaBIZ, volgens de instructies in de [iPass SmartConnect Single sign-on tutorial](ipasssmartconnect-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersinrichting voor iPass SmartConnect in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **iPass SmartConnect**in de lijst met toepassingen .

    ![De iPass SmartConnect-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://openmobile.ipass.com/moservices/scim/v1` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer het token aan toonder in dat eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met iPass SmartConnect. Als de verbinding mislukt, moet u ervoor zorgen dat uw iPass SmartConnect-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met iPass SmartConnect**onder de sectie **Toewijzingen** .

    ![IPass SmartConnect-gebruikerstoewijzingen](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar iPass SmartConnect in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de gebruikersaccounts in iPass SmartConnect te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![IPass SmartConnect-gebruikerstoewijzingen](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

11. Als u de Azure AD-inrichtingsservice voor iPass SmartConnect wilt inschakelen, wijzigt u de **ininrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten op iPass SmartConnect door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op iPass SmartConnect.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

* iPass SmartConnect accepteert alleen gebruikersnamen waarop hun domeinen zijn geregistreerd in de iPass SmartConnect-beheerconsole.  

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
