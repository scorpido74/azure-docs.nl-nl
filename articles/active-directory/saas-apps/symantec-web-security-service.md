---
title: 'Zelfstudie: Symantec Web Security Service (WSS) configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Symantec Web Security Service (WSS).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: fbd105ca1623512a3c16f3b609374f5c4055898b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063115"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Zelfstudie: Symantec Web Security Service (WSS) configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Symantec Web Security Service (WSS) en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Symantec Web Security Service (WSS).

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Symantec Web Security Service (WSS) tenant](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Een gebruikersaccount in Symantec Web Security Service (WSS) met beheerdersmachtigingen.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Gebruikers toewijzen aan Symantec Web Security Service (WSS)

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Symantec Web Security Service (WSS). Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Symantec Web Security Service (WSS) door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Belangrijke tips voor het toewijzen van gebruikers aan Symantec Web Security Service (WSS)

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Symantec Web Security Service (WSS) om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker toewijs aan Symantec Web Security Service (WSS), moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Installatie Symantec Web Security Service (WSS) voor inrichten

Voordat u Symantec Web Security Service (WSS) configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-inrichting inschakelen voor Symantec Web Security Service (WSS).

1. Meld u aan bij uw [symantec websecurityservice-beheerconsole](https://portal.threatpulse.com/login.jsp). Navigeer naar **Solutions** > **Service**.

    ![Symantec Web Security Service(WSS)](media/symantec-web-security-service/service.png)

2. Navigeer naar integratie > **seinen** > voor **accountonderhoud****Nieuwe integratie**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Selecteer **gebruikers van derden & groepen synchroniseren**. 

    ![Symantec Web Security Service](media/symantec-web-security-service/third-party-users.png)

4.  Kopieer de **SCIM-URL** en **-token**. Deze waarden worden ingevoerd in het **veld URL van tenant** en geheim **token** op het tabblad Provisioning van uw Symantec Web Security Service (WSS)-toepassing in de Azure-portal.

    ![Symantec Web Security Service](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Symantec Web Security Service (WSS) toevoegen vanuit de galerie

Als u De Web Security Service (WSS) van Symantec wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u Symantec Web Security Service (WSS) vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Symantec Web Security Service (WSS) toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Symantec Web Security Service,** selecteer **Symantec Web Security Service** in het deelvenster Resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Symantec Web Security Service (WSS) toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Automatische gebruikersvoorziening configureren voor Symantec Web Security Service (WSS)

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Symantec Web Security Service (WSS) te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om saml-gebaseerde single sign-on in te schakelen voor Symantec Web Security Service (WSS), volgens de instructies in de [Symantec Web Security Service (WSS) Single sign-on tutorial](symantec-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Ga als lid van het Internet Security Service (WSS) van de gebruiker in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer Symantec Web **Security Service**in de lijst met toepassingen .

    ![De koppeling Symantec Web Security Service (WSS) in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie Beheerdersreferenties de **SCIM-URL-** en **tokenwaarden** in die eerder zijn opgehaald in respectievelijk **tenant-URL** en **Secret Token.** Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Symantec Web Security Service. Als de verbinding mislukt, moet u ervoor zorgen dat uw Symantec Web Security Service (WSS)-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Symantec Web Security Service (WSS)** onder de sectie **Toewijzingen.**

    ![WSS-gebruikerstoewijzingen (Symantec Web Security Service)](media/symantec-web-security-service/usermapping.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Symantec Web Security Service (WSS) in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Symantec Web Security Service (WSS) te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![WSS-gebruikerstoewijzingen (Symantec Web Security Service)](media/symantec-web-security-service/userattribute.png)

10. Selecteer Azure **Active Directory-groepen synchroniseren met Symantec Web Security Service**in de sectie **Toewijzingen.**

    ![WSS-gebruikerstoewijzingen (Symantec Web Security Service)](media/symantec-web-security-service/groupmapping.png)

11. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Symantec Web Security Service (WSS) in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepen in Symantec Web Security Service (WSS) voor updatebewerkingen te matchen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![WSS-gebruikerstoewijzingen (Symantec Web Security Service)](media/symantec-web-security-service/groupattribute.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Symantec Web Security Service wilt inschakelen, wijzigt u de **ininrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten bij Symantec Web Security Service (WSS) door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan latere synchronisaties. Zie [Hoe lang het duurt voordat](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)gebruikers en/of groepen worden ingericht voor meer informatie over hoe lang het duurt voordat gebruikers zijn voorzien.

U de sectie **Huidige status** gebruiken om de voortgang te volgen en koppelingen naar uw installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Symantec Web Security Service (WSS). Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie . Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
