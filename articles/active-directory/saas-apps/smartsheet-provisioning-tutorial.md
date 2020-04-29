---
title: 'Zelf studie: Smart Sheet configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Smart Sheet.
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063183"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Zelf studie: Smart Sheet configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Smart Sheet en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in Smart Sheet.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een Smart Sheet-Tenant](https://www.smartsheet.com/pricing)
* Een gebruikers account op een Smart Sheet Enter prise-of ENTER prise Premier-abonnement met systeembeheerders machtigingen.

## <a name="assign-users-to-smartsheet"></a>Gebruikers toewijzen aan Smart Sheet

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot Smart Sheet. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan Smart Sheet door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Belang rijke tips voor het toewijzen van gebruikers aan Smart Sheet

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan Smart sheet om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Smart Sheet, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

* Om ervoor te zorgen dat pariteit in gebruikersrol toewijzingen tussen Smart Sheet en Azure AD wordt gebruikt, is het raadzaam om dezelfde roltoewijzingen te gebruiken die zijn ingevuld in de volledige Smart Sheet-gebruikers lijst. Als u deze gebruikers lijst van Smart sheet wilt ophalen, gaat u naar **account beheerder > gebruikers beheer > meer acties > Download gebruikers lijst (CSV)**.

* Voor toegang tot bepaalde functies in de app vereist Smart Sheet dat een gebruiker meerdere rollen heeft. Ga voor meer informatie over gebruikers typen en machtigingen in Smart Sheet naar [gebruikers typen en machtigingen](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Als aan een gebruiker meerdere rollen zijn toegewezen in Smart Sheet, **moet** u ervoor zorgen dat deze roltoewijzingen worden gerepliceerd in azure AD om een scenario te vermijden waarin gebruikers de toegang tot Smart Sheet-objecten permanent kunnen verliezen. Elke unieke rol in Smart Sheet **moet** worden toegewezen aan een andere groep in azure AD. De gebruiker **moet** vervolgens worden toegevoegd aan elk van de groepen die overeenkomen met de gewenste rollen. 

## <a name="set-up-smartsheet-for-provisioning"></a>Smart Sheet instellen voor inrichting

Voordat u Smart Sheet configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten inschakelen op Smart Sheet.

1. Meld u aan als **sysadmin** in de **[Smart Sheet-Portal](https://app.smartsheet.com/b/home)** en navigeer naar **account beheerder**.

    ![Smart Sheet-account beheerder](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Ga naar **beveiligings controles > gebruikers automatisch inrichten > bewerken**.

    ![Smart Sheet-beveiligings controles](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Voeg en valideer de e-mail domeinen voor de gebruikers die u van plan bent vanuit Azure AD in te richten op Smart Sheet. Kies **niet ingeschakeld** om ervoor te zorgen dat alle inrichtings acties alleen afkomstig zijn van Azure AD en om er ook voor te zorgen dat uw Smart Sheet-gebruikers lijst wordt gesynchroniseerd met Azure AD-toewijzingen.

    ![Smart Sheet gebruikers inrichten](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Nadat de validatie is voltooid, moet u het domein activeren. 

    ![Domein Smart Sheet activeren](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Genereer het **geheime token** dat vereist is voor het configureren van automatische gebruikers inrichting met Azure AD door te navigeren naar **apps en integraties**.

    ![Smart Sheet-installatie](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Kies **API-toegang**. Klik op **nieuw toegangs token genereren**.

    ![Smart Sheet-installatie](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definieer de naam van het API-toegangs token. Klik op **OK**.

    ![Smart Sheet-installatie](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Kopieer het API-toegangs token en sla het op omdat dit de enige keer is dat u deze kunt bekijken. Dit is vereist in het veld **geheime token** in azure AD.

    ![Smart Sheet-token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Smart Sheet toevoegen vanuit de galerie

Als u Smart sheet wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Smart Sheet van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Smart Sheet**in het zoekvak en selecteer **Smart Sheet** in het deel venster resultaten. 

    ![Smart sheet in de lijst met resultaten](common/search-new-app.png)

5. Selecteer de knop **Aanmelden voor Smart Sheet** , waarmee u wordt doorgestuurd naar de aanmeldings pagina van Smart Sheet. 

    ![Smart Sheet OIDC toevoegen](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Als Smart Sheet een OpenIDConnect-app is, kiest u aanmelden bij Smart Sheet met uw micro soft-werk account.

    ![Smart Sheet OIDC-aanmelding](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Accepteer na een geslaagde verificatie de toestemming prompt voor de pagina toestemming. De toepassing wordt vervolgens automatisch toegevoegd aan uw Tenant en u wordt doorgestuurd naar uw Smart Sheet-account.

    ![Smart Sheet OIDc toestemming](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Automatische gebruikers inrichting configureren voor Smart Sheet 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Smart Sheet te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Smart sheet in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Smart Sheet**.

    ![De koppeling Smart sheet in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de `https://scim.smartsheet.com/v2/` invoer in de **Tenant-URL**. Voer de waarde in die u hebt opgehaald en eerder hebt opgeslagen uit Smart sheet in een **geheim token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Smart Sheet. Als de verbinding mislukt, zorg er dan voor dat uw Smart Sheet-account SysAdmin-machtigingen heeft en probeer het opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Smart Sheet**.

    ![Smart Sheet-gebruikers toewijzingen](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Smart sheet in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Smart Sheet voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Smart Sheet-gebruikers kenmerken](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

11. Als u de Azure AD-inrichtings service voor **Smart sheet wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor Smart Sheet door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Smart Sheet.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

* Smart Sheet biedt geen ondersteuning voor soft-verwijderingen. Wanneer het **actieve** kenmerk van een gebruiker is ingesteld op ONWAAR, wordt de gebruiker in Smart Sheet permanent verwijderd.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
