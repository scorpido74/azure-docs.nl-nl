---
title: 'Zelf studie: prioriteits matrix configureren voor automatische gebruikers inrichting met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts in de prioriteits matrix.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: 43f5b96c7029da6b49bc9df40d92fbd250145715
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325994"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Zelf studie: prioriteiten matrix configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in de Priority Matrix en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in prioriteits matrix.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een prioriteits matrix Tenant](https://appfluence.com/pricing/)
* Een gebruikers account op een prioriteits matrix met beheerders machtigingen.

## <a name="assign-users-to-priority-matrix"></a>Gebruikers toewijzen aan de prioriteits matrix

Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang moeten hebben tot de prioriteits matrix. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan de prioriteits matrix door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Belang rijke tips voor het toewijzen van gebruikers aan de prioriteits matrix

* U wordt aangeraden één Azure AD-gebruiker aan de prioriteits matrix toe te wijzen om de configuratie van de automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan de prioriteits matrix, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-priority-matrix-for-provisioning"></a>Prioriteits matrix voor inrichting instellen

Voordat u de prioriteits matrix configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u bepaalde inrichtings gegevens ophalen uit de prioritaire matrix.

1. Meld u aan bij de [beheer console van de prioriteiten matrix](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Klik op **OAuth-aanmeldings token** voor de prioriteits matrix

    ![SCIM voor prioriteits matrix toevoegen](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Klik op de knop **Nieuw Token ophalen** . Kopieer de **token teken reeks**. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van de toepassing Priority Matrix in de Azure Portal. 

    ![Token voor de prioriteits matrix maken](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Prioriteiten matrix toevoegen vanuit de galerie

Als u de prioriteits matrix wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u prioriteiten matrix uit de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **prioriteiten matrix**in en selecteer **prioriteiten matrix** in het resultaten paneel. 

    ![Prioriteits matrix in de resultaten lijst](common/search-new-app.png)

5. Selecteer de knop **Aanmelden voor prioriteits matrix** , waarmee u wordt omgeleid naar de aanmeldings pagina van de Priority-Matrix. 

    ![OIDC voor prioriteits matrix toevoegen](media/priority-matrix-provisioning-tutorial/signup.png)

6. Als Priority matrix is een OpenIDConnect-app die u wilt aanmelden bij Priority matrix met uw micro soft-werk account.

    ![OIDC-aanmelding voor prioriteits matrix](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Accepteer na een geslaagde verificatie de toestemming prompt voor de pagina toestemming. De toepassing wordt vervolgens automatisch toegevoegd aan uw Tenant en u wordt omgeleid naar uw Priority Matrix-account.

    ![OIDc toestemming voor prioriteiten matrix](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Automatische gebruikers inrichting configureren voor de prioriteits matrix 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Priority matrix te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!NOTE]
> Raadpleeg [User Provisioning and priority matrix](https://appfluence.com/help/article/user-provisioning/)voor meer informatie over het scim-eind punt van de Priority-Matrix.

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Automatische gebruikers inrichting voor de prioriteits matrix configureren in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Priority Matrix**.

    ![De prioriteits matrix koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Scherm opname van de opties voor beheer met de inrichtings optie.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Scherm afbeelding van de vervolg keuzelijst voor de inrichtings modus met de automatische optie aangeroepen.](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://sync.appfluence.com/scim/v2/` in de Tenant- **URL**. Voer de waarde in die u hebt opgehaald en eerder hebt opgeslagen in de prioriteits matrix in een **geheim token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met de prioriteits matrix. Als de verbinding mislukt, zorg er dan voor dat uw prioriteits matrix account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met prioriteits matrix**.

    ![Gebruikers toewijzingen voor prioriteits matrix](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Priority Matrix in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in de Priority-Matrix voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikers kenmerken van de prioriteits matrix](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Als u de Azure AD Provisioning Service voor Priority matrix wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor de prioriteits matrix door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning Service op prioriteits matrix.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)


