---
title: 'Zelf studie: TheOrgWiki configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op TheOrgWiki.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063137"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Zelf studie: TheOrgWiki configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in TheOrgWiki en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in TheOrgWiki.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een OrgWiki-Tenant](https://www.theorgwiki.com/welcome/).
* Een gebruikers account in TheOrgWiki met beheerders machtigingen.

## <a name="assign-users-to-theorgwiki"></a>Gebruikers toewijzen aan TheOrgWiki

Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot TheOrgWiki. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan TheOrgWiki door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Belang rijke tips voor het toewijzen van gebruikers aan TheOrgWiki

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan TheOrgWiki om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan TheOrgWiki, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-theorgwiki-for-provisioning"></a>TheOrgWiki instellen voor inrichting

Voordat u TheOrgWiki configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten inschakelen op TheOrgWiki.

1. Meld u aan bij de [TheOrgWiki-beheer console](https://www.theorgwiki.com/login/). Klik op **beheer console**.

    ![SCIM TheOrgWiki toevoegen](media/theorgwiki-provisioning-tutorial/login.png)

2. Klik in de beheer console op het **tabblad Instellingen**. 

    ![SCIM TheOrgWiki toevoegen](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Navigeer naar **service accounts**.

    ![SCIM TheOrgWiki toevoegen](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Klik op **+ Service account**. Selecteer bij **type service account**de optie **op token gebaseerd**. Klik op **Opslaan**.

    ![SCIM TheOrgWiki toevoegen](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Kopieer de **actieve tokens**. Deze waarde wordt ingevoerd in het veld geheime token op het tabblad inrichten van uw TheOrgWiki-toepassing in de Azure Portal.
     
    ![SCIM TheOrgWiki toevoegen](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>TheOrgWiki toevoegen vanuit de galerie

Als u TheOrgWiki wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u TheOrgWiki van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **TheOrgWiki**in het zoekvak en selecteer **TheOrgWiki** in het deel venster resultaten. 

    ![TheOrgWiki in de lijst met resultaten](common/search-new-app.png)

5. Selecteer de knop **Aanmelden voor TheOrgWiki** , waarmee u wordt doorgestuurd naar de aanmeldings pagina van TheOrgWiki. 

    ![SCIM TheOrgWiki toevoegen](media/theorgwiki-provisioning-tutorial/image00.png)

6.  Selecteer **Aanmelden**in de rechter bovenhoek.

    ![SCIM TheOrgWiki toevoegen](media/theorgwiki-provisioning-tutorial/image02.png)

7. Als TheOrgWiki een OpenIDConnect-app is, kiest u aanmelden bij OrgWiki met uw micro soft-werk account.

    ![SCIM TheOrgWiki toevoegen](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Na een geslaagde verificatie wordt de toepassing automatisch toegevoegd aan uw Tenant en wordt u omgeleid naar uw TheOrgWiki-account.

    ![SCIM OrgWiki toevoegen](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Automatische gebruikers inrichting configureren voor TheOrgWiki 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TheOrgWiki te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor TheOrgWiki in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **TheOrgWiki**.

    ![De koppeling OrgWiki in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` in de **Tenant-URL**. 

    Voorbeeld: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> De **subdomeinwaarde** kan alleen worden ingesteld tijdens het eerste aanmeldings proces voor TheOrgWiki.
 
6. Voer de token waarde in het veld **geheim token** in die u eerder hebt opgehaald van TheOrgWiki. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met TheOrgWiki. Als de verbinding mislukt, zorg er dan voor dat uw TheOrgWiki-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met TheOrgWiki**.

    ![TheOrgWiki-gebruikers toewijzingen](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar TheOrgWiki in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in TheOrgWiki voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![TheOrgWiki-gebruikers kenmerken](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

12. Als u de Azure AD-inrichtings service voor **TheOrgWiki wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

13. Definieer de gebruikers en/of groepen die u wilt inrichten voor OrgWiki door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

14. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan bij de volgende synchronisaties. Voor meer informatie over hoe lang het duurt voor het inrichten van gebruikers en/of groepen, raadpleegt u [hoe lang het duurt om gebruikers](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)in te richten.

U kunt de **huidige status** sectie gebruiken om de voortgang te controleren en koppelingen naar uw inrichtings activiteiten rapport te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op TheOrgWiki. Zie [de status van gebruikers inrichten controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie. Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor informatie over het vastleggen van Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende resources

* Het [inrichten van een gebruikers account voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over inrichtings activiteiten](../app-provisioning/check-status-user-account-provisioning.md).