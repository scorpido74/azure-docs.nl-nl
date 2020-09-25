---
title: 'Zelf studie: Oracle Fusion ERP configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts tot Oracle Fusion ERP.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 3f835c021e20edba21583e71c6c1a480eca7fd89
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91255825"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Zelf studie: Oracle Fusion ERP configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Oracle Fusion ERP en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in Oracle Fusion ERP.

> [!NOTE]
>  In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector is momenteel beschikbaar in preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* Een [Oracle Fusion ERP-Tenant](https://www.oracle.com/applications/erp/).
* Een gebruikers account in Oracle Fusion ERP met beheerders machtigingen.

## <a name="assign-users-to-oracle-fusion-erp"></a>Gebruikers toewijzen aan Oracle Fusion ERP 
Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot Oracle Fusion ERP. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan Oracle Fusion ERP door de volgende instructies te volgen:
 
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Belang rijke tips voor het toewijzen van gebruikers aan Oracle Fusion ERP 

 * Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan Oracle Fusion ERP om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Oracle Fusion ERP, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol Standaard toegang worden uitgesloten van het inrichten.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Oracle Fusion ERP instellen voor het inrichten

Voordat u Oracle Fusion ERP configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten inschakelen op Oracle Fusion ERP.

1. Meld u aan bij uw [Oracle Fusion ERP-beheer console](https://cloud.oracle.com/sign-in)

2. Klik op de Navigator in de linkerbovenhoek bovenin. Onder **extra**, selecteer **beveiligings console**.

    ![Oracle Fusion ERP SCIM toevoegen](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Navigeer naar **gebruikers**.
    
    ![Oracle Fusion ERP SCIM toevoegen](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Sla de gebruikers naam en het wacht woord voor het gebruikers account van de beheerder op die u gaat gebruiken om u aan te melden bij de Oracle Fusion ERP-beheer console. Deze waarden moeten worden ingevoerd in de velden **gebruikers naam** en **wacht woord** beheerder op het tabblad inrichting van uw Oracle Fusion ERP-toepassing in de Azure Portal.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Oracle Fusion ERP toevoegen vanuit de galerie

Als u Oracle Fusion ERP wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Oracle Fusion ERP vanuit de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u Oracle Fusion ERP wilt toevoegen vanuit de Azure AD-toepassings galerie, voert u de volgende stappen uit:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Oracle Fusion ERP**in en selecteer **Oracle Fusion ERP** in het paneel resultaten.

    ![Oracle Fusion ERP in de lijst met resultaten](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Automatische gebruikers inrichting configureren voor Oracle Fusion ERP 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Oracle Fusion ERP te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor Oracle Fusion ERP door de instructies te volgen die zijn opgenomen in de [zelf studie Oracle Fusion ERP single sign-on](oracle-fusion-erp-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

> [!NOTE]
> Raadpleeg [rest API voor algemene functies in de cloud van Oracle-toepassingen](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)voor meer informatie over het scim-eind punt van Oracle Fusion ERP.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Fuze in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Oracle Fusion ERP** in de lijst met toepassingen.

    ![Het koppeling naar Oracle Fusion ERP in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Scherm opname van de opties voor beheer met de inrichtings optie.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Scherm afbeelding van de vervolg keuzelijst voor de inrichtings modus met de automatische optie aangeroepen.](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` in de Tenant- **URL**. Voer de gebruikers naam en het wacht woord voor de beheerder in die eerder zijn opgehaald in de velden **gebruikers naam** en **wacht** woord beheerder. Klik op **verbinding testen** tussen Azure AD en Oracle Fusion ERP. 

    ![Oracle Fusion ERP SCIM toevoegen](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Oracle Fusion ERP**.

    ![Oracle Fusion ERP SCIM toevoegen](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Oracle Fusion ERP in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Oracle Fusion ERP voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Oracle Fusion ERP SCIM toevoegen](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Oracle Fusion ERP**.

    ![Oracle Fusion ERP-groeps toewijzingen](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Oracle Fusion ERP in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt voor het vergelijken van de groepen in Oracle Fusion ERP voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerken van Oracle Fusion ERP-groep](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor Oracle Fusion ERP wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Oracle Fusion ERP door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

    Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Oracle Fusion ERP.

    Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

* Oracle Fusion ERP ondersteunt alleen basis verificatie voor hun SCIM-eind punt.
* Oracle Fusion ERP biedt geen ondersteuning voor het inrichten van groepen.
* Rollen in Oracle Fusion ERP worden toegewezen aan groepen in azure AD. Als u rollen wilt toewijzen aan gebruikers in Oracle Fusion ERP vanuit Azure AD, moet u gebruikers toewijzen aan de gewenste Azure AD-groepen die zijn benoemd na rollen in Oracle Fusion ERP.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
