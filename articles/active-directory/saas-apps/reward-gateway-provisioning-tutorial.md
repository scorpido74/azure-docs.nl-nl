---
title: 'Zelf studie: een belonings gateway configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts voor een belonings gateway.
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
ms.openlocfilehash: 298ca69a659bec2d5262b344c274fac7371390f1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88534350"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Zelf studie: belonings gateway voor automatische gebruikers inrichting configureren

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in belonings gateway en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen tot belonings gateway.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* Een [beloning gateway-Tenant](https://www.rewardgateway.com/).
* Een gebruikers account in beloning gateway met beheerders machtigingen.

## <a name="assigning-users-to-reward-gateway"></a>Gebruikers toewijzen aan belonings gateway 

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot belonings gateway. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan een beloning gateway door de instructies in [een gebruiker of groep toewijzen aan een bedrijfs-app te](../manage-apps/assign-user-or-group-access-portal.md)volgen.


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Belang rijke tips voor het toewijzen van gebruikers aan belonings gateway 

* Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan de prijs van de automatische gebruikers inrichtings configuratie. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan belonings gateway, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-reward-gateway--for-provisioning"></a>Stel de gateway in voor de inrichting van het inrichten
Voordat u een belonings gateway configureert voor automatische gebruikers toewijzing met Azure AD, moet u SCIM Provisioning inschakelen voor de belonings gateway.

1. Meld u aan bij de [beheer console van uw belonings gateway](https://rewardgateway.photoshelter.com/login/). Klik op **integraties**.

    ![Beheer console van belonings gateway](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Selecteer **mijn integratie**.

    ![Beheer console van belonings gateway](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Kopieer de waarden van de **scim-URL (v2)** en het **OAuth Bearer-token**. Deze waarden worden ingevoerd in het veld Tenant-URL en geheim token op het tabblad inrichting van uw belonings gateway toepassing in de Azure Portal.

    ![Beheer console van belonings gateway](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Belonings gateway toevoegen vanuit de galerie

Als u een belonings gateway wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u een belonende gateway toevoegen vanuit de Azure AD-toepassings galerie naar uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om een belonende gateway uit de Azure AD-toepassings galerie toe te voegen:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak de prijs van de **lonen**in, Selecteer in het deel venster resultaten **belonings gateway** en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Reward Gateway in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Automatische gebruikers inrichting configureren voor de beloning van Gateway  

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in belonings gateway te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te scha kelen voor de beschik bare gateway, gevolgd door de instructies in de [zelf studie voor eenmalige aanmelding in de on-Sign-gateway](reward-gateway-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Automatische gebruikers inrichting voor belonings gateway in azure AD configureren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Reward Gateway** in de lijst met toepassingen.

    ![De koppeling Reward Gateway in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. In het gedeelte **beheerders referenties** voert u de **scim-URL (v2)** en de **OAuth Bearer-token** waarden in die respectievelijk eerder zijn opgehaald in de **Tenant-URL** en het **geheime token** . Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met de belonings gateway. Als de verbinding mislukt, zorg er dan voor dat uw beloning gateway-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Synchroniseer Azure Active Directory gebruikers om de gateway te belonen**.

    ![Beheer console van belonings gateway](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD to beloning gateway in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de gebruikers accounts in belonings gateway voor update bewerkingen te vergelijken. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Beheer console van belonings gateway](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

11. Als u de Azure AD Provisioning Service voor belonings gateway wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor de berekenings gateway door de gewenste waarden in het **bereik** in de sectie **instellingen** te kiezen.

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op belonings gateway.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

De belonings gateway biedt momenteel geen ondersteuning voor het inrichten van groepen.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
