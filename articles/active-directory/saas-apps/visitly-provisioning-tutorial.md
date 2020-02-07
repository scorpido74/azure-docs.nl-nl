---
title: 'Zelf studie: configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en inrichten van gebruikers accounts.
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
ms.openlocfilehash: 73cc1a58689db7902843f222aa4874a5e188be44
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063153"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Zelf studie: voor het automatisch inrichten van gebruikers naar een andere configuratie

Het doel van deze zelf studie is het demonstreren van de stappen die u in een stap en Azure Active Directory (Azure AD) uitvoert om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers of groepen.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen [gebruikers automatisch inrichten en ongedaan maken van de inrichting van SaaS-toepassingen (Software-as-a-Service) met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een bezochte Tenant](https://www.visitly.io/pricing/)
* Een gebruikers account met beheerders machtigingen

## <a name="assign-users-to-visitly"></a>Gebruikers aan een bezoek toewijzen 

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u het automatisch inrichten van gebruikers configureert en inschakelt, moet u bepalen welke gebruikers of groepen in azure AD u wilt bezoeken. Wijs deze gebruikers of groepen vervolgens door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Belang rijke tips voor het naar een andere gebruiker toewijzen van gebruikers 

* We raden u aan één Azure AD-gebruiker toe te wijzen om de automatische gebruikers inrichtings configuratie te testen. Extra gebruikers of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst om deze te openen, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol standaard toegang worden uitgesloten van het inrichten.

## <a name="set-up-visitly-for-provisioning"></a>Ga naar de inrichting instellen

Voordat u voor het eerst de configuratie van automatische gebruikers met Azure AD configureert, moet u het systeem voor het inrichten van SCIM (Cross-Domain Identity Management) op een bezoek brengen.

1. Meld u aan om te [bezoeken](https://app.visitly.io/login). Selecteer **integraties** > **synchronisatie**van de host.

    ![Host-synchronisatie](media/Visitly-provisioning-tutorial/login.png)

2. Selecteer de sectie **Azure AD** .

    ![Sectie over Azure AD](media/Visitly-provisioning-tutorial/integration.png)

3. Kopieer de **API-sleutel**. Deze waarden worden ingevoerd in het vak **geheime token** op het tabblad **inrichten** van uw bezochte toepassing in de Azure Portal.

    ![API-sleutel](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Vanuit de galerie een bezoek toevoegen

Als u wilt configureren voor automatische gebruikers inrichting met Azure AD, kunt u een bezoek brengen vanuit de Azure AD-toepassings galerie naar uw lijst met beheerde SaaS-toepassingen.

Als u een bezoek wilt toevoegen vanuit de Azure AD-toepassings galerie, voert u de volgende stappen uit.

1. Selecteer **Azure Active Directory**in de [Azure Portal](https://portal.azure.com)in het navigatie deel venster links.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Ga naar**, ga **naar** de slag paneel en selecteer vervolgens **toevoegen** om de toepassing toe te voegen.

    ![Visitly in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Automatisch door gebruikers inrichten configureren om te bezoeken 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers of groepen in een bezoek te maken, bij te werken en uit te scha kelen op basis van gebruikers-of groeps toewijzingen in azure AD.

> [!TIP]
> Als u eenmalige aanmelding op basis van SAML voor bezoek wilt inschakelen, volgt u de instructies in de [zelf studie eenmalige aanmelding bekijken](Visitly-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Automatisch gebruikers inrichten configureren voor bezoek aan Azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen** > **alle toepassingen**.

    ![Alle toepassingen](common/enterprise-applications.png)

2. Selecteer **Visitly** in de lijst met toepassingen.

    ![De Visitly-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Inrichtings modus ingesteld op automatisch](common/provisioning-automatic.png)

5. Geef in de sectie beheerders referenties de `https://api.visitly.io/v1/usersync/SCIM`-en **API-sleutel** waarden op die respectievelijk eerder zijn opgehaald in de **Tenant-URL** en het **geheime token**. Selecteer **verbinding testen** om ervoor te zorgen dat Azure AD kan worden verbonden met een bezoek. Als de verbinding mislukt, moet u ervoor zorgen dat uw account voor beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het vak **e-mail bericht** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen. Schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **synchronisatie van Azure Active Directory gebruikers om te bezoeken**.

    ![Gebruikers toewijzingen voor het bezoek](media/visitly-provisioning-tutorial/usermapping.png)

9. Bekijk de gebruikers kenmerken die door Azure AD worden gesynchroniseerd om te bezoeken in de sectie **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de gebruikers accounts in een bezoek te brengen aan update bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikers kenmerken voor het bezoek](media/visitly-provisioning-tutorial/userattribute.png)

10. Als u bereik filters wilt configureren, volgt u de instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik.

11. Als u de Azure AD-inrichtings service voor het bezoek wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers of groepen die u wilt inrichten door te klikken op de gewenste waarden in het **bereik** in het gedeelte **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan bij de volgende synchronisaties. Voor meer informatie over hoe lang het duurt voor het inrichten van gebruikers of groepen, raadpleeg dan [hoe lang het duurt om gebruikers in te richten?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

U kunt de **huidige status** sectie gebruiken om de voortgang te controleren en koppelingen naar uw inrichtings activiteiten rapport te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service. Zie [de status van gebruikers inrichten controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie. Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor informatie over het vastleggen van Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

Biedt geen ondersteuning voor harde verwijderingen. Alles is alleen zacht verwijderen.

## <a name="additional-resources"></a>Aanvullende resources

* [Het inrichten van gebruikers accounts beheren voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
