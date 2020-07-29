---
title: 'Zelf studie: Storegate configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Storegate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 72903a36f88f9092ce1d203b557003083407320b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77064254"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Zelf studie: Storegate configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Storegate en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in Storegate.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een Storegate-Tenant](https://www.storegate.com)
* Een gebruikers account op een Storegate met beheerders machtigingen.

## <a name="assign-users-to-storegate"></a>Gebruikers toewijzen aan Storegate

Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot Storegate. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan Storegate door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Belang rijke tips voor het toewijzen van gebruikers aan Storegate

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan Storegate om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Storegate, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

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

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Storegate**.

    ![De koppeling Storegate in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://dialpad.com/scim` in de Tenant- **URL**. Voer de waarde in die u hebt opgehaald en eerder hebt opgeslagen uit Storegate in een **geheim token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Storegate. Als de verbinding mislukt, zorg er dan voor dat uw Storegate-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Storegate**.

    ![Storegate-gebruikers toewijzingen](media/storegate-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Storegate in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Storegate voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Storegate-gebruikers kenmerken](media/storegate-provisioning-tutorial/userattributes.png)

10. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

11. Als u de Azure AD-inrichtings service voor **Storegate wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor Storegate door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Storegate.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
