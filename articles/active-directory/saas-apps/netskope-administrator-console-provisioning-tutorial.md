---
title: 'Zelfstudie: Netskope Administrator Console configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Netskope Administrator Console.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061242"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Zelfstudie: Netskope Administrator Console configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Netskope Administrator Console en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Netskope Administrator Console.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een netskope Administrator Console-tenant](https://www.netskope.com/)
* Een gebruikersaccount in netskope Administrator Console met beheerdersmachtigingen.

## <a name="assigning-users-to-netskope-administrator-console"></a>Gebruikers toewijzen aan Netskope Administrator Console

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Netskope Administrator Console. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Netskope Administrator Console door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Belangrijke tips voor het toewijzen van gebruikers aan Netskope Administrator Console

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Netskope Administrator Console om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker toewijst aan Netskope Administrator Console, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Netskope Administrator Console instellen voor inrichting

1. Meld u aan bij de [beheerder console van de Netskope Administrator Console](https://netskope.goskope.com/). Navigeer naar **>-instellingen voor thuisgebruik**.

    ![Beheerder console van beheerder netskope](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navigeer naar **Extra.** Navigeer onder het menu **Extra** naar **Directory Tools > SCIM-INTEGRATIE**.

    ![Netskope Administrator Console-hulpprogramma's](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope Administrator Console Add SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Scroll naar beneden en klik op De knop **Token toevoegen.** Klik in het dialoogvenster **OAuth-clientnaam toevoegen** een **klantnaam** op en klik op de knop **Opslaan.**

    ![Netskope Administrator Console Token toevoegen](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope Administrator Console CLient Naam](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Kopieer de URL van de **SCIM-server** en het **TOKEN**. Deze waarden worden respectievelijk ingevoerd in de velden Tenant-URL en Secret Token op het tabblad Provisioning van uw Netskope Administrator Console-toepassing in de Azure-portal.

    ![Netskope Administrator Console Token maken](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Netskope Administrator Console toevoegen vanuit de galerie

Voordat u Netskope Administrator Console configureert voor automatische gebruikersvoorziening met Azure AD, moet u Netskope Administrator Console vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Netskope Administrator Console toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Netskope Administrator Console,** selecteer **Netskope Administrator Console** in het deelvenster Resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Netskope Administrator Console in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Automatische gebruikersvoorziening configureren voor Netskope Administrator Console 

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Netskope Administrator Console te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde aanmelding voor Netskope Administrator Console in te schakelen door de instructies te volgen die zijn gegeven in de [zelfstudie netskope Administrator Console Single sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

> [!NOTE]
> Raadpleeg [dit](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)voor meer informatie over het SCIM-eindpunt van Netskope Administrator Console.

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor Netskope Administrator Console in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **netskope Administrator Console**in de lijst met toepassingen .

    ![De koppeling Netskope Administrator Console in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** de **URL-waarde van de SCIM-server** in die eerder in **tenant-URL**is opgehaald . Voer de **tokenwaarde** in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Netskope Administrator Console. Als de verbinding mislukt, moet u ervoor zorgen dat uw Netskope Administrator Console-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer azure **Active Directory-gebruikers synchroniseren met Netskope Administrator Console**onder de sectie **Toewijzingen** .

    ![Gebruikerstoewijzingen van netskope-beheerdersconsole](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Netskope Administrator Console in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in netskope Administrator Console te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Gebruikerskenmerken van de Beheerder console van Netskope](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Selecteer onder de sectie **Toewijzingen** de optie **Azure Active Directory Groups synchroniseren met Netskope Administrator Console**.

    ![Groepstoewijzingen van netskope-beheerders](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Netskope Administrator Console in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepen in netskope Administrator Console te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van netskope-beheerdersconsole](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Netskope Administrator Console wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten op netskope administratorconsole door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op de Netskope Administrator Console.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

