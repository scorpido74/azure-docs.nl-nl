---
title: 'Zelf studie: BitaBIZ configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op BitaBIZ.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 4f3e01147e44b495a666d11a3927eabbbe0dec0e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547272"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Zelf studie: BitaBIZ configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in BitaBIZ en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in BitaBIZ.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een BitaBIZ-Tenant](https://bitabiz.dk/en/price/).
* Een gebruikers account in BitaBIZ met beheerders machtigingen.

## <a name="assigning-users-to-bitabiz"></a>Gebruikers toewijzen aan BitaBIZ

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot BitaBIZ. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan BitaBIZ door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Belang rijke tips voor het toewijzen van gebruikers aan BitaBIZ

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan BitaBIZ om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan BitaBIZ, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-bitabiz-for-provisioning"></a>BitaBIZ instellen voor inrichting

Voordat u BitaBIZ configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten inschakelen op BitaBIZ.

1. Meld u aan bij de [BitaBIZ-beheer console](https://www.bitabiz.com/login?lang=en). Klik op **BEHEERDER INSTELLEN**.

    ![BitaBIZ-beheer console](media/bitabiz-provisioning-tutorial/setup-admin.png)

2.  Navigeer naar **integratie**.

    ![BitaBIZ-beheer console](media/bitabiz-provisioning-tutorial/integration.png)

2.  Navigeer naar **Microsoft Azure AD inrichting**.  Selecteer **ingeschakeld** bij automatische gebruikers inrichting. Kopieer de waarden voor **scim-inrichtings eind punt-URL** en  **Bearer-token**. Deze waarden worden ingevoerd in de velden Tenant-URL en geheim-token op het tabblad inrichten van uw BitaBIZ-toepassing in de Azure Portal.

    ![SCIM BitaBIZ toevoegen](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>BitaBIZ toevoegen vanuit de galerie

Als u BitaBIZ wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u BitaBIZ van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om BitaBIZ toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **BitaBIZ**in het zoekvak, selecteer **BitaBIZ** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![BitaBIZ in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Automatische gebruikers inrichting configureren voor BitaBIZ 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in BitaBIZ te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor BitaBIZ, gevolgd door de instructies in de [BitaBIZ-zelf studie voor eenmalige aanmelding](BitaBIZ-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar in de compliment

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor BitaBIZ in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **BitaBIZ** in de lijst met toepassingen.

    ![De koppeling naar BitaBIZ in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. In het gedeelte beheerders referenties voert u de **scim-inrichtings eind punt-URL** en **Bearer-token** waarden in die respectievelijk eerder zijn opgehaald in de Tenant-URL en het geheime token. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met BitaBIZ. Als de verbinding mislukt, zorg er dan voor dat uw BitaBIZ-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met BitaBIZ**.

    ![BitaBIZ-gebruikers toewijzingen](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar BitaBIZ in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in BitaBIZ voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![BitaBIZ-gebruikers kenmerken](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

11. Als u de Azure AD-inrichtings service voor **BitaBIZ wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor BitaBIZ door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op BitaBIZ.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

* BitaBIZ vereist **username**, **email**, **FirstName** en **LastName** als verplichte kenmerken. 
* BitaBIZ biedt momenteel geen ondersteuning voor harde verwijderingen.

## <a name="additional-resources"></a>Aanvullende bronnen

* Het [inrichten van een gebruikers account voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over inrichtings activiteiten](../app-provisioning/check-status-user-account-provisioning.md).
