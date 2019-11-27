---
title: 'Zelf studie: Zscaler private Access (ZPA) configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts voor Zscaler private Access (ZPA).
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
ms.openlocfilehash: c5274f3c38782394b01f8f29c56823753783307c
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287721"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Zelf studie: Zscaler private Access (ZPA) configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Zscaler private Access (ZPA) en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen naar Zscaler private Access (ZPA).

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een ZPA-Tenant (Zscaler private Access)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Een gebruikers account in Zscaler private Access (ZPA) met beheerders machtigingen.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Gebruikers toewijzen aan Zscaler persoonlijke toegang (ZPA)

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot Zscaler private Access (ZPA). Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan Zscaler private Access (ZPA) door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Belang rijke tips voor het toewijzen van gebruikers aan Zscaler private Access (ZPA)

* Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan Zscaler private Access (ZPA) om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Zscaler private Access (ZPA), moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Zscaler private Access (ZPA) voor inrichting instellen

1. Meld u aan bij uw [ZPA-beheer console (Zscaler private Access)](https://admin.private.zscaler.com/). Ga naar **beheer > IDP-configuratie**.

    ![Zscaler private Access (ZPA)-beheer console](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Controleer of er een IdP is geconfigureerd voor **eenmalige aanmelding** . Als er geen IdP is ingesteld, voegt u er een toe door te klikken op het plus pictogram in de rechter bovenhoek van het scherm.

    ![Zscaler private Access (ZPA) SCIM toevoegen](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Volg de wizard **IDP-configuratie toevoegen** om een IDP toe te voegen. Zorg ervoor dat het veld **eenmalige aanmelding** is ingesteld op **gebruiker**. Geef een **naam** op en selecteer de **domeinen** in de vervolg keuzelijst. Klik op **volgende** om naar het volgende venster te gaan.

    ![Zscaler private Access (ZPA) IdP toevoegen](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Down load het certificaat van de **service provider**. Klik op **volgende** om naar het volgende venster te gaan.

    ![Zscaler private Access (ZPA) SP-certificaat](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Upload het certificaat van de **service provider** in het volgende venster dat u eerder hebt gedownload.

    ![Zscaler private Access (ZPA) certificaat uploaden](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Schuif omlaag om de **URL voor eenmalige aanmelding** en de **IDP-Entiteits-ID**op te geven.

    ![Zscaler private Access (ZPA) IdP-ID](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Schuif omlaag om **scim-synchronisatie in te scha kelen**. Klik op de knop **nieuw token genereren** . Kopieer het **Bearer-token**. Deze waarde wordt ingevoerd in het veld geheime token op het tabblad inrichten van uw ZPA-toepassing (Zscaler private Access) in de Azure Portal.

    ![Zscaler private Access (ZPA) token maken](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Als u de **URL** van de Tenant wilt zoeken, gaat u naar **beheer > IDP-configuratie**. Klik op de naam van de zojuist toegevoegde IdP-configuratie die wordt weer gegeven op de pagina.

    ![Zscaler private Access (ZPA) IDP name](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Schuif omlaag om het **eind punt van de scim-service provider** aan het einde van de pagina weer te geven. Kopieer het **eind punt van de scim-service provider**. Deze waarde wordt ingevoerd in het veld Tenant-URL op het tabblad inrichting van uw ZPA-toepassing (Zscaler private Access) in de Azure Portal.

    ![Zscaler private Access (ZPA) SCIM-URL](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler private Access (ZPA) toevoegen vanuit de galerie

Voordat u Zscaler private Access (ZPA) configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Zscaler private Access (ZPA) toevoegen vanuit de Azure AD-toepassings galerie aan uw lijst met beheerde SaaS-toepassingen.

**Als u Zscaler private Access (ZPA) wilt toevoegen vanuit de Azure AD-toepassings galerie, voert u de volgende stappen uit:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Zscaler private Access (ZPA)** in, selecteer **Zscaler private Access (ZPA)** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Zscaler private Access (ZPA) in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Automatische gebruikers inrichting configureren voor Zscaler private Access (ZPA) 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Zscaler private Access (ZPA) te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor Zscaler private Access (ZPA) door de instructies in de [zelf studie Zscaler private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)eenmalige aanmelding te volgen. Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

> [!NOTE]
> Raadpleeg voor meer informatie [over het scim](https://www.zscaler.com/partners/microsoft)-eind punt van Zscaler private Access.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Zscaler private Access (ZPA) in azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Zscaler private Access (ZPA)** .

    ![De koppeling Zscaler persoonlijke toegang (ZPA) in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Voer in het gedeelte **beheerders referenties** de waarde in van de scim van de **service provider** die eerder is opgehaald in de **Tenant-URL**. Invoer van de **Bearer-token** waarde die eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Zscaler private Access (ZPA). Als de verbinding mislukt, zorgt u ervoor dat uw ZPA-account (Zscaler private Access) beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Zscaler persoonlijke toegang (ZPA)** .

    ![Zscaler persoonlijke toegang (ZPA) gebruikers toewijzingen](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zscaler private Access (ZPA) in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Zscaler private Access (ZPA) voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![ZPA-gebruikers kenmerken (Zscaler private Access)](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Zscaler private Access (ZPA)** .

    ![Zscaler persoonlijke toegang (ZPA) groeps toewijzingen](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zscaler private Access (ZPA) in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de groepen in Zscaler private Access (ZPA) voor update bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![ZPA-groeps kenmerken (Zscaler private Access)](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD-inrichtings service voor Zscaler private Access (ZPA) wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Zscaler private Access (ZPA) door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Zscaler private Access (ZPA).

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../manage-apps/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)

