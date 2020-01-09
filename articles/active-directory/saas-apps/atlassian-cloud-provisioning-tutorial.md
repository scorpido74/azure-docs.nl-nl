---
title: 'Zelf studie: atlassian Cloud configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts in Atlassian Cloud.
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
ms.openlocfilehash: 604dca2861b7a7126d2e37b5a01bcb85c530546e
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561414"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Zelf studie: atlassian-Cloud configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is om te demonstreren welke stappen moeten worden uitgevoerd in Atlassian Cloud en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in Atlassian Cloud.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een Atlassian-Cloud Tenant](https://www.atlassian.com/licensing/cloud)
* Een gebruikers account in Atlassian Cloud met beheerders machtigingen.

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de **Atlassian Cloud scim-API**, die beschikbaar is voor Atlassian Cloud teams.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Een Atlassian-Cloud toevoegen vanuit de galerie

Voordat u de Atlassian-Cloud configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Atlassian cloud van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Atlassian-Cloud toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Atlassian Cloud**in, selecteer **Atlassian Cloud** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Atlassian Cloud in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Gebruikers toewijzen aan Atlassian Cloud

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot de Atlassian-Cloud. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan de Atlassian-Cloud door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Belang rijke tips voor het toewijzen van gebruikers aan Atlassian Cloud

* Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan de Atlassian-Cloud om de configuratie voor automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Atlassian-Cloud, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Automatische gebruikers inrichting configureren voor Atlassian-Cloud 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Atlassian Cloud te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te scha kelen voor Atlassian Cloud, gevolgd door de instructies in de [Atlassian-zelf studie voor eenmalige aanmelding](atlassian-cloud-tutorial.md)in de Cloud. Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Atlassian Cloud in azure AD:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Atlassian Cloud**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Atlassian Cloud**.

    ![De koppeling naar Atlassian Cloud in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Atlassian Cloud inrichting](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Atlassian Cloud inrichting](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Navigeer naar [Atlassian Organization Manager](https://admin.atlassian.com) **> Selecteer de map org >** .

    ![Atlassian Cloud inrichting](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Klik op **Gebruikers inrichten** en klik op **een map maken**. Kopieer de **Directory basis-URL** en **Bearer-token** naar respectievelijk de velden Tenant- **URL** en **geheime token** .

    ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Atlassian Cloud Provisioning](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Voer de URL van de **Tenant** en het **geheime token** van het account van uw Atlassian-Cloud in het gedeelte **beheerders referenties** in. Voor beelden van deze waarden zijn:

   * In het veld **Tenant-URL** vult u het specifieke Tenant eindpunt dat u ontvangt van de Atlassian, zoals beschreven in stap 6. Bijvoorbeeld: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * In het veld **geheim token** vult u het geheime token in, zoals beschreven in stap 6.

8. Klik bij het invullen van de velden die worden weer gegeven in stap 7 op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Atlassian Cloud. Als de verbinding mislukt, zorg er dan voor dat uw Atlassian-Cloud account beheerders machtigingen heeft en probeer het opnieuw.

    ![Atlassian Cloud inrichting](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![Atlassian Cloud inrichting](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Klik op **Opslaan**.

11. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Atlassian Cloud**.

    ![Atlassian Cloud inrichting](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Atlassian Cloud in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in de Atlassian-Cloud voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Atlassian Cloud inrichting](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren naar Atlassian Cloud**.

    ![Atlassian Cloud inrichting](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Atlassian Cloud in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de groepen in de Atlassian-Cloud te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Atlassian Cloud inrichting](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

16. Als u de Azure AD Provisioning Service voor Atlassian **Cloud wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Atlassian Cloud inrichting](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Definieer de gebruikers en/of groepen die u wilt inrichten voor Atlassian Cloud door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Atlassian Cloud inrichting](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Atlassian Cloud inrichting](./media/atlassian-cloud-provisioning-tutorial/save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. Hiermee worden alle acties beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Atlassian Cloud.

Zie voor meer informatie over het lezen van de Azure AD inrichting logboeken [rapportage over het inrichten van automatische gebruikersaccounts](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector beperkingen

* Met Atlassian Cloud kunnen gebruikers alleen worden ingericht vanuit [geverifieerde domeinen](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud biedt momenteel geen ondersteuning voor de naam van een groep. Dit betekent dat wijzigingen in de displayName van een groep in azure AD niet worden bijgewerkt en niet worden weer gegeven in de Atlassian-Cloud.
* De waarde van het kenmerk **e-mail** gebruiker in azure AD wordt alleen ingevuld als de gebruiker een micro soft Exchange-postvak heeft. Als de gebruiker niet beschikt over één, wordt aanbevolen om een ander gewenst kenmerk toe te wijzen aan het kenmerk **emails** in Atlassian Cloud.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png