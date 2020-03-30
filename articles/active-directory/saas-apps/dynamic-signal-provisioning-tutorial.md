---
title: 'Zelfstudie: Dynamisch signaal configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Dynamic Signal.
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
ms.openlocfilehash: 2ec91d42dff8f3a1fc4b036aa1c3ec77faf6a0fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058039"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Zelfstudie: Dynamisch signaal configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Dynamic Signal en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Dynamisch signaal.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een dynamic signal tenant](https://dynamicsignal.com/)
* Een gebruikersaccount in Dynamic Signal met beheerdersmachtigingen.

## <a name="add-dynamic-signal-from-the-gallery"></a>Dynamisch signaal toevoegen vanuit de galerie

Voordat u Dynamisch signaal configureert voor automatische gebruikersvoorziening met Azure AD, moet u Dynamisch signaal uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om dynamisch signaal toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **dynamisch signaal**in het zoekvak, selecteer **Dynamisch signaal** in het deelvenster Resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Dynamic Signal in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Gebruikers toewijzen aan Dynamisch signaal

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Dynamisch signaal nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Dynamic Signal door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Belangrijke tips voor het toewijzen van gebruikers aan Dynamic Signal

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Dynamic Signal om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Dynamisch signaal toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Automatische gebruikersvoorziening configureren voor Dynamisch signaal 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Dynamisch signaal te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor Dynamic Signal, volgens de instructies in de [zelfstudie voor eenmalig aanmelden van Dynamic Signal.](dynamicsignal-tutorial.md) Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor Dynamisch signaal in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Dynamic Signal** in de lijst met toepassingen.

    ![De koppeling naar Dynamic Signal in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** de **URL van de tenant** en het geheime **token** van het account van uw dynamisch signaal in zoals beschreven in stap 6.

6. Navigeer in de beheerconsole Dynamisch signaal naar **Beheerder > Geavanceerde > API**.

    ![Dynamische signaalvoorziening](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    Kopieer de URL van de **SCIM API** naar **tenant-URL**. Klik op **Nieuw token genereren** om een token aan **toonder** te genereren en kopieer de waarde naar **Secret Token.**

    ![Dynamische signaalvoorziening](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. Klik op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Dynamisch signaal wanneer u de velden in stap 5 bevolken. Als de verbinding mislukt, moet u ervoor zorgen dat uw Dynamic Signal-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer Azure **Active Directory-gebruikers synchroniseren met Dynamisch signaal**onder de sectie **Toewijzingen** .

    ![Dynamische signaalgebruikerstoewijzingen](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Dynamisch signaal in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Dynamisch signaal te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van dynamische signaalgebruikers](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Dynamisch signaal wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten op Dynamisch signaal door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Dynamisch signaal.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Beperkingen voor connectoren

* Dynamic Signal biedt geen ondersteuning voor permanente verwijderingen van gebruikers uit Azure AD. Als u een gebruiker permanent wilt verwijderen in Dynamic Signal, moet de bewerking worden uitgevoerd via de gebruikersinterface van de Dynamische signaalbeheerder. 
* Dynamic Signal ondersteunt momenteel geen groepen.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

