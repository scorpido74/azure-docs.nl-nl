---
title: 'Zelfstudie: Snowflake configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Snowflake.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063139"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Zelfstudie: Snowflake configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Snowflake en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Snowflake.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* [Een Snowflake huurder.](https://www.Snowflake.com/pricing/)
* Een gebruikersaccount in Snowflake met beheerdersmachtigingen.

## <a name="assigning-users-to-snowflake"></a>Gebruikers toewijzen aan Snowflake

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Snowflake nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Snowflake door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Belangrijke tips voor het toewijzen van gebruikers aan Snowflake

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Snowflake wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Snowflake toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="setup-snowflake-for-provisioning"></a>Setup Snowflake voor provisioning

Voordat u Snowflake configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-inrichting op Snowflake inschakelen.

1. Meld je aan bij je Snowflake Admin Console. Voer de onderstaande query in het gemarkeerde werkblad in en klik op **Uitvoeren**.

    ![Sneeuwvlok-beheerconsole](media/Snowflake-provisioning-tutorial/image00.png)

2.  Er wordt een SCIM Access Token gegenereerd voor uw Snowflake-tenant. Klik op de onderstaande link om deze op te halen.

    ![Snowflake Add SCIM](media/Snowflake-provisioning-tutorial/image01.png)

3. Kopieer de gegenereerde tokenwaarde en klik op **Gereed**. Deze waarde wordt ingevoerd in het veld **Geheim token** op het tabblad Provisioning van uw Snowflake-toepassing in de Azure-portal.

    ![Snowflake Add SCIM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Sneeuwvlok toevoegen uit de galerie

Als u Snowflake wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u Snowflake vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Snowflake toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Snowflake**in het zoekvak, selecteer **Snowflake** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Snowflake toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Automatische gebruikersvoorziening configureren voor Snowflake 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Snowflake te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on voor Snowflake in te schakelen, volgens de instructies in de [Snowflake Single sign-on tutorial](Snowflake-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Ga als een automatisch beheer van de gebruiker voor Snowflake in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Snowflake**in de lijst met toepassingen .

    ![De koppeling naar Snowflake in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://<Snowflake Account URL>/scim/v2` Beheerdersreferenties invoer in tenant-URL in. Een voorbeeld van de url van de tenant:`https://acme.snowflakecomputing.com/scim/v2`

6. Voer de waarde van de **SCIM-verificatietoken** in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Snowflake. Als de verbinding mislukt, moet u ervoor zorgen dat uw Snowflake-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer Azure **Active Directory-gebruikers synchroniseren met Snowflake**in de sectie **Toewijzingen.**

    ![Sneeuwvlok gebruikerstoewijzingen](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Snowflake in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Snowflake te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Voorkeuren voor sneeuwvlok](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. Selecteer Azure **Active Directory-groepen synchroniseren met Snowflake**onder de sectie **Toewijzingen** .

    ![Sneeuwvlok groep toewijzingen](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Snowflake in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in Snowflake te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Voorkeuren voor sneeuwvlokken](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

14. Als u de Azure AD-inrichtingsservice voor Snowflake wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

15. Definieer de gebruikers en/of groepen die u aan Snowflake wilt inrichten door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.** Als deze optie niet beschikbaar is, configureert u de vereiste velden onder Beheerdersreferenties op **Opslaan** en vernieuwen van de pagina. 

    ![Inrichtingskader](common/provisioning-scope.png)

16. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

    Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Snowflake.

    Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md) voor meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

* Snowflake gegenereerde SCIM tokens verlopen in 6 maanden. Houd er rekening mee dat deze moeten worden vernieuwd voordat ze verlopen om de inrichtende synchronisaties te laten werken. 

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md).
