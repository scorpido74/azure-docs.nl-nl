---
title: 'Zelfstudie: Zscaler Private Access (ZPA) configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Zscaler Private Access (ZPA).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: ee9128c3-ff02-4739-8c51-0693d8451742
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: 609d2726eaaaeb49210e19f000bcc2faef1de5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064144"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Zelfstudie: Zscaler Private Access (ZPA) configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Zscaler Private Access (ZPA) en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Zscaler Private Access (ZPA).

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Zscaler Private Access (ZPA) tenant](https://www.zscaler.com/pricing-and-plans#contact-us)
* Een gebruikersaccount in Zscaler Private Access (ZPA) met beheerdersmachtigingen.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Gebruikers toewijzen aan Zscaler Private Access (ZPA)

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Zscaler Private Access (ZPA). Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Zscaler Private Access (ZPA) door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Belangrijke tips voor het toewijzen van gebruikers aan Zscaler Private Access (ZPA)

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Zscaler Private Access (ZPA) om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker toewijst aan Zscaler Private Access (ZPA), moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Zscaler Private Access (ZPA) instellen voor inrichten

1. Meld u aan bij uw [Zscaler Private Access (ZPA) admin console.](https://admin.private.zscaler.com/) Navigeer naar **Beheer > IdP-configuratie**.

    ![Zscaler Private Access (ZPA) Admin Console](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Controleer of een IdP voor **eenmalige aanmelding** is geconfigureerd. Als er geen IdP is ingesteld, voeg dan een door te klikken op het plus-pictogram in de rechterbovenhoek van het scherm.

    ![Zscaler Private Access (ZPA) Voeg SCIM toe](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Volg de wizard **IdP-configuratie toevoegen** om een IdP toe te voegen. Laat het veld **Eenmalig aanmelding** instellen op **Gebruiker**. Geef een **naam** op en selecteer de **domeinen** in de vervolgkeuzelijst. Klik op **Volgende** om naar het volgende venster te navigeren.

    ![Zscaler Private Access (ZPA) Voeg IdP toe](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Download het **serviceprovidercertificaat**. Klik op **Volgende** om naar het volgende venster te navigeren.

    ![Zscaler Private Access (ZPA) SP-certificaat](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Upload in het volgende venster het eerder gedownloade **Serviceprovidercertificaat.**

    ![Zscaler Private Access (ZPA) uploadcertificaat](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Scroll naar beneden om de URL voor **één aanmelding** en **idp-entiteits-id**te opgeven .

    ![Zscaler Private Access (ZPA) IdP-id](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Scroll naar beneden om **SCIM Sync in te schakelen**. Klik op de knop **Nieuwe token genereren.** Kopieer het **token aan toonder**. Deze waarde wordt ingevoerd in het veld Geheim token op het tabblad Provisioning van uw Zscaler Private Access-toepassing (ZPA) in de Azure-portal.

    ![Zscaler Private Access (ZPA) Token maken](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Als u de **URL van de tenant wilt** vinden, navigeert u naar Beheer > **IdP-configuratie**. Klik op de naam van de nieuw toegevoegde IdP-configuratie op de pagina.

    ![Zscaler Private Access (ZPA) Idp-naam](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Schuif omlaag om het Eindpunt van de **SCIM-serviceprovider** aan het einde van de pagina weer te geven. Kopieer het eindpunt van de **SCIM-serviceprovider**. Deze waarde wordt ingevoerd in het veld URL van tenant op het tabblad Provisioning van uw Zscaler Private Access-toepassing (ZPA) in de Azure-portal.

    ![Zscaler Private Access (ZPA) SCIM URL](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler Private Access (ZPA) toevoegen vanuit de galerie

Voordat u Zscaler Private Access (ZPA) configureert voor automatische gebruikersvoorziening met Azure AD, moet u Zscaler Private Access (ZPA) toevoegen vanuit de Azure AD-toepassingsgalerie aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Zscaler Private Access (ZPA) toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Zscaler Private Access (ZPA)** in het zoekvak, selecteer **Zscaler Private Access (ZPA)** in het deelvenster Resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Zscaler Private Access (ZPA) in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Automatische gebruikersvoorziening configureren voor Zscaler Private Access (ZPA) 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Zscaler Private Access (ZPA) te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on voor Zscaler Private Access (ZPA) in te schakelen door de instructies in de [Zscaler Private Access (ZPA) Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)op te volgen. Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

> [!NOTE]
> Raadpleeg [dit](https://www.zscaler.com/partners/microsoft)voor meer informatie over het SCIM-eindpunt van Zscaler Private Access.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Ga als lid van de gebruiker voor de voorziening van Zscaler Private Access (ZPA) in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zscaler Private Access (ZPA)** in de lijst met toepassingen.

    ![De Zscaler Private Access (ZPA) link in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** de **eindpuntwaarde van de SCIM-serviceprovider** in die eerder in **tenant-URL**is opgehaald . Voer de waarde **van het token aan toonder** in die eerder in Secret **Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Zscaler Private Access (ZPA). Als de verbinding mislukt, moet u ervoor zorgen dat uw Zscaler Private Access-account (ZPA)-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer azure **Active Directory-gebruikers synchroniseren met Zscaler Private Access (ZPA)** onder de sectie **Toewijzingen** .

    ![Zscaler Private Access (ZPA) Gebruikerstoewijzingen](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Zscaler Private Access (ZPA) in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Zscaler Private Access (ZPA) te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Zscaler Private Access (ZPA) Gebruikerskenmerken](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Selecteer azure **Active Directory-groepen synchroniseren met Zscaler Private Access (ZPA)** onder de sectie **Toewijzingen** .

    ![Zscaler Private Access (ZPA) Groepstoewijzingen](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Zscaler Private Access (ZPA) in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in Zscaler Private Access (ZPA) te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Zscaler Private Access (ZPA) Groepskenmerken](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Zscaler Private Access (ZPA) wilt inschakelen, wijzigt u de **ininrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Zscaler Private Access (ZPA) door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Zscaler Private Access (ZPA).

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

