---
title: 'Zelf studie: Wrike configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Wrike.
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
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 5dd4f5ac6152c22b5e2a84ecc0774672bcd5590b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77064186"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Zelf studie: Wrike configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die u uitvoert in Wrike en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers of groepen in Wrike.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen [gebruikers automatisch inrichten en ongedaan maken van de inrichting van SaaS-toepassingen (Software-as-a-Service) met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een Wrike-Tenant](https://www.wrike.com/price/)
* Een gebruikers account in Wrike met beheerders machtigingen

## <a name="assign-users-to-wrike"></a>Gebruikers toewijzen aan Wrike
Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u bepalen welke gebruikers of groepen in azure AD toegang nodig hebben tot Wrike. Wijs deze gebruikers of groepen vervolgens toe aan Wrike door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Belang rijke tips voor het toewijzen van gebruikers aan Wrike

* We raden u aan één Azure AD-gebruiker toe te wijzen aan Wrike om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Wrike, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol standaard toegang worden uitgesloten van het inrichten.

## <a name="set-up-wrike-for-provisioning"></a>Wrike instellen voor inrichting

Voordat u Wrike configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u systeem inschakelen voor SCIM-inrichting (Cross-Domain Identity Management) op Wrike.

1. Meld u aan bij de [Wrike-beheer console](https://www.Wrike.com/login/). Ga naar uw Tenant-ID. Selecteer **Apps & integraties**.

    ![Integraties van apps &](media/Wrike-provisioning-tutorial/admin.png)

2.  Ga naar **Azure AD** en selecteer deze.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Selecteer SCIM. Kopieer de **basis-URL**.

    ![Basis-URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Selecteer **API**  >  **Azure scim**.

    ![Azure-SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Er wordt een pop-up geopend. Voer het wacht woord in dat u eerder hebt gemaakt om een account te maken.

    ![Wrike-token maken](media/Wrike-provisioning-tutorial/password.png)

6.  Kopieer het **geheime token**en plak het in azure AD. Selecteer **Opslaan** om de inrichtings configuratie op Wrike te volt ooien.

    ![Permanent toegangs token](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Wrike toevoegen vanuit de galerie

Voordat u Wrike configureert voor het automatisch inrichten van gebruikers met Azure AD, voegt u Wrike van de Azure AD-toepassings galerie toe aan uw lijst met beheerde SaaS-toepassingen.

Voer de volgende stappen uit om Wrike toe te voegen vanuit de Azure AD-toepassings galerie.

1. Selecteer **Azure Active Directory**in de [Azure Portal](https://portal.azure.com)in het navigatie deel venster links.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Wrike**in het zoekvak, selecteer **Wrike** in het deel venster resultaten en selecteer **toevoegen** om de toepassing toe te voegen.

    ![Wrike in de lijst met resultaten](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Automatische gebruikers inrichting configureren voor Wrike 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers of groepen in Wrike te maken, bij te werken en uit te scha kelen op basis van gebruikers-of groeps toewijzingen in azure AD.

> [!TIP]
> Als u eenmalige aanmelding op basis van SAML wilt inschakelen voor Wrike, volgt u de instructies in de [Wrike-zelf studie voor eenmalige aanmelding](wrike-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Wrike in azure AD

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise Applications**  >  **alle toepassingen**in bedrijfs toepassingen.

    ![Alle toepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Wrike**.

    ![De koppeling Wrike in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Inrichtings modus ingesteld op automatisch](common/provisioning-automatic.png)

5. In het gedeelte beheerders referenties voert u de waarden van de **basis-URL** en het **permanente toegangs token** in die respectievelijk eerder zijn opgehaald in de **Tenant-URL** en het **geheime token**. Selecteer **verbinding testen** om te controleren of Azure AD verbinding kan maken met Wrike. Als de verbinding mislukt, zorgt u ervoor dat uw Wrike-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het vak **e-mail bericht** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen. Schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding](common/provisioning-notification-email.png)

8. Selecteer **Opslaan**.

9. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Wrike**.

    ![Wrike-gebruikers toewijzingen](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Wrike in de sectie **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Wrike voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren.

    ![Wrike-gebruikers kenmerken](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Als u bereik filters wilt configureren, volgt u de instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik.

12. Als u de Azure AD-inrichtings service voor **Wrike wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

13. Definieer de gebruikers of groepen die u wilt inrichten voor Wrike door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

14. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan bij de volgende synchronisaties. Voor meer informatie over hoe lang het duurt voor het inrichten van gebruikers of groepen, raadpleeg dan [hoe lang het duurt om gebruikers in te richten?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

U kunt de **huidige status** sectie gebruiken om de voortgang te controleren en koppelingen naar uw inrichtings activiteiten rapport te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Wrike. Zie [de status van gebruikers inrichten controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie. Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor informatie over het vastleggen van Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Het inrichten van gebruikers accounts beheren voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
