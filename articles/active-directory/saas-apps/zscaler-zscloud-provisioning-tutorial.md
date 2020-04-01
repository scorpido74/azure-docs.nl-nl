---
title: 'Zelfstudie: Zscaler ZSCloud configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: In deze zelfstudie leert u hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en te deprovisioneren naar Zscaler ZSCloud.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: a752be80-d3ef-45d1-ac8f-4fb814c07b07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: af9d4aa15a4ff2784d2e1ca1334d9c24a8d12251
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77062673"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Zelfstudie: Zscaler ZSCloud configureren voor automatische gebruikersinrichting

In deze zelfstudie leert u hoe u Azure Active Directory (Azure AD) configureert om gebruikers en/of groepen automatisch in te richten en te deprovisioneren op Zscaler ZSCloud.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd op de Azure AD-service voor het inrichten van gebruikers. Zie [Gebruikersinrichting en deprovisioning voor saas-toepassingen automatiseren voor](../active-directory-saas-app-provisioning.md)belangrijke informatie over wat deze service doet en hoe deze werkt en antwoorden op veelgestelde vragen, voor belangrijke details over wat deze service doet en hoe deze werkt, en antwoorden op veelgestelde vragen.


## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze zelfstudie wilt uitvoeren, hebt u het volgende nodig:

* Een Azure AD-tenant.
* Een Zscaler ZSCloud-tenant.
* Een gebruikersaccount in Zscaler ZSCloud met beheerdersmachtigingen.

> [!NOTE]
> De integratie van Azure AD-inrichting is gebaseerd op de Zscaler ZSCloud SCIM API, die beschikbaar is voor Enterprise-accounts.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Zscaler ZSCloud toevoegen vanuit de galerie

Voordat u Zscaler ZSCloud configureert voor automatische gebruikersvoorziening met Azure AD, moet u Zscaler ZSCloud vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

Selecteer azure **active directory**in de [Azure-portal](https://portal.azure.com)in het linkerdeelvenster:

![Selecteer Azure Active Directory](common/select-azuread.png)

Ga naar **Enterprise-toepassingen** en selecteer **Alle toepassingen:**

![Bedrijfstoepassingen](common/enterprise-applications.png)

Als u een toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het venster:

![Nieuwe toepassing selecteren](common/add-new-app.png)

Voer in het zoekvak **Zscaler ZSCloud**in. Selecteer **Zscaler ZSCloud** in de resultaten en selecteer **Vervolgens Toevoegen**.

![Resultatenlijst](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Gebruikers toewijzen aan Zscaler ZSCloud

Azure AD-gebruikers moeten toegang krijgen tot geselecteerde apps voordat ze ze kunnen gebruiken. In de context van automatische gebruikersinrichting worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Zscaler ZSCloud nodig hebben. Nadat u dat hebt besloten, u deze gebruikers en groepen toewijzen aan Zscaler ZSCloud door de instructies in [Een gebruiker of groep toewijzen aan een bedrijfsapp te volgen.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Belangrijke tips voor het toewijzen van gebruikers aan Zscaler ZSCloud

* We raden u aan eerst één Azure AD-gebruiker aan Zscaler ZSCloud toe te wijzen om de automatische configuratie van gebruikersinrichting te testen. U later meer gebruikers en groepen toewijzen.

* Wanneer u een gebruiker aan Zscaler ZSCloud toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoogvenster Toewijzing. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-automatic-user-provisioning"></a>Automatische gebruikersinrichting instellen

In deze sectie u de stappen doorlopen voor het configureren van de Azure AD-inrichtingsservice voor het maken, bijwerken en uitschakelen van gebruikers en groepen in Zscaler ZSCloud op basis van gebruikers- en groepstoewijzingen in Azure AD.

> [!TIP]
> U ook saml-gebaseerde eenmalige aanmelding voor Zscaler ZSCloud inschakelen. Als u dit doet, volgt u de instructies in de [zelfstudie voor eenmalig aanmelden van Zscaler ZSCloud.](zscaler-zsCloud-tutorial.md) Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, maar de twee functies vullen elkaar aan.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Enterprise-toepassingen** > **Alle toepassingen** > **Zscaler ZSCloud:**

    ![Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zscaler ZSCloud**in de lijst met toepassingen:

    ![Lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten:**

    ![Zscaler ZSCloud-provisioning](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Stel de **inrichtingsmodus** in op **Automatisch:**

    ![De inrichtingsmodus instellen](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. Voer in de sectie **Beheerdersreferenties** de **URL van tenant** en geheim **token** van uw Zscaler ZSCloud-account in, zoals beschreven in de volgende stap.

6. Als u de **URL van** de tenant en het geheime token **wilt ophalen,** gaat u naar**Verificatieinstellingen** **voor beheer** > in de Zscaler ZSCloud-portal en selecteert u **SAML** onder **Verificatietype:**

    ![Zscaler ZSCloud-verificatieinstellingen](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Selecteer **SAML configureren** om het **SAML-venster configureren** te openen:

    ![SAML-venster configureren](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Selecteer **SCIM-gebaseerde inrichting inschakelen** en kopieer de url van de **basis** en het token **aan toonder**en sla de instellingen op. Plak in de Azure-portal de **url van** de basis in het vak **Tenant-URL** en het **token aan toonder** in het vak **Secret Token.**

7. Nadat u de waarden in de **vakken Tenant-URL** en **Geheim Token hebt** ingevoerd, selecteert u **Testverbinding** om te zorgen dat Azure AD verbinding kan maken met Zscaler ZSCloud. Als de verbinding mislukt, controleert u of uw Zscaler ZSCloud-account beheerdersmachtigingen heeft en probeert u het opnieuw.

    ![De verbinding testen](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. Voer in het vak **Berichte-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen. Selecteer **Een e-mailmelding verzenden wanneer er een fout optreedt:**

    ![E-mail met meldingen instellen](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Selecteer **Opslaan**.

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory Users synchroniseren met ZscalerZSCloud:**

    ![Azure AD-gebruikers synchroniseren](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Zscaler ZSCloud in de sectie **Toewijzingen van kenmerken.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de gebruikersaccounts in Zscaler ZSCloud te matchen voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen door te voeren.

    ![Kenmerkentoewijzingen](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory Groups synchroniseren met ZscalerZSCloud:**

    ![Azure AD-groepen synchroniseren](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Zscaler ZSCloud in de sectie **Toewijzingen van kenmerken.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepen in Zscaler ZSCloud te matchen voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen door te voeren.

    ![Kenmerkentoewijzingen](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Als u scopingfilters wilt configureren, raadpleegt u de instructies in de zelfstudie van het [filter Scoping](./../active-directory-saas-scoping-filters.md).

15. Als u de Azure AD-inrichtingsservice voor Zscaler ZSCloud wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen:**

    ![Inrichtingsstatus](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Definieer de gebruikers en/of groepen die u aan Zscaler ZSCloud wilt inrichten door de gewenste waarden te kiezen onder **Bereik** in de sectie **Instellingen:**

    ![Bereikwaarden](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Wanneer u klaar bent voor inlevering, selecteert u **Opslaan:**

    ![Opslaan selecteren](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden, zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de voortgang in de sectie **Synchronisatiedetails** controleren. U ook koppelingen volgen naar een rapportagerapport voor het inrichten van activiteiten, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Zscaler ZSCloud.

Zie [Rapportage over automatische gebruikersaccountinrichting](../active-directory-saas-provisioning-reporting.md)voor informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor bedrijfsapps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
