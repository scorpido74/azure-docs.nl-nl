---
title: 'Zelf studie: Cisco WebEx configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Cisco WebEx.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77058492"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Zelf studie: Cisco WebEx configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is om te demonstreren welke stappen moeten worden uitgevoerd in Cisco WebEx en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers naar Cisco WebEx.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector is momenteel beschikbaar als preview-versie. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een Cisco WebEx-Tenant](https://www.webex.com/pricing/index.html).
* Een gebruikers account in Cisco WebEx met beheerders machtigingen.

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco Webex toevoegen vanuit de galerie

Voordat u Cisco WebEx configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Cisco WebEx vanuit de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u Cisco WebEx wilt toevoegen vanuit de Azure AD-toepassings galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Cisco Webex** in het zoekvak, selecteer **Cisco Webex** in het resultatenvenster en klik op **Toevoegen** om de toepassing toe te voegen.

    ![Cisco Webex in de resultatenlijst](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Gebruikers toewijzen aan Cisco WebEx

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers, worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u bepalen welke gebruikers in azure AD toegang nodig hebben tot Cisco WebEx. Als u deze gebruikers eenmaal hebt vastgesteld, kunt u deze toewijzen aan Cisco WebEx door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Belang rijke tips voor het toewijzen van gebruikers aan Cisco WebEx

* Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan Cisco WebEx om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Cisco WebEx, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Automatische gebruikers inrichting configureren voor Cisco WebEx

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers in Cisco WebEx te maken, bij te werken en uit te scha kelen op basis van gebruikers toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Cisco WebEx in azure AD:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Cisco WebEx**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **Cisco Webex** in de lijst met toepassingen.

    ![De koppeling Cisco Webex in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Cisco WebEx-inrichting](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Cisco WebEx-inrichting](common/provisioning-automatic.png)

5. Voer in het gedeelte **beheerders referenties** de **Tenant-URL**en het **geheime token** van uw Cisco WebEx-account in.

    ![Cisco WebEx-inrichting](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  Voer in het veld **Tenant-URL** een waarde in de vorm van `https://api.ciscoweb.com/v1/scim/[OrgId]`in. Meld u `[OrgId]`aan bij uw [Cisco WebEx-besturings element](https://admin.webex.com/login)om het te verkrijgen. Klik linksonder op de naam van uw organisatie en kopieer de waarde van **organisatie-id**. 

    * Als u de waarde voor een **geheim token**wilt ophalen, gaat u naar deze [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Meld u op de pagina Web-aanmelding die wordt weer gegeven aan met het volledige Cisco WebEx-beheerders account voor uw organisatie. Er wordt een fout pagina weer gegeven met de melding dat de site niet kan worden bereikt, maar dit is normaal.

        ![Cisco WebEx-inrichting](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * Kopieer de waarde van het gegenereerde Bearer-token van de URL zoals hieronder is gemarkeerd. Dit token is 365 dagen geldig.
        
        ![Cisco WebEx-inrichting](./media/cisco-webex-provisioning-tutorial/test1.png)

7. Klik bij het invullen van de velden die worden weer gegeven in stap 5 op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Cisco WebEx. Als de verbinding mislukt, zorg er dan voor dat uw Cisco WebEx-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)
   
8. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Cisco WebEx**.

    ![Cisco WebEx-inrichting](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Cisco WebEx in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Cisco WebEx voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Cisco WebEx-inrichting](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD-inrichtings service voor Cisco WebEx wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Cisco WebEx door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Cisco WebEx.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

* Cisco WebEx bevindt zich momenteel in de EFT-fase (Early veld testing) van Cisco. Neem contact op met [het ondersteunings team van Cisco](https://www.webex.co.in/support/support-overview.html)voor meer informatie. 
* Raadpleeg de Cisco [-documentatie voor](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub)meer informatie over de Cisco WebEx-configuratie.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)