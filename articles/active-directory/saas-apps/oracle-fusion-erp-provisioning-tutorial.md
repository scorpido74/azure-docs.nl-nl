---
title: 'Zelfstudie: Oracle Fusion ERP configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Oracle Fusion ERP.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061173"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Zelfstudie: Oracle Fusion ERP configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Oracle Fusion ERP en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Oracle Fusion ERP.

> [!NOTE]
>  In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Voorbeeld. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure voor Voorbeeldfuncties

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* Een [Oracle Fusion ERP-tenant](https://www.oracle.com/applications/erp/).
* Een gebruikersaccount in Oracle Fusion ERP met beheerdersmachtigingen.

## <a name="assign-users-to-oracle-fusion-erp"></a>Gebruikers toewijzen aan Oracle Fusion ERP 
Azure Active Directory gebruikt een concept genaamd toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Oracle Fusion ERP. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Oracle Fusion ERP door de instructies hier te volgen:
 
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Belangrijke tips voor het toewijzen van gebruikers aan Oracle Fusion ERP 

 * Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Oracle Fusion ERP om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Oracle Fusion ERP toekent, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Oracle Fusion ERP instellen voor inrichten

Voordat u Oracle Fusion ERP configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-inrichting op Oracle Fusion ERP inschakelen.

1. Meld u aan bij uw [Oracle Fusion ERP-beheerconsole](https://cloud.oracle.com/sign-in)

2. Klik op de Navigator in de linkerbovenhoek. Selecteer **onder Extra**de optie **Beveiligingsconsole**.

    ![Oracle Fusion ERP Voeg SCIM toe](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Navigeer naar **gebruikers.**
    
    ![Oracle Fusion ERP Voeg SCIM toe](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Sla de gebruikersnaam en het wachtwoord op voor het beheerdersgebruikersaccount waarmee u zich aanmeldt bij de Oracle Fusion ERP-beheerconsole. Deze waarden moeten worden ingevoerd in de velden Gebruikersnaam en **Wachtwoord** **beheerder** op het tabblad Inrichten van uw Oracle Fusion ERP-toepassing in de Azure-portal.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Oracle Fusion ERP toevoegen vanuit de galerie

Als u Oracle Fusion ERP wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u Oracle Fusion ERP vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Oracle Fusion ERP toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ Oracle Fusion **ERP**in het zoekvak, selecteer **Oracle Fusion ERP** in het resultatenpaneel.

    ![Oracle Fusion ERP in de lijst met resultaten](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Automatische gebruikersinrichting configureren voor Oracle Fusion ERP 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Oracle Fusion ERP te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om saml-gebaseerde enkele aanmelding voor Oracle Fusion ERP in te schakelen door de instructies in de [Oracle Fusion ERP Single sign-on tutorial](oracle-fusion-erp-tutorial.md)te volgen. Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

> [!NOTE]
> Voor meer informatie over het SCIM-eindpunt van Oracle Fusion ERP raadpleegt u [REST API voor algemene functies in Oracle Applications Cloud.](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersvoorziening voor Fuze in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Oracle Fusion ERP** in de lijst met toepassingen.

    ![Het koppeling naar Oracle Fusion ERP in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer de gebruikersnaam en het wachtwoord van de beheerder in die eerder zijn opgehaald in de velden Gebruikersnaam en **Wachtwoord beheerder.** **Admin Username** Klik op **Testverbinding** tussen Azure AD en Oracle Fusion ERP. 

    ![Oracle Fusion ERP Voeg SCIM toe](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Oracle Fusion ERP**in de sectie **Toewijzingen** .

    ![Oracle Fusion ERP Voeg SCIM toe](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Oracle Fusion ERP in de sectie **Attribute-Mapping.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Oracle Fusion ERP te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Oracle Fusion ERP Voeg SCIM toe](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. Selecteer Azure **Active Directory-groepen synchroniseren met Oracle Fusion ERP**in de sectie **Toewijzingen** .

    ![Oracle Fusion ERP Group Mappings](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Bekijk de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Oracle Fusion ERP in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen Matching** worden gebruikt om de groepen in Oracle Fusion ERP te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van Oracle Fusion ERP-groep](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Oracle Fusion ERP wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Oracle Fusion ERP door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

    Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Oracle Fusion ERP.

    Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

* Oracle Fusion ERP ondersteunt alleen basisverificatie voor hun SCIM-eindpunt.
* Oracle Fusion ERP ondersteunt geen groepsinrichting.
* Rollen in Oracle Fusion ERP worden toegewezen aan groepen in Azure AD. Als u rollen wilt toewijzen aan gebruikers in Oracle Fusion ERP vanuit Azure AD, moet u gebruikers toewijzen aan de gewenste Azure AD-groepen die zijn vernoemd naar rollen in Oracle Fusion ERP.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
