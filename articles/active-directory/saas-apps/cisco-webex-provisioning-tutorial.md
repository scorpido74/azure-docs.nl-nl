---
title: 'Zelfstudie: Cisco Webex configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Cisco Webex.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 0075783c049e7f48645f768026dd9d5ec0ead821
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77058492"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Zelfstudie: Cisco Webex configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Cisco Webex en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers automatisch te voorzien en te de-provisionenten voor Cisco Webex.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Voorbeeld. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* [Een Cisco Webex-tenant.](https://www.webex.com/pricing/index.html)
* Een gebruikersaccount in Cisco Webex met beheerdersmachtigingen.

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco Webex toevoegen vanuit de galerie

Voordat u Cisco Webex configureert voor automatische gebruikersvoorziening met Azure AD, moet u Cisco Webex vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u Cisco Webex wilt toevoegen vanuit de Azure AD-toepassingsgalerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Cisco Webex** in het zoekvak, selecteer **Cisco Webex** in het resultatenvenster en klik op **Toevoegen** om de toepassing toe te voegen.

    ![Cisco Webex in de resultatenlijst](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Gebruikers toewijzen aan Cisco Webex

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers in Azure AD toegang nodig hebben tot Cisco Webex. Eenmaal besloten, u deze gebruikers toewijzen aan Cisco Webex door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Belangrijke tips voor het toewijzen van gebruikers aan Cisco Webex

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Cisco Webex om de automatische configuratie van gebruikersinrichting te testen. Extra gebruikers kunnen later worden toegewezen.

* Wanneer u een gebruiker aan Cisco Webex toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Automatische gebruikersvoorziening configureren voor Cisco Webex

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers in Cisco Webex te maken, bij te werken en uit te schakelen op basis van gebruikerstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersinrichting voor Cisco Webex in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Cisco Webex**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **Cisco Webex** in de lijst met toepassingen.

    ![De koppeling Cisco Webex in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Cisco Webex-provisioning](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Cisco Webex-provisioning](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** de **URL tenant**en **geheim token** van uw Cisco Webex-account in.

    ![Cisco Webex-provisioning](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  Voer in het veld **URL van** tenant `https://api.ciscoweb.com/v1/scim/[OrgId]`een waarde in in de vorm van . Meld `[OrgId]`u aan bij uw [Cisco Webex Control Hub](https://admin.webex.com/login)om u aan te melden. Klik linksonder op de naam van uw organisatie en kopieer de waarde vanuit **organisatie-id.** 

    * Als u de waarde voor **Secret Token wilt**verkrijgen, navigeert u naar deze [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Meld u vanaf de webex-aanmeldingspagina die wordt weergegeven, aan met het volledige Cisco Webex-beheerdersaccount voor uw organisatie. Er verschijnt een foutpagina waarin staat dat de site niet bereikbaar is, maar dit is normaal.

        ![Cisco Webex-provisioning](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * Kopieer de waarde van het gegenereerde token aan de drager van de URL zoals hieronder gemarkeerd. Dit token is 365 dagen geldig.
        
        ![Cisco Webex-provisioning](./media/cisco-webex-provisioning-tutorial/test1.png)

7. Klik op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Cisco Webex wanneer u de velden in stap 5 bevolken. Als de verbinding mislukt, moet u ervoor zorgen dat uw Cisco Webex-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)
   
8. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer Azure **Active Directory-gebruikers synchroniseren met Cisco Webex**onder de sectie **Toewijzingen** .

    ![Cisco Webex-provisioning](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Cisco Webex in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Cisco Webex te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Cisco Webex-provisioning](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Cisco Webex wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Cisco Webex wilt inrichten door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Cisco Webex.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

* Cisco Webex bevindt zich momenteel in cisco's Early Field Testing (EFT) fase. Neem voor meer informatie contact op met [het ondersteuningsteam van Cisco.](https://www.webex.co.in/support/support-overview.html) 
* Voor meer informatie over Cisco Webex configuratie, verwijzen wij u naar de Cisco documentatie [hier](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)