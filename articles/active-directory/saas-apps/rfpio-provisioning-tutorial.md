---
title: 'Zelfstudie: RFPIO configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor RFPIO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 54419db4-47d5-4fb4-ab74-7b0b28afb11b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 6ae423305b39c1335b5db1cd893d5f817be1929b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060861"
---
# <a name="tutorial-configure-rfpio-for-automatic-user-provisioning"></a>Zelfstudie: RFPIO configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in RFPIO en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen aan RFPIO.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* [Een RFPIO-tenant.](https://www.rfpio.com/product/)
* Een gebruikersaccount in RFPIO met beheerdersmachtigingen.

## <a name="assigning-users-to-rfpio"></a>Gebruikers toewijzen aan RFPIO

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot RFPIO nodig hebben. Eenmaal besloten, u deze gebruikers en/ of groepen toewijzen aan RFPIO door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rfpio"></a>Belangrijke tips voor het toewijzen van gebruikers aan RFPIO

* Het wordt aanbevolen dat één Azure AD-gebruiker aan RFPIO wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan RFPIO toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="setup-rfpio-for-provisioning"></a>Setup RFPIO voor provisioning

Voordat u RFPIO configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-inrichting op RFPIO inschakelen.

1.  Meld u aan bij uw RFPIO-beheerconsole. Klik linksonder in de beheerdersconsole op **Tenant**.

    ![RFPIO-beheerconsole](media/rfpio-provisioning-tutorial/aadtest0.png)

2.  Klik **op Organisatie-instellingen**.
    
    ![RFPIO-beheerder](media/rfpio-provisioning-tutorial/aadtest.png)

3.  Navigeer naar **User MANAGEMENT** > **SECURITY** > **SCIM**.

    ![RFPIO Add SCIM](media/rfpio-provisioning-tutorial/scim.png)

4.  Controleer of **Auto User Provisioning** is ingeschakeld. Klik op **Generate SCIM API TOKEN**.

    ![RFPIO-token maken](media/rfpio-provisioning-tutorial/generate.png)

5.  Sla het **SCIM API-token op,** omdat dit token niet opnieuw wordt weergegeven voor beveiligingsdoeleinden. Deze waarde wordt ingevoerd in het veld **Secret Token** op het tabblad Provisioning van uw RFPIO-toepassing in de Azure-portal.

    ![RFPIO-token maken](media/rfpio-provisioning-tutorial/auth.png)

## <a name="add-rfpio-from-the-gallery"></a>RFPIO toevoegen vanuit de galerie

Als u RFPIO wilt configureren voor automatische gebruikersinrichting met Azure AD, moet u RFPIO uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u RFPIO wilt toevoegen vanuit de Azure AD-toepassingsgalerie, voert u de volgende stappen uit:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **RFPIO**in het zoekvak, selecteer **RFPIO** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![RFPIO in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rfpio"></a>Automatische gebruikersvoorziening configureren voor RFPIO 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in RFPIO te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor RFPIO, volgens de instructies in de [RFPIO Single sign-on tutorial](rfpio-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-rfpio-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor RFPIO in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **RFPIO**in de lijst met toepassingen .

    ![De RFPIO-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://<RFPIO tenant instance>.rfpio.com/rfpserver/scim/v2 ` **Beheerdersreferenties** invoer in **Tenant-URL**in . Een voorbeeldwaarde `https://Azure-test1.rfpio.com/rfpserver/scim/v2`is . Voer de **scim-API-tokenwaarde** in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met RFPIO. Als de verbinding mislukt, moet u ervoor zorgen dat uw RFPIO-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met RFPIO**in de sectie **Toewijzingen.**

    ![RFPIO-gebruikerstoewijzingen](media/rfpio-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar RFPIO in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in RFPIO te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![RFPIO-gebruikerskenmerken](media/rfpio-provisioning-tutorial/userattributes.png)

10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

11. Als u de Azure AD-inrichtingsservice voor RFPIO wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u aan RFPIO wilt inrichten door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op RFPIO.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Beperkingen voor connectoren

* RFPIO ondersteunt momenteel geen groepen die inrichten.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
