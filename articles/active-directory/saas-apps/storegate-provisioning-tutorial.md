---
title: 'Zelf studie: Storegate configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Storegate.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: cf71246caef377fb607a9ca7a765ec9ca5999744
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91285922"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Zelf studie: Storegate configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Storegate en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in Storegate.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een Storegate-Tenant](https://www.storegate.com)
* Een gebruikers account op een Storegate met beheerders machtigingen.

## <a name="assign-users-to-storegate"></a>Gebruikers toewijzen aan Storegate

Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot Storegate. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan Storegate door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Belang rijke tips voor het toewijzen van gebruikers aan Storegate

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan Storegate om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Storegate, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-storegate-for-provisioning"></a>Storegate instellen voor inrichting

Voordat u Storegate configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u bepaalde inrichtings gegevens ophalen van Storegate.

1. Meld u aan bij de [Storegate-beheer console](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) en ga naar de instellingen door te klikken op het pictogram gebruiker in de rechter bovenhoek en selecteer **account instellingen**.

    ![SCIM Storegate toevoegen](media/storegate-provisioning-tutorial/admin.png)

2. In instellingen gaat u naar **Team > instellingen** en controleert u of de wissel knop is ingeschakeld in de sectie **eenmalige aanmelding** .

    ![Storegate-instellingen](media/storegate-provisioning-tutorial/team.png)

    ![Storegate wissel knop](media/storegate-provisioning-tutorial/sso.png)

3. Kopieer de **URL** van de Tenant en het **token**. Deze waarden worden respectievelijk in de velden **Tenant-URL** en **geheim** vermeld op het tabblad inrichten van uw Storegate-toepassing in de Azure Portal. 

    ![Storegate-token maken](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Storegate toevoegen vanuit de galerie

Als u Storegate wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Storegate van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Storegate**in het zoekvak en selecteer **Storegate** in het deel venster resultaten. 

    ![Storegate in de lijst met resultaten](common/search-new-app.png)

5. Selecteer de knop **Aanmelden voor Storegate** , waarmee u wordt doorgestuurd naar de aanmeldings pagina van Storegate. 

    ![Storegate OIDC toevoegen](media/storegate-provisioning-tutorial/signup.png)

6.  Meld u aan bij de [Storegate-beheer console](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) en ga naar de instellingen door te klikken op het pictogram gebruiker in de rechter bovenhoek en selecteer **account instellingen**.

    ![Storegate-aanmelding](media/storegate-provisioning-tutorial/admin.png)

7. In instellingen gaat u naar **Team > instellingen** en klikt u op wissel knop in de sectie eenmalige aanmelding, waarna de toestemming-flow wordt gestart. Klik op **activeren**.

    ![Storegate-team](media/storegate-provisioning-tutorial/team.png)

    ![Storegate SSO](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate activeren](media/storegate-provisioning-tutorial/activate.png)

8. Als Storegate een OpenIDConnect-app is, kiest u aanmelden bij Storegate met uw micro soft-werk account.

    ![Storegate OIDC-aanmelding](media/storegate-provisioning-tutorial/login.png)

9. Accepteer na een geslaagde verificatie de toestemming prompt voor de pagina toestemming. De toepassing wordt vervolgens automatisch toegevoegd aan uw Tenant en u wordt doorgestuurd naar uw Storegate-account.

    ![Storegate OIDc toestemming](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Automatische gebruikers inrichting configureren voor Storegate 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Storegate te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!NOTE]
> [Raadpleeg voor](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)meer informatie over het scim-eind punt van Storegate.

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Storegate in azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Storegate**.

    ![De koppeling Storegate in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Scherm opname van de opties voor beheer met de inrichtings optie.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Scherm afbeelding van de vervolg keuzelijst voor de inrichtings modus met de automatische optie aangeroepen.](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://dialpad.com/scim` in de Tenant- **URL**. Voer de waarde in die u hebt opgehaald en eerder hebt opgeslagen uit Storegate in een **geheim token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Storegate. Als de verbinding mislukt, zorg er dan voor dat uw Storegate-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Storegate**.

    ![Storegate-gebruikers toewijzingen](media/storegate-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Storegate in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Storegate voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Storegate-gebruikers kenmerken](media/storegate-provisioning-tutorial/userattributes.png)

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Als u de Azure AD-inrichtings service voor **Storegate wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor Storegate door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Storegate.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
