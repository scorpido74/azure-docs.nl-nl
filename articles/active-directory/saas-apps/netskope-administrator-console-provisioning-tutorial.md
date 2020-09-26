---
title: 'Zelf studie: Netskope-beheer console configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts naar de Netskope-beheerders console.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: b4ac2308eae3466dbb9d68895bca4a4de30fcebc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91304921"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Zelf studie: Netskope-beheer console configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in de Netskope-beheer console en de Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in Netskope-beheer console.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een Netskope-beheerders console Tenant](https://www.netskope.com/)
* Een gebruikers account in Netskope Administrator-console met beheerders machtigingen.

## <a name="assigning-users-to-netskope-administrator-console"></a>Gebruikers toewijzen aan Netskope Administrator-console

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot de Netskope-beheerders console. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan de Netskope Administrator-console door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Belang rijke tips voor het toewijzen van gebruikers aan de Netskope-beheer console

* Het is raadzaam dat er één Azure AD-gebruiker wordt toegewezen aan de Netskope-beheer console om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Netskope Administrator-console, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Netskope Administrator-console instellen voor inrichting

1. Meld u aan bij de [beheer console van uw Netskope-beheer console](https://netskope.goskope.com/). Navigeer naar **instellingen voor Home->**.

    ![Beheer console van Netskope-beheer console](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navigeer naar **Hulpprogram ma's**. Ga in het menu **extra** naar **Directory-HULPPROGRAM ma's > scim-integratie**.

    ![Hulpprogram ma's voor Netskope-beheer console](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope-beheer console SCIM toevoegen](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Schuif omlaag en klik op de knop **token toevoegen** . Geef in het dialoog venster **OAuth-client naam toevoegen** een **client naam** op en klik op de knop **Opslaan** .

    ![Netskope-beheer console-token toevoegen](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![CLient naam Netskope-Administrator-console](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Kopieer de **URL** van de scim-server en het **token**. Deze waarden worden respectievelijk in de velden Tenant-URL en geheim vermeld op het tabblad inrichten van uw Netskope-beheer console toepassing in de Azure Portal.

    ![Token Netskope-beheer console maken](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Netskope Administrator-console toevoegen vanuit de galerie

Voordat u de Netskope-beheer console configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u de Netskope-beheer console vanuit de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om de Netskope-beheer console toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Netskope Administrator-console**in, selecteer **Netskope Administrator console** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Netskope Administrator-console in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Automatische gebruikers inrichting configureren voor de Netskope-beheer console 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Netskope-beheer console te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor de Netskope-beheer console met behulp van de instructies in de [zelf studie Netskope Administrator-console voor eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

> [!NOTE]
> Raadpleeg voor meer informatie over het SCIM-eind punt [van de Netskope](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)-beheer console.

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor de Netskope-beheer console in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Netskope Administrator Console** in de lijst met toepassingen.

    ![De koppeling Netskope Administrator console in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Scherm opname van de opties voor beheer met de inrichtings optie.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Scherm afbeelding van de vervolg keuzelijst voor de inrichtings modus met de automatische optie aangeroepen.](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de waarde voor de **scim-server-URL** die eerder is opgehaald in de Tenant- **URL**. Voer de **token** waarde in die eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met de Netskope-beheer console. Als de verbinding mislukt, zorgt u ervoor dat het account van de Netskope-beheerders console beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Netskope Administrator-console**.

    ![Gebruikers toewijzingen van Netskope-beheer console](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar de Netskope-beheer console in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Netskope Administrator-console voor update-bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikers kenmerken van Netskope-beheer console](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Netskope Administrator-console**.

    ![Netskope Administrator-console groeps toewijzingen](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar de Netskope-beheer console in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt voor het vergelijken van de groepen in de Netskope-beheer console voor update bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Netskope-beheer console-groeps kenmerken](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor Netskope-beheer console wilt inschakelen, wijzigt **u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor de Netskope-beheerders console door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op de Netskope-beheer console.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

