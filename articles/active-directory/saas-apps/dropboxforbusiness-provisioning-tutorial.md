---
title: 'Zelfstudie: Dropbox voor Bedrijven configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen in Dropbox voor Bedrijven.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3acc2c271e590bddb13aaa01498f404da4340036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058395"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Zelfstudie: Dropbox voor Bedrijven configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om aan te tonen welke stappen moeten worden uitgevoerd in Dropbox for Business en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen naar Dropbox voor Bedrijven.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Dropbox voor Bedrijven-tenant](https://www.dropbox.com/business/pricing)
* Een gebruikersaccount in Dropbox voor Bedrijven met beheerdersmachtigingen.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Dropbox voor Bedrijven toevoegen vanuit de galerij

Voordat u Dropbox voor Bedrijven configureert voor automatische gebruikersvoorziening met Azure AD, moet u Dropbox voor Bedrijven vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Dropbox voor Bedrijven toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ Dropbox voor **Bedrijven**in het zoekvak, selecteer **Dropbox voor Bedrijven** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Dropbox voor Bedrijven in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Gebruikers toewijzen aan Dropbox voor Bedrijven

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Dropbox voor Bedrijven nodig hebben. Zodra je hebt besloten, kun je deze gebruikers en/of groepen toewijzen aan Dropbox voor Bedrijven door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Belangrijke tips voor het toewijzen van gebruikers aan Dropbox voor Bedrijven

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Dropbox voor Bedrijven om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer je een gebruiker aan Dropbox voor Bedrijven toewijst, moet je een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Automatische gebruikersvoorziening configureren voor Dropbox voor Bedrijven 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Dropbox voor Bedrijven te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> Je er ook voor kiezen om saml-gebaseerde enkele aanmelding voor Dropbox voor Bedrijven in te schakelen, volgens de instructies in de [zelfstudie voor eenmalige aanmelding van Dropbox voor Bedrijven.](dropboxforbusiness-tutorial.md) Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor Dropbox voor Bedrijven in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer Dropbox voor **Bedrijven**in de lijst met toepassingen .

    ![De koppeling Dropbox voor Bedrijven in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Klik onder de sectie **Beheerdersreferenties** op **Autoriseren**. Het opent een inlogdialoogvenster dropbox voor bedrijven in een nieuw browservenster.

    ![Inrichten ](common/provisioning-oauth.png)

6. Meld u in het dialoogvenster Aanmelden bij Dropbox voor Bedrijven om te koppelen aan het dialoogvenster Azure AD, meld u aan bij uw Dropbox voor **Bedrijven-tenant** en verifieer t.a.v. je identiteit.

    ![Aanmelden voor Dropbox voor Bedrijven](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Klik op **Testverbinding** om ervoor te zorgen dat Azure AD verbinding kan maken met Dropbox voor Bedrijven. Als de verbinding mislukt, moet u ervoor zorgen dat je Dropbox voor Bedrijven-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Token](common/provisioning-testconnection-oauth.png)

8. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer Azure **Active Directory-gebruikers synchroniseren met Dropbox**onder de sectie **Toewijzingen** .

    ![Dropbox-gebruikerstoewijzingen](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Dropbox in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Dropbox te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van Dropbox-gebruikers](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Selecteer Azure **Active Directory-groepen synchroniseren met Dropbox**onder de sectie **Toewijzingen** .

    ![Toewijzingen van Dropbox-groepen](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Dropbox in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in Dropbox te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van Dropbox-groepen](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

15. Als u de Azure AD-inrichtingsservice voor Dropbox wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

16. Definieer de gebruikers en/of groepen die je aan Dropbox wilt inrichten door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

17. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Dropbox.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Beperkingen voor connectoren
 
* Dropbox biedt geen ondersteuning voor het opschorten van uitgenodigde gebruikers. Als een uitgenodigde gebruiker wordt geschorst, wordt die gebruiker verwijderd.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

