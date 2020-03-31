---
title: 'Zelfstudie: BlueJeans configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcb3fe009a6482c8e512899a952694beaed361a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058991"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Zelfstudie: BlueJeans configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in BlueJeans en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor BlueJeans.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al het volgende hebt:

* Een Azure AD-tenant
* Een BlueJeans huurder met het [My Company](https://www.BlueJeans.com/pricing) plan of beter ingeschakeld
* Een gebruikersaccount in BlueJeans met beheerdersmachtigingen

> [!NOTE]
> De Azure AD-integratie is gebaseerd op de [BlueJeans API](https://BlueJeans.github.io/developer), die beschikbaar is voor BlueJeans-teams op het standaard-abonnement of beter.

## <a name="adding-bluejeans-from-the-gallery"></a>BlueJeans toevoegen vanuit de galerie

Voordat u BlueJeans configureert voor automatische gebruikersvoorziening met Azure AD, moet u BlueJeans uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u BlueJeans wilt toevoegen vanuit de Azure AD-toepassingsgalerie, voert u de volgende stappen uit:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **BlueJeans**in het zoekvak, selecteer **BlueJeans** in het resultatenpaneel en selecteer vervolgens de knop **Toevoegen** om de toepassing toe te voegen.

    ![BlueJeans in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Gebruikers toewijzen aan BlueJeans

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot BlueJeans nodig hebben. Eenmaal besloten, u deze gebruikers en/ of groepen toewijzen aan BlueJeans door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Belangrijke tips voor het toewijzen van gebruikers aan BlueJeans

* Het wordt aanbevolen dat één Azure AD-gebruiker aan BlueJeans wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan BlueJeans toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Automatische gebruikersvoorziening configureren voor BlueJeans

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in BlueJeans te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor BlueJeans, volgens de instructies in de [BlueJeans single sign-on tutorial](bluejeans-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersinrichting voor BlueJeans in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens BlueJeans**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **BlueJeans** in de lijst met toepassingen.

    ![De koppeling BlueJeans in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Voer onder de sectie **Beheerdersreferenties** het **gebruikersnaam voor beheerders**en het **beheerderswachtwoord** van uw BlueJeans-account in. Voorbeelden van deze waarden zijn:

   * Vul in het veld **Gebruikersnaam beheerder** de gebruikersnaam van het beheerdersaccount in op uw BlueJeans-tenant. Bijvoorbeeld: admin@contoso.com.

   * Vul in het veld **Beheerderswachtwoord** het wachtwoord in dat overeenkomt met de gebruikersnaam van de beheerder.

6. Klik op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met BlueJeans wanneer u de velden in stap 5 bevolken. Als de verbinding mislukt, moet u ervoor zorgen dat uw BlueJeans-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Klik op **Opslaan**.

9. Selecteer Azure **Active Directory-gebruikers synchroniseren met BlueJeans**onder de sectie **Toewijzingen** .

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar BlueJeans in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in BlueJeans te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

12. Als u de Azure AD-inrichtingsservice voor BlueJeans wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Definieer de gebruikers en/of groepen die u aan BlueJeans wilt inrichten door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op BlueJeans.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Beperkingen voor connectoren

* Bluejeans staat geen gebruikersnamen toe die meer dan 30 tekens bevatten.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
