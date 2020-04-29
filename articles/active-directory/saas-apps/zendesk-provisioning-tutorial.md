---
title: 'Zelf studie: Zendesk configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a480119ee88521b920be88669f6d80e3754d24d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062739"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Zelf studie: Zendesk configureren voor automatische gebruikers inrichting

In deze zelf studie ziet u de stappen voor het uitvoeren van Zendesk en Azure Active Directory (Azure AD) voor het configureren van Azure AD voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en groepen in Zendesk.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie voor meer informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen [gebruikers automatisch inrichten en ongedaan maken van de inrichting van SaaS-toepassingen (Software-as-a-Service) met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u het volgende hebt:

* Een Azure AD-tenant.
* Een Zendesk-Tenant met het Professional-abonnement of is beter ingeschakeld.
* Een gebruikers account in Zendesk met beheerders machtigingen.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Zendesk toevoegen vanuit Azure Marketplace

Voordat u Zendesk configureert voor het automatisch inrichten van gebruikers met Azure AD, voegt u Zendesk van de Azure Marketplace toe aan uw lijst met beheerde SaaS-toepassingen.

Voer de volgende stappen uit om Zendesk toe te voegen vanuit de Marketplace.

1. In de [Azure Portal](https://portal.azure.com), in het navigatie deel venster aan de linkerkant, selecteert u **Azure Active Directory**.

    ![Het Azure Active Directory pictogram](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Zendesk** in het zoekvak en selecteer **Zendesk** in het deel venster resultaten. Selecteer **toevoegen**om de toepassing toe te voegen.

    ![Zendesk in de resultatenlijst](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Gebruikers toewijzen aan Zendesk

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u bepalen welke gebruikers of groepen in azure AD toegang nodig hebben tot Zendesk. Volg de instructies in [een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)om deze gebruikers of groepen toe te wijzen aan Zendesk.

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Belang rijke tips voor het toewijzen van gebruikers aan Zendesk

* Momenteel worden Zendesk-rollen automatisch en dynamisch ingevuld in de gebruikers interface van Azure Portal. Voordat u Zendesk-rollen aan gebruikers toewijst, moet u ervoor zorgen dat een initiële synchronisatie is voltooid tegen Zendesk om de nieuwste rollen in uw Zendesk-Tenant op te halen.

* We raden u aan één Azure AD-gebruiker toe te wijzen aan Zendesk om de initiële configuratie van de automatische gebruikers inrichting te testen. U kunt later aanvullende gebruikers of groepen toewijzen nadat de tests zijn voltooid.

* Wanneer u een gebruiker toewijst aan Zendesk, selecteert u een geldige toepassingsspecifieke rol, indien beschikbaar, in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Automatische gebruikers inrichting configureren voor Zendesk 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service. Gebruik dit om gebruikers of groepen in Zendesk te maken, bij te werken en uit te scha kelen op basis van gebruikers-of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt ook op SAML gebaseerde eenmalige aanmelding inschakelen voor Zendesk. Volg de instructies in de [zelf studie Zendesk single sign-on](zendesk-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Zendesk in azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer**alle toepassingen** > in **bedrijfs toepassingen** > **Zendesk**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Zendesk**.

    ![De koppeling Zendesk in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Zendesk-inrichting](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Zendesk-inrichtings modus](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Voer in het gedeelte **beheerders referenties** de gebruikers naam van de beheerder, het geheime token en het domein van uw Zendesk-account in. Voor beelden van deze waarden zijn:

   * Vul in het vak **Administrator-gebruikers naam** de gebruikers naam in van het beheerders account op uw Zendesk-Tenant. Een voorbeeld is admin@contoso.com.

   * In het vak **geheim token** vult u het geheime token in, zoals beschreven in stap 6.

   * Vul in het vak **domein** het subdomein van uw Zendesk-Tenant in. Voor een account met een Tenant-URL van `https://my-tenant.zendesk.com`is uw subdomein bijvoorbeeld **mijn Tenant**.

6. Het geheime token voor uw Zendesk-account bevindt zich in de **beheer** > -**API** > -**instellingen**. Zorg ervoor dat **token toegang** is ingesteld op **ingeschakeld**.

    ![Zendesk-beheerders instellingen](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk-geheim token](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Nadat u de vakken in stap 5 hebt ingevuld, selecteert u **verbinding testen** om te controleren of Azure AD verbinding kan maken met Zendesk. Als de verbinding mislukt, zorgt u ervoor dat uw Zendesk-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Zendesk-test verbinding](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Voer in het vak **e-mail bericht** het e-mail adres van de persoon of groep in om de inrichtings fout meldingen te ontvangen. Schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail met Zendesk-melding](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Selecteer **Opslaan**.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Zendesk**.

    ![Zendesk-gebruikers synchronisatie](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zendesk in de sectie **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Zendesk voor bijwerk bewerkingen. Selecteer **Opslaan**om de wijzigingen op te slaan.

    ![Zendesk overeenkomende gebruikers kenmerken](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Zendesk**.

    ![Synchronisatie van Zendesk-groep](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zendesk in de sectie **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in Zendesk te vergelijken voor bijwerk bewerkingen. Selecteer **Opslaan**om de wijzigingen op te slaan.

    ![Zendesk die overeenkomen met groeps kenmerken](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Als u bereik filters wilt configureren, volgt u de instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik.

15. Als u de Azure AD-inrichtings service voor Zendesk wilt inschakelen, wijzigt u de **inrichtings status** in het gedeelte **instellingen** in **op aan**.

    ![Zendesk-inrichtings status](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definieer de gebruikers of groepen die u wilt inrichten voor Zendesk. Selecteer in de sectie **instellingen** de waarden die u in het **bereik**wilt.

    ![Zendesk-bereik](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**.

    ![Zendesk opslaan](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan bij latere synchronisaties. Ze treden ongeveer elke 40 minuten in beslag, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. In het rapport worden alle acties beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Zendesk.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

* Zendesk biedt alleen ondersteuning voor het gebruik van groepen voor gebruikers met **agent** rollen. Zie de [Zendesk-documentatie](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)voor meer informatie.

* Wanneer een aangepaste rol wordt toegewezen aan een gebruiker of groep, wijst de Azure AD Automatic User Provisioning-Service ook de standaardrol **agent**toe. Er kunnen alleen agents worden toegewezen aan een aangepaste rol. Zie de [ZENDESK API-documentatie](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests)voor meer informatie. 

## <a name="additional-resources"></a>Aanvullende bronnen

* [Het inrichten van gebruikers accounts beheren voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
