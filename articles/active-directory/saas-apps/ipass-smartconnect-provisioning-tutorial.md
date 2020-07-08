---
title: 'Zelf studie: iPass SmartConnect configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op iPass SmartConnect.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 43d6de4a-b3a2-439b-95f2-8883fffded52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: fb3a5d03f390b88f9856f03bdc97a35b845874ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77057518"
---
# <a name="tutorial-configure-ipass-smartconnect-for-automatic-user-provisioning"></a>Zelf studie: iPass SmartConnect configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om te demonstreren welke stappen moeten worden uitgevoerd in iPass SmartConnect en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen op iPass SmartConnect.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een IPass SmartConnect-Tenant](https://www.ipass.com/buy-ipass/).
* Een gebruikers account in iPass SmartConnect met beheerders machtigingen.

## <a name="assigning-users-to-ipass-smartconnect"></a>Gebruikers toewijzen aan iPass SmartConnect

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot iPass SmartConnect. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan iPass SmartConnect door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-ipass-smartconnect"></a>Belang rijke tips voor het toewijzen van gebruikers aan iPass SmartConnect

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan iPass SmartConnect om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan iPass SmartConnect, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-ipass-smartconnect-for-provisioning"></a>IPass SmartConnect instellen voor inrichting

Voordat u iPass SmartConnect configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u configuratie-informatie ophalen van de iPass SmartConnect-beheer console:

1. Als u het Bearer-token wilt ophalen dat nodig is voor verificatie op basis van uw iPass SmartConnect SCIM-eind punt, raadpleegt u de eerste keer dat u iPass SmartConnect instelt, omdat deze waarde alleen wordt opgegeven. 
2. Als u geen Bearer-token hebt, neemt u contact op met het [ondersteunings team van IPass SmartConnect](mailto:help@ipass.com) om een nieuwe te verkrijgen.

## <a name="add-ipass-smartconnect-from-the-gallery"></a>IPass SmartConnect toevoegen vanuit de galerie

Als u iPass SmartConnect wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u iPass SmartConnect van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om iPass SmartConnect toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **IPass SmartConnect**in, selecteer **iPass SmartConnect** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![iPass SmartConnect in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-ipass-smartconnect"></a>Automatische gebruikers inrichting configureren voor iPass SmartConnect 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in iPass SmartConnect te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
>  U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor BitaBIZ, gevolgd door de instructies in de [zelf studie IPass SmartConnect single sign-on](ipasssmartconnect-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-ipass-smartconnect-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor iPass SmartConnect in azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **IPass SmartConnect**.

    ![De koppeling iPass SmartConnect in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://openmobile.ipass.com/moservices/scim/v1` in de Tenant- **URL**. Voer het Bearer-token in dat eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met iPass SmartConnect. Als de verbinding mislukt, zorg er dan voor dat uw iPass SmartConnect-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met iPass SmartConnect**.

    ![iPass SmartConnect-gebruikers toewijzingen](media/ipass-smartconnect-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar iPass SmartConnect in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in iPass SmartConnect voor update bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![iPass SmartConnect-gebruikers toewijzingen](media/ipass-smartconnect-provisioning-tutorial/userattribute.png)


10. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

11. Als u de Azure AD-inrichtings service voor iPass SmartConnect wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor iPass SmartConnect door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. Hiermee worden alle acties beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op iPass SmartConnect.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

* iPass SmartConnect accepteert alleen gebruikers namen waarvan de domeinen zijn geregistreerd in de iPass SmartConnect-beheer console.  

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
