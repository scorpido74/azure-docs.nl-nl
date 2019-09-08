---
title: 'Zelfstudie: De levens gebruiker configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts voor de levens gebruiker.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: e444d6254b575ead4b5d436a4b92f960c61b1d1c
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802873"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Zelfstudie: De levens gebruiker configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in de levens groep en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen voor de levens groep.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een Tenant van een Food](https://Foodee.com/pricing/)
* Een gebruikers account in de levens groep met beheerders machtigingen.

## <a name="assigning-users-to-foodee"></a>Gebruikers toewijzen aan de levens gebruiker 

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot de levens groep. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan de levens groep door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-foodee"></a>Belang rijke tips voor het toewijzen van gebruikers aan de levens gebruiker 

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan de levens eenheid om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan de genodigde, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-foodee-for-provisioning"></a>De levens gevoede voor het inrichten

Voordat u een levens gebruiker configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten inschakelen voor de levens middelen.

1. Meld u aan bij een [levens middel](https://www.food.ee/login/). Klik op uw **Tenant-id**

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

2. Selecteer in Enter prise Portal de optie **eenmalige aanmelding**>.

    ![Foodee](media/Foodee-provisioning-tutorial/scim.png)

3. Kopieer het **API-token**. Deze waarden worden ingevoerd in het veld **geheime token** op het tabblad inrichten van uw toepassing in de Food in de Azure Portal.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)


## <a name="add-foodee-from-the-gallery"></a>Levens middelen toevoegen vanuit de galerie

Als u een levens gebruiker wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u de levens middelen van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om een levens middel toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De Azure Active Directory-knop](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Enterprise-toepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak een **voeding**in, selecteer de optie **levens middelen** in het paneel resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![De levens middelen in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-foodee"></a>Automatische gebruikers inrichting configureren voor de levens gebruiker  

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in de levens groep te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groepeer toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor de levens middelen, gevolgd door de instructies in de [zelf studie voor eenmalige aanmelding](Foodee-tutorial.md)in de levens werk. Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="to-configure-automatic-user-provisioning-for-foodee-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor de levens gebruiker in azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **eten**.

    ![De koppeling van de levens middelen in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. In het gedeelte beheerders referenties voert u de ` https://concierge.food.ee/scim/v2` token waarden voor en **API** in die respectievelijk eerder zijn opgehaald in de **Tenant-URL** en het **geheime token** . Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met de levens gevoeder. Als de verbinding mislukt, zorg er dan voor dat uw levensmiddelen account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mailmelding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met de levens gebruiker**.

    ![Gebruikers toewijzingen van een gebruiker](media/Foodee-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd van Azure AD naar de levens gebruiker in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in de levens werk voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikers kenmerken van de levensmiddelen gebruiker](media/Foodee-provisioning-tutorial/userattribute.png)

10. Selecteer in de sectie **toewijzingen** de optie * * Azure Active Directory groepen synchroniseren met de **levens** groep

    ![Gebruikers kenmerken van de levensmiddelen gebruiker](media/Foodee-provisioning-tutorial/groupmapping.png)

11. Controleer de gebruikers kenmerken die zijn gesynchroniseerd van Azure AD naar de levens gebruiker in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de groeps accounts in de levens werk voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikers kenmerken van de levensmiddelen gebruiker](media/Foodee-provisioning-tutorial/groupattribute.png)

12. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD-inrichtings service wilt inschakelen voor de levens afdeling, wijzigt **u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor de levens groep door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan bij de volgende synchronisaties. Voor meer informatie over hoe lang het duurt voor het inrichten van gebruikers en/of groepen, raadpleegt u [hoe lang het duurt om gebruikers](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)in te richten.

U kunt de **huidige status** sectie gebruiken om de voortgang te bewaken en koppelingen naar uw inrichtings activiteiten rapport te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service voor de levens werk. Zie [de status van gebruikers inrichten controleren](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie. Zie [rapportage over het automatisch inrichten van gebruikers accounts](../manage-apps/check-status-user-account-provisioning.md)voor informatie over het vastleggen van Azure AD-inrichtings Logboeken.



## <a name="additional-resources"></a>Aanvullende resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)
