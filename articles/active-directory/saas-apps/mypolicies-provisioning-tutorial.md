---
title: 'Zelf studie: myPolicies configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op myPolicies.
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
ms.openlocfilehash: ea9a0e52ce424459b6c402eb136d06dd370bab7d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548036"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Zelf studie: myPolicies configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in myPolicies en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in myPolicies.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een myPolicies-Tenant](https://mypolicies.com/index.html#section10).
* Een gebruikers account in myPolicies met beheerders machtigingen.

## <a name="assigning-users-to-mypolicies"></a>Gebruikers toewijzen aan myPolicies

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot myPolicies. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan myPolicies door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Belang rijke tips voor het toewijzen van gebruikers aan myPolicies

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan myPolicies om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan myPolicies, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-mypolicies-for-provisioning"></a>MyPolicies instellen voor inrichting

Voordat u myPolicies configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten inschakelen op myPolicies.

1. Neem contact op met uw myPolicies-vertegenwoordiger **support@mypolicies.com** om het geheime token te verkrijgen dat nodig is voor het configureren van scim-inrichting.

2.  Sla de token waarde op die door de myPolicies-vertegenwoordiger is verschaft. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van uw myPolicies-toepassing in de Azure Portal.

## <a name="add-mypolicies-from-the-gallery"></a>MyPolicies toevoegen vanuit de galerie

Als u myPolicies wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u myPolicies van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om myPolicies toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **myPolicies**in het zoekvak, selecteer **myPolicies** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![myPolicies in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Automatische gebruikers inrichting configureren voor myPolicies 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in myPolicies te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor myPolicies, gevolgd door de instructies in de [myPolicies-zelf studie voor eenmalige aanmelding](mypolicies-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor myPolicies in azure AD:

1. Meld u aan bij de [Microsoft Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **myPolicies**.

    ![De koppeling myPolicies in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://<myPoliciesCustomDomain>.mypolicies.com/scim` in de Tenant- **URL** waar `<myPoliciesCustomDomain>` het aangepaste domein van uw myPolicies is. U kunt uw myPolicies-klant domein ophalen uit uw URL.
Voor beeld: `<demo0-qa>` . mypolicies.com.

6. In een **geheim token**voert u de token waarde in die eerder is opgehaald. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met myPolicies. Als de verbinding mislukt, zorg er dan voor dat uw myPolicies-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met myPolicies**.

    ![myPolicies-gebruikers toewijzingen](media/mypolicies-provisioning-tutorial/usermapping.png)

10. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar myPolicies in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in myPolicies voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![myPolicies-gebruikers toewijzingen](media/mypolicies-provisioning-tutorial/userattribute.png)

11. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

12. Als u de Azure AD-inrichtings service voor **myPolicies wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

13. Definieer de gebruikers en/of groepen die u wilt inrichten voor myPolicies door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

14. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op myPolicies.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

* voor myPolicies is altijd **gebruikers naam**, **e-mail adres** en **externalId**vereist.
* myPolicies biedt geen ondersteuning voor harde verwijderingen voor gebruikers kenmerken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
