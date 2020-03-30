---
title: 'Zelfstudie: Proxyclick configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen naar Proxyclick.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 95cb0371c4b2181d8f09991fe6e652c0e939f3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063353"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Zelfstudie: Proxyclick configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Proxyclick en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen naar Proxyclick.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een proxyclick-tenant](https://www.proxyclick.com/pricing)
* Een gebruikersaccount in Proxyclick met beheerdersmachtigingen.

## <a name="add-proxyclick-from-the-gallery"></a>Proxyclick toevoegen vanuit de galerie

Voordat u Proxyclick configureert voor automatische gebruikersvoorziening met Azure AD, moet u Proxyclick vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Proxyclick toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Proxyclick**in het zoekvak, selecteer **Proxyclick** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Proxyclick in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Gebruikers toewijzen aan Proxyclick

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Proxyclick nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Proxyclick door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Belangrijke tips voor het toewijzen van gebruikers aan Proxyclick

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Proxyclick wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Proxyclick toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Automatische gebruikersvoorziening configureren voor Proxyclick 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Proxyclick te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor Proxyclick, volgens de instructies in de [zelfstudie proxyclick single sign-on](proxyclick-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor Proxyclick in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Proxyclick**in de lijst met toepassingen .

    ![De koppeling Proxyclick in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Als u de **URL van de tenant** en het geheime **token** van uw Proxyclick-account wilt ophalen, volgt u de walkthrough zoals beschreven in stap 6.

6. Meld u aan bij uw [proxyclick-beheerconsole](https://app.proxyclick.com/login//?destination=%2Fdefault). Navigeer naar > **Instellingen-integraties** > **bladeren door Marketplace**. **Settings**

    ![Proxyclick-instellingen](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Proxyclick-integraties](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Selecteer **Azure AD**. Klik **op Nu installeren**.

    ![Proxyclick Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick Installeren](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Selecteer **Gebruikersinrichting** en klik op **Integratie starten**. 

    ![Proxyclick User Provisioning](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    De juiste configuratie-gebruikersinterface voor instellingen moet nu worden weergegeven onder > **Instellingen-integraties**. **Settings** Selecteer **Instellingen** onder **Azure AD (User Provisioning)**.

    ![Proxyclick Maken](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    U vindt de **URL van de tenant** en secret **token** hier.

    ![Proxyclick Token maken](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Klik op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Proxyclick wanneer u de velden in stap 5 bevolken. Als de verbinding mislukt, moet u ervoor zorgen dat uw Proxyclick-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer onder de sectie **Toewijzingen** de optie **Azure Active Directory Users synchroniseren met Proxyclick**.

    ![Proxyclick Gebruikerstoewijzingen](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Proxyclick in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Proxyclick te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Proxyclick Gebruikerskenmerken](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

14. Als u de Azure AD-inrichtingsservice voor Proxyclick wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

15. Definieer de gebruikers en/of groepen die u wilt inrichten aan Proxyclick door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

16. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het inrichten van activiteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Proxyclick.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Beperkingen voor connectoren

* Proxyclick vereist **dat e-mails** en **userName** dezelfde bronwaarde hebben. Updates voor beide kenmerken wijzigen de andere waarde.
* Proxyclick biedt geen ondersteuning voor het inrichten voor groepen.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

