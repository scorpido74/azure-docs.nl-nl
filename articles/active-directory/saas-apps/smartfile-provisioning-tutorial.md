---
title: 'Zelfstudie: SmartFile configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor SmartFile.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eeff992-a84f-4f88-a360-9accbd077538
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: b113cc27195b2ce954d677ab0f1ec83e394946be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060215"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Zelfstudie: SmartFile configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in SmartFile en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor SmartFile.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* [Een SmartFile-tenant.](https://www.SmartFile.com/pricing/)
* Een gebruikersaccount in SmartFile met beheerdersmachtigingen.

## <a name="assigning-users-to-smartfile"></a>Gebruikers toewijzen aan SmartFile

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot SmartFile nodig hebben. Zodra u hebt besloten, u deze gebruikers en/of groepen toewijzen aan SmartFile door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Belangrijke tips voor het toewijzen van gebruikers aan SmartFile

* Het wordt aanbevolen dat één Azure AD-gebruiker aan SmartFile wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan SmartFile toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="setup-smartfile-for-provisioning"></a>SmartFile instellen voor inrichten

Voordat u SmartFile configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-inrichting op SmartFile inschakelen en extra details verzamelen die nodig zijn.

1. Meld u aan bij uw SmartFile-beheerconsole. Navigeer naar de rechterbovenhoek van de SmartFile-beheerconsole. Selecteer **Productcode**.

    ![SmartFile-beheerconsole](media/smartfile-provisioning-tutorial/login.png)

2. Als u een token aan toonder wilt genereren, kopieert u de **productcode** en **het productwachtwoord.** Plak ze in een notitieblok met een dikke darm ertussen.
    
     ![SmartFile Add SCIM](media/smartfile-provisioning-tutorial/auth.png)

    ![SmartFile Add SCIM](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>SmartFile toevoegen vanuit de galerie

Als u SmartFile wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u SmartFile uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om SmartFile toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **SmartFile,** selecteer **SmartFile** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![SmartFile in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Automatische gebruikersvoorziening configureren voor SmartFile 

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in SmartFile te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde eenmalige aanmelding voor SmartFile in te schakelen, volgens de instructies in de [smartfile-aanmeldingszelfstudie](SmartFile-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Ga als een automatisch beheer van de gebruiker voor SmartFile in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **SmartFile**.

    ![De SmartFile-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5.  Voer onder de sectie `https://<SmartFile sitename>.smartfile.com/ftp/scim` **Beheerdersreferenties** invoer in **Tenant-URL**in . Een voorbeeld zou `https://demo1test.smartfile.com/ftp/scim`eruit zien als . Voer de **tokenwaarde aan toonder** (ProductKey:ProductPassword) in die u eerder in **Secret Token**hebt opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met SmartFile. Als de verbinding mislukt, moet u ervoor zorgen dat uw SmartFile-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met SmartFile**onder de sectie **Toewijzingen** .

    ![SmartFile-gebruikerstoewijzingen](media/smartfile-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar SmartFile in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in SmartFile te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van SmartFile-gebruikers](media/smartfile-provisioning-tutorial/userattribute.png)

10. Selecteer azure **Active Directory-groepen synchroniseren met SmartFile**onder de sectie **Toewijzingen** .

    ![SmartFile-groepstoewijzingen](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar SmartFile in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen Matching** worden gebruikt om de groepen in SmartFile te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van SmartFile-groepen](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor SmartFile wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor SmartFile door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

    Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op SmartFile.

    Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md) voor meer informatie over het lezen van de azure AD-inrichtingslogboeken.
    
## <a name="connector-limitations"></a>Verbindingsbeperkingen

* SmartFile ondersteunt alleen harde verwijderingen. 

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

 [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
