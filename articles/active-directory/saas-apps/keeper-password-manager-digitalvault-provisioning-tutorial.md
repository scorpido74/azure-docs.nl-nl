---
title: 'Zelfstudie: Keeper Password Manager & Digitale kluis configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Keeper Password Manager & Digital Vault.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 236527a9889879f872ef8c3867a7ec3c1b1ba0a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057512"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Zelfstudie: Keeper Password Manager & Digitale Kluis configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Keeper Password Manager & Digital Vault en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen naar Keeper Password Manager & Digital Vault.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Keeper Password Manager & Digital Vault-tenant](https://keepersecurity.com/pricing.html?t=e)
* Een gebruikersaccount in Keeper Password Manager & Digital Vault met beheerdersmachtigingen.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Keeper Password Manager & Digitale Kluis toevoegen vanuit de galerie

Voordat u Keeper Password Manager & Digital Vault configureert voor automatische gebruikersvoorziening met Azure AD, moet u Keeper Password Manager & Digital Vault vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Keeper Password Manager & Digital Vault toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Keeper Password Manager & Digital Vault,** selecteer Keeper Password Manager & Digital **Vault** in het deelvenster Resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Keeper Password Manager & Digital Vault toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Gebruikers toewijzen aan Keeper Password Manager & Digital Vault

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Keeper Password Manager & Digital Vault. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Keeper Password Manager & Digital Vault door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Belangrijke tips voor het toewijzen van gebruikers aan Keeper Password Manager & Digital Vault

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Keeper Password Manager & Digital Vault om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker toewijst aan Keeper Password Manager & Digital Vault, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Automatische gebruikersvoorziening configureren voor Keeper Password Manager & Digital Vault 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen te maken, bij te werken en uit te schakelen in Keeper Password Manager & Digital Vault op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor Keeper Password Manager & Digital Vault, volgens de instructies in de [Keeper Password Manager & Digital Vault single sign-on tutorial.](keeperpasswordmanager-tutorial.md) Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor Keeper Password Manager & Digital Vault in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Keeper Password Manager & Digital Vault** in de lijst met toepassingen.

    ![De koppeling naar Keeper Password Manager & Digital Vault in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** de **URL van de tenant** en het geheime **token** van uw Keeper Password Manager in & het account van Digital Vault zoals beschreven in stap 6.

6. Meld u aan bij [uw beheerdersconsole](https://keepersecurity.com/console/#login)voor beheerders . Klik op **Beheerder** en selecteer een bestaand knooppunt of maak een nieuw knooppunt. Navigeer naar het tabblad **Inrichten** en selecteer **Methode toevoegen**.

    ![Keeper Admin Console](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selecteer **SCIM (Systeem voor identiteitsbeheer tussen domeinen**.

    ![Keeper Add SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Klik **op Inrichtingstoken maken**.

    ![Eindpunt keeper maken](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Kopieer de waarden voor **URL** en **Token** en plak ze in **Tenant URL** en Secret **Token** in Azure AD. Klik **op Opslaan** om de inrichtingsinstelling op Keeper te voltooien.

    ![Token keeper maken](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Klik op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Keeper Password Manager & Digital Vault. Als de verbinding mislukt, moet u ervoor zorgen dat uw Keeper Password Manager & Digital Vault-account beheerdersmachtigingen heeft en het opnieuw probeert.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer onder de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Keeper Password Manager & Digital Vault.**

    ![Gebruikerstoewijzingen keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Keeper Password Manager & Digital Vault in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Keeper Password Manager & Digital Vault te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Gebruikerskenmerken keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Selecteer onder de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Keeper Password Manager & Digital Vault.**

    ![Keeper Group Mappings](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Keeper Password Manager & Digital Vault in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepen in Keeper Password Manager & Digital Vault te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van keepergroep](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

15. Als u de Azure AD-inrichtingsservice voor Keeper Password Manager & Digitale kluis wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

16. Definieer de gebruikers en/of groepen die u wilt inrichten aan Keeper Password Manager & Digital Vault door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

17. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiegegevens** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Keeper Password Manager & Digital Vault.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Beperkingen voor connectoren

* Keeper Password Manager & Digital Vault vereist **dat e-mails** en **userName** dezelfde bronwaarde hebben, omdat updates van beide kenmerken de andere waarde wijzigen.
* Keeper Password Manager & Digital Vault ondersteunt geen verwijderingen van gebruikers, alleen uitschakelen. Uitgeschakelde gebruikers worden weergegeven als vergrendeld in de gebruikersinterface van de Keeper Admin Console.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

