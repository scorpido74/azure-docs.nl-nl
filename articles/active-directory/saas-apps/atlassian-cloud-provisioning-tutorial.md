---
title: 'Zelfstudie: Atlassian Cloud configureren voor automatische gebruikersinrichting met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen in Atlassian Cloud.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059331"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Zelfstudie: Atlassian Cloud configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Atlassian Cloud en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Atlassian Cloud.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Atlassian Cloud-tenant](https://www.atlassian.com/licensing/cloud)
* Een gebruikersaccount in Atlassian Cloud met beheerdersmachtigingen.

> [!NOTE]
> De Azure AD-integratie is gebaseerd op de **Atlassian Cloud SCIM API**, die beschikbaar is voor Atlassian Cloud-teams.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Atlassian Cloud toevoegen vanuit de galerie

Voordat u Atlassian Cloud configureert voor automatische gebruikersvoorziening met Azure AD, moet u Atlassian Cloud vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u Atlassian Cloud wilt toevoegen vanuit de Azure AD-toepassingsgalerie, voert u de volgende stappen uit:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Atlassian Cloud**in het zoekvak, selecteer **Atlassian Cloud** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Atlassian Cloud in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Gebruikers toewijzen aan Atlassian Cloud

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Atlassian Cloud nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Atlassian Cloud door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Belangrijke tips voor het toewijzen van gebruikers aan Atlassian Cloud

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Atlassian Cloud om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker toewijst aan Atlassian Cloud, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Automatische gebruikersvoorziening configureren voor Atlassian Cloud 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Atlassian Cloud te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor Atlassian Cloud, volgens de instructies in de [atlassian Cloud single sign-on tutorial](atlassian-cloud-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersinrichting voor Atlassian Cloud in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Atlassian Cloud**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Atlassian Cloud**in de lijst met toepassingen.

    ![De koppeling naar Atlassian Cloud in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Navigeer naar [Atlassian Organization Manager](https://admin.atlassian.com) **> de map > org te selecteren.**

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Klik **op Gebruikersinrichting** en klik op **Een map maken**. Kopieer respectievelijk de URL van de **directorybasis** en **het token aan toonder** naar de **URL van de tenant** en de velden **Secret Token.**

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Voer onder de sectie **Beheerdersreferenties** de **URL van de tenant** en het geheime **token** van het account van uw Atlassian Cloud in. Voorbeelden van deze waarden zijn:

   * Vul in het veld **URL van tenant** het specifieke tenanteindpunt in dat u van de Atlassian ontvangt, zoals beschreven in stap 6. Bijvoorbeeld: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * Vul in het veld **Secret Token** het geheime token zoals beschreven in stap 6.

8. Klik op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Atlassian Cloud wanneer u de velden in stap 7 bevolken. Als de verbinding mislukt, moet u ervoor zorgen dat uw Atlassian Cloud-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Klik op **Opslaan**.

11. Selecteer Azure **Active Directory-gebruikers synchroniseren met Atlassian Cloud**onder de sectie **Toewijzingen** .

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Atlassian Cloud in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Atlassian Cloud te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. Selecteer Azure **Active Directory-groepen synchroniseren met Atlassian Cloud**onder de sectie **Toewijzingen** .

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Atlassian Cloud in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen Matching** worden gebruikt om de groepen in Atlassian Cloud te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

16. Als u de Azure AD-inrichtingsservice voor Atlassian Cloud wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Definieer de gebruikers en/of groepen die u wilt inrichten in Atlassian Cloud door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Atlassian Cloud.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Beperkingen voor connectoren

* Atlassian Cloud staat het mogelijk om gebruikers alleen vanuit [geverifieerde domeinen te voorzien.](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)
* Atlassian Cloud ondersteunt vandaag de dag geen groepsnamen. Dit betekent dat wijzigingen in de weergavenaam van een groep in Azure AD niet worden bijgewerkt en weergegeven in Atlassian Cloud.
* De waarde van het **kenmerk e-mailgebruiker** in Azure AD wordt alleen ingevuld als de gebruiker een Microsoft Exchange-postvak heeft. Als de gebruiker er geen heeft, wordt het aanbevolen om een ander gewenst kenmerk toe te schrijven aan het **e-mailskenmerk** in Atlassian Cloud.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png