---
title: 'Zelf studie: SAManage configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op SAManage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060463"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Zelf studie: SAManage configureren voor automatische gebruikers inrichting

In deze zelf studie ziet u de stappen voor het uitvoeren van SAManage en Azure Active Directory (Azure AD) voor het configureren van Azure AD voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en groepen in SAManage.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie voor meer informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen [gebruikers automatisch inrichten en ongedaan maken van de inrichting van SaaS-toepassingen (Software-as-a-Service) met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u het volgende hebt:

* Een Azure AD-tenant.
* Een [SAManage-Tenant](https://www.samanage.com/pricing/) met het Professional-pakket.
* Een gebruikers account in SAManage met beheerders machtigingen.

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de [SAManage rest-API](https://www.samanage.com/api/). Deze API is beschikbaar voor SAManage-ontwikkel aars voor accounts met het Professional-pakket.

## <a name="add-samanage-from-the-azure-marketplace"></a>SAManage toevoegen vanuit Azure Marketplace

Voordat u SAManage configureert voor het automatisch inrichten van gebruikers met Azure AD, voegt u SAManage van de Azure Marketplace toe aan uw lijst met beheerde SaaS-toepassingen.

Voer de volgende stappen uit om SAManage toe te voegen vanuit de Marketplace.

1. In de [Azure Portal](https://portal.azure.com), in het navigatie deel venster aan de linkerkant, selecteert u **Azure Active Directory**.

    ![Het Azure Active Directory pictogram](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SAManage** in het zoekvak en selecteer **SAManage** in het deel venster resultaten. Selecteer **toevoegen**om de toepassing toe te voegen.

    ![Samanage in de resultatenlijst](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Gebruikers toewijzen aan SAManage

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u bepalen welke gebruikers of groepen in azure AD toegang nodig hebben tot SAManage. Volg de instructies in [een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)om deze gebruikers of groepen toe te wijzen aan SAManage.

### <a name="important-tips-for-assigning-users-to-samanage"></a>Belang rijke tips voor het toewijzen van gebruikers aan SAManage

*    Momenteel worden SAManage-rollen automatisch en dynamisch ingevuld in de gebruikers interface van Azure Portal. Voordat u SAManage-rollen aan gebruikers toewijst, moet u ervoor zorgen dat een initiële synchronisatie is voltooid tegen SAManage om de nieuwste rollen in uw SAManage-Tenant op te halen.

*    We raden u aan één Azure AD-gebruiker toe te wijzen aan SAManage om de initiële configuratie van de automatische gebruikers inrichting te testen. U kunt later extra gebruikers en groepen toewijzen nadat de tests zijn voltooid.

*    Wanneer u een gebruiker toewijst aan SAManage, selecteert u een geldige toepassingsspecifieke rol, indien beschikbaar, in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Automatische gebruikers inrichting configureren voor SAManage

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service. Gebruik dit om gebruikers of groepen in SAManage te maken, bij te werken en uit te scha kelen op basis van gebruikers-of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt ook op SAML gebaseerde eenmalige aanmelding inschakelen voor SAManage. Volg de instructies in de [zelf studie SAManage single sign-on](samanage-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor SAManage in azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer**alle toepassingen** > in **bedrijfs toepassingen** > **SAManage**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Samanage** in de lijst met toepassingen.

    ![De koppeling SAManage in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![SAManage-inrichting](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Geef in het gedeelte **beheerders referenties** de SAManage- **Tenant-URL** en het **geheime token**op. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met SAManage. Als de verbinding mislukt, zorg er dan voor dat uw SAManage-account beheerders machtigingen heeft en probeer het opnieuw.

    ![SAManage-test verbinding](./media/samanage-provisioning-tutorial/provisioning.png)

6. Voer in het vak **e-mail bericht** het e-mail adres van de persoon of groep in om de inrichtings fout meldingen te ontvangen. Schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail met SAManage-melding](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met SAManage**.

    ![SAManage-gebruikers synchronisatie](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar SAManage in de sectie **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in SAManage voor bijwerk bewerkingen. Selecteer **Opslaan**om de wijzigingen op te slaan.

    ![SAManage overeenkomende gebruikers kenmerken](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Als u groeps toewijzingen wilt inschakelen, selecteert u in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met SAManage**.

    ![Synchronisatie van SAManage-groep](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Stel **ingeschakeld** in op **Ja** om groepen te synchroniseren. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar SAManage in de sectie **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in SAManage voor bijwerk bewerkingen. Selecteer **Opslaan**om de wijzigingen op te slaan.

    ![SAManage die overeenkomen met groeps kenmerken](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Als u bereik filters wilt configureren, volgt u de instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik.

13. Als u de Azure AD-inrichtings service voor SAManage wilt inschakelen, wijzigt u de **inrichtings status** in het gedeelte **instellingen** in **op aan**.

    ![SAManage-inrichtings status](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Definieer de gebruikers of groepen die u wilt inrichten voor SAManage. Selecteer in de sectie **instellingen** de waarden die u in het **bereik**wilt. Wanneer u de optie **alle gebruikers en groepen synchroniseren** selecteert, moet u rekening houden met de beperkingen zoals beschreven in de volgende sectie "beperkingen van connectors".

    ![SAManage-bereik](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**.

    ![SAManage opslaan](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan bij latere synchronisaties. Ze treden ongeveer elke 40 minuten in beslag, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. In het rapport worden alle acties beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op SAManage.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

Als u de optie **alle gebruikers en groepen synchroniseren** selecteert en een waarde voor het kenmerk SAManage **roles** configureert, moet de waarde onder de **standaard waarde** worden weer gegeven in de volgende notatie:

- {"displayName": "Role"}, waarbij Role de gewenste standaard waarde is.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Het inrichten van gebruikers accounts beheren voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
