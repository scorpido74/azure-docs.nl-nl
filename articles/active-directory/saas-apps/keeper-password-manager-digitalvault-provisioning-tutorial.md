---
title: 'Zelf studie: keeper Password Manager & Digital kluis configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts voor wachtwoord beheer & digitale kluis.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: a9ca77a059625ace13e6798e3fde84f11f3fe1db
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546784"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Zelf studie: keeper Password Manager & Digital kluis configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in keeper Password Manager & Digital-kluis en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen voor wachtwoord beheer & digitale kluis.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een keeper-wachtwoord beheerder & Digital kluis-Tenant](https://keepersecurity.com/pricing.html?t=e)
* Een gebruikers account in keeper Password Manager & Digital kluis met beheerders machtigingen.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Bewaar wachtwoord beheer & digitale kluis toevoegen vanuit de galerie

Voordat u keeper-wachtwoord beheer configureert & digitale kluis voor automatische gebruikers inrichting met Azure AD, moet u keeper Password Manager & Digital kluis van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om keeper Password Manager & Digital kluis toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **keeper password manager & Digital kluis**in, selecteer **wachtwoord beheer & digitale kluis** in het paneel resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Keeper Password Manager & Digital Vault toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Gebruikers toewijzen aan keeper Password Manager & Digital-kluis

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot wacht woord beheer & digitale kluis. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan keeper Password Manager & Digital kluis door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Belang rijke tips voor het toewijzen van gebruikers aan keeper Password Manager & Digital kluis

* Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan keeper Password Manager & Digital-kluis om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst voor wachtwoord beheer & digitale kluis, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Automatische gebruikers inrichting configureren voor wachtwoord beheer & digitale kluis 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service voor het maken, bijwerken en uitschakelen van gebruikers en/of groepen in keeper Password Manager & Digital-kluis op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor wachtwoord beheer & Digital-kluis, gevolgd door de instructies in de [hand leiding voor de wachtwoord beheerder voor eenmalige aanmelding van de keeper password manager &](keeperpasswordmanager-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Automatisch inrichten van gebruikers configureren voor wacht woord beheer & digitale kluis in azure AD:

1. Meld u aan bij de [Microsoft Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Keeper Password Manager & Digital Vault** in de lijst met toepassingen.

    ![De koppeling naar Keeper Password Manager & Digital Vault in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Voer in het gedeelte **beheerders referenties** de **Tenant-URL** en het **geheime token** van uw wacht woord beheerder in & Digital kluis account zoals beschreven in stap 6.

6. Meld u aan bij uw [keeper-beheer console](https://keepersecurity.com/console/#login). Klik op **beheerder** en selecteer een bestaand knoop punt of maak een nieuwe. Navigeer naar het tabblad **inrichten** en selecteer **methode toevoegen**.

    ![Keeper-beheer console](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selecteer **scim (systeem voor Cross-Domain Identity Management**.

    ![Keeper SCIM toevoegen](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Klik op **inrichtings token maken**.

    ![Eind punt voor het maken van een keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Kopieer de waarden voor **URL** en **token** en plak ze in de **Tenant-URL** en het **geheime token** in azure AD. Klik op **Opslaan** om de inrichtings configuratie voor de keeper te volt ooien.

    ![Token voor het maken van keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Klik bij het invullen van de velden die worden weer gegeven in stap 5 op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met keeper-wachtwoord beheer & digitale kluis. Als de verbinding mislukt, zorgt u ervoor dat uw wacht woord voor wachtwoord beheer & account voor de digitale kluis beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met wachtwoord beheer & digitale kluis**.

    ![Gebruikers toewijzingen voor keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar keeper Password Manager & Digital kluis in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in keeper Password Manager & Digital kluis voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerken van keeper-gebruikers](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met wachtwoord beheer & digitale kluis**.

    ![Toewijzing van keeper-groepen](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar keeper Password Manager & Digital kluis in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de groepen in keeper Password Manager & Digital kluis voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerken van keeper-groep](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

15. Als u de Azure AD-inrichtings service wilt inschakelen voor wachtwoord beheer & digitale kluis, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

16. Definieer de gebruikers en/of groepen die u wilt inrichten voor wachtwoord beheer & digitale kluis door de gewenste waarden in het **bereik** te kiezen in het gedeelte **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

17. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. Hiermee worden alle acties beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op wachtwoord beheer & digitale kluis.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

* Wacht woord beheer & Digital-kluis vereist dat **e-mail berichten** en **gebruikers naam** dezelfde bron waarde hebben, omdat alle wijzigingen in beide kenmerken de andere waarde wijzigen.
* Wachtwoord beheer van keeper & digitale kluis biedt geen ondersteuning voor het verwijderen van gebruikers, alleen uitschakelen. Uitgeschakelde gebruikers worden weer gegeven als vergrendeld in de gebruikers interface van de keeper-beheer console.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)

