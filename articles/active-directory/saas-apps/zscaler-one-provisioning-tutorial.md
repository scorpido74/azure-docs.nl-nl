---
title: 'Zelfstudie: Zscaler One configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te deprovisioneren in Zscaler One.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064169"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Zelfstudie: Zscaler One configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen weergegeven die moeten worden uitgevoerd in Zscaler One en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en groepen automatisch in te richten en te deprovisioneren voor Zscaler One.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD-service voor het inrichten van gebruikers. Zie [Gebruikersinrichting en deprovisioning voor software-as-a-service -toepassingen (SaaS) automatiseren met Azure Active Directory](../active-directory-saas-app-provisioning.md)voor informatie over wat deze service doet, hoe deze werkt en veelgestelde vragen.


## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u:

* Een Azure AD-tenant.
* Een Zscaler One huurder.
* Een gebruikersaccount in Zscaler One met beheerdersmachtigingen.

> [!NOTE]
> De Azure AD-integratie is gebaseerd op de Zscaler One SCIM-API. Deze API is beschikbaar voor Zscaler One-ontwikkelaars voor accounts met het Enterprise-pakket.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Zscaler One toevoegen vanuit de Azure Marketplace

Voordat u Zscaler One configureert voor automatische gebruikersvoorziening met Azure AD, voegt u Zscaler One van de Azure Marketplace toe aan uw lijst met beheerde SaaS-toepassingen.

Voer deze stappen uit om Zscaler One van de Marketplace toe te voegen.

1. Selecteer Azure **Active Directory**in de [Azure-portal](https://portal.azure.com)in het navigatiedeelvenster aan de linkerkant .

    ![Het Azure Active Directory-pictogram](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Zscaler One** in en selecteer **Zscaler One** in het resultaatpaneel. Als u de toepassing wilt toevoegen, selecteert u **Toevoegen**.

    ![Zscaler One in de resultatenlijst](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Gebruikers toewijzen aan Zscaler One

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersinrichting worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, bepaalt u welke gebruikers of groepen in Azure AD toegang tot Zscaler One nodig hebben. Als u deze gebruikers of groepen wilt toewijzen aan Zscaler One, volgt u de instructies in [Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Belangrijke tips voor het toewijzen van gebruikers aan Zscaler One

* We raden u aan één Azure AD-gebruiker aan Zscaler One toe te wijzen om de automatische configuratie van gebruikersinrichting te testen. U later extra gebruikers of groepen toewijzen.

* Wanneer u een gebruiker aan Zscaler One toewijst, selecteert u, indien beschikbaar, een geldige toepassingsspecifieke rol in het dialoogvenster Toewijzing. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Automatische gebruikersinrichting configureren voor Zscaler One

In deze sectie u de stappen doorlopen om de Azure AD-inrichtingsservice te configureren. Gebruik het om gebruikers of groepen te maken, bij te werken en uit te schakelen in Zscaler One op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> U saml-gebaseerde eenmalige aanmelding voor Zscaler One ook inschakelen. Volg de instructies in de [Zscaler One single sign-on tutorial](zscaler-One-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Zscaler One in Azure AD

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen** > **Alle toepassingen** > **Zscaler One**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zscaler One**in de lijst met toepassingen .

    ![De Zscaler One-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Zscaler One Provisioning](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Zscaler One Provisioning Mode](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Vul onder de sectie **Beheerdersreferenties** de **vakken Tenant-URL** en **Secret Token** in met de instellingen voor uw Zscaler One-account zoals beschreven in stap 6.

6. Als u de URL van de tenant en het geheime token wilt verkrijgen, gaat u naar**Verificatieinstellingen** **voor beheer** > in de gebruikersinterface van de Zscaler One-portal. Selecteer **SAML** **onder Verificatietype**.

    ![Zscaler One-verificatie-instellingen](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selecteer **SAML configureren** om de **SAML-opties configureren** te openen.

    ![Zscaler One SAML configureren](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Selecteer **SCIM-gebaseerde inrichting inschakelen** om de instellingen in **base-URL** en **dragertoken te**krijgen. Sla vervolgens de instellingen op. Kopieer de **basis-URL-instelling** naar **tenant-URL** in de Azure-portal. Kopieer de instelling **Token aan toonder** naar **Secret Token** in de Azure-portal.

7. Nadat u de vakken in hebt ingevuld die in stap 5 worden weergegeven, selecteert u **Testverbinding** om ervoor te zorgen dat Azure AD verbinding kan maken met Zscaler One. Als de verbinding mislukt, controleert u of uw Zscaler One-account beheerdersmachtigingen heeft en probeert u het opnieuw.

    ![Zscaler-testverbinding](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Voer in het vak **Berichte-mail** het e-mailadres van de persoon of groep in om de meldingen van provisioning-fouten te ontvangen. Schakel het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt** in.

    ![Zscaler One Notification E-mail](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selecteer **Opslaan**.

10. Selecteer Azure **Active Directory-gebruikers synchroniseren met Zscaler One**onder de sectie **Toewijzingen** .

    ![Zscaler One-gebruikerssynchronisatie](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Zscaler One in de sectie **Toewijzingen van kenmerken.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Zscaler One te matchen voor updatebewerkingen. Als u wijzigingen wilt opslaan, selecteert u **Opslaan**.

    ![Zscaler One-overeenkomende gebruikerskenmerken](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Selecteer Azure **Active Directory-groepen synchroniseren met Zscaler One**onder de sectie **Toewijzingen** .

    ![Zscaler One-groepssynchronisatie](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Zscaler One in de sectie **Toewijzingen van kenmerken.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in Zscaler One te matchen voor updatebewerkingen. Als u wijzigingen wilt opslaan, selecteert u **Opslaan**.

    ![Zscaler One-overeenkomende groepskenmerken](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Als u scopingfilters wilt configureren, volgt u de instructies in de zelfstudie van het [scopingfilter.](./../active-directory-saas-scoping-filters.md)

15. Als u de Azure AD-inrichtingsservice voor Zscaler One wilt inschakelen, wijzigt u in de sectie **Instellingen** **de inrichtingsstatus** in **Aan**.

    ![Zscaler One Provisioning Status](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definieer de gebruikers of groepen die u wilt inrichten in Zscaler One. Selecteer **in** de sectie Instellingen de gewenste waarden in **Bereik**.

    ![Zscaler One Scope](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Wanneer u klaar bent voor inlevering, selecteert u **Opslaan**.

    ![Zscaler One Opslaan](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan later synchroniseert. Ze vinden ongeveer elke 40 minuten plaats zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het rapportagerapport te volgen. In het rapport worden alle acties beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Zscaler One.

Zie [Rapportage over automatische gebruikersaccountinrichting](../active-directory-saas-provisioning-reporting.md)voor informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor bedrijfsapps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
