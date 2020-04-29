---
title: 'Zelf studie: Oracle Fusion ERP configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts tot Oracle Fusion ERP.
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
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 08a82be5a11ba7b81cd1939fc5b386c161c43480
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77061173"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Zelf studie: Oracle Fusion ERP configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Oracle Fusion ERP en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in Oracle Fusion ERP.

> [!NOTE]
>  In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector is momenteel beschikbaar als preview-versie. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* Een [Oracle Fusion ERP-Tenant](https://www.oracle.com/applications/erp/).
* Een gebruikers account in Oracle Fusion ERP met beheerders machtigingen.

## <a name="assign-users-to-oracle-fusion-erp"></a>Gebruikers toewijzen aan Oracle Fusion ERP 
Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot Oracle Fusion ERP. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan Oracle Fusion ERP door de volgende instructies te volgen:
 
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Belang rijke tips voor het toewijzen van gebruikers aan Oracle Fusion ERP 

 * Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan Oracle Fusion ERP om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Oracle Fusion ERP, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol standaard toegang worden uitgesloten van het inrichten.

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

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

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

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Oracle Fusion ERP** in de lijst met toepassingen.

    ![Het koppeling naar Oracle Fusion ERP in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` invoer in de **Tenant-URL**. Voer de gebruikers naam en het wacht woord voor de beheerder in die eerder zijn opgehaald in de velden **gebruikers naam** en **wacht** woord beheerder. Klik op **verbinding testen** tussen Azure AD en Oracle Fusion ERP. 

    ![Oracle Fusion ERP SCIM toevoegen](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Oracle Fusion ERP**.

    ![Oracle Fusion ERP SCIM toevoegen](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Oracle Fusion ERP in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Oracle Fusion ERP voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Oracle Fusion ERP SCIM toevoegen](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Oracle Fusion ERP**.

    ![Oracle Fusion ERP-groeps toewijzingen](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Oracle Fusion ERP in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt voor het vergelijken van de groepen in Oracle Fusion ERP voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerken van Oracle Fusion ERP-groep](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD-inrichtings service voor Oracle Fusion ERP wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Oracle Fusion ERP door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

    Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Oracle Fusion ERP.

    Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

* Oracle Fusion ERP ondersteunt alleen basis verificatie voor hun SCIM-eind punt.
* Oracle Fusion ERP biedt geen ondersteuning voor het inrichten van groepen.
* Rollen in Oracle Fusion ERP worden toegewezen aan groepen in azure AD. Als u rollen wilt toewijzen aan gebruikers in Oracle Fusion ERP vanuit Azure AD, moet u gebruikers toewijzen aan de gewenste Azure AD-groepen die zijn benoemd na rollen in Oracle Fusion ERP.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
