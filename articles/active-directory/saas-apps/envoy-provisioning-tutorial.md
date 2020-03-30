---
title: 'Zelfstudie: Envoy configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen aan Envoy.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 30faae80f1af4ff63924a76b26a03b8fe354a7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058022"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Zelfstudie: Envoy configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Envoy en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch te voorzien en te de-provisionen aan Envoy.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een huurder van de Gezant](https://envoy.com/pricing/)
* Een gebruikersaccount in Envoy met beheerdersmachtigingen.

## <a name="add-envoy-from-the-gallery"></a>Gezant toevoegen van de galerij

Voordat u Envoy configureert voor automatische gebruikersvoorziening met Azure AD, moet u Envoy uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om gezant toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Envoy**in, selecteer **Envoy** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Envoy in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-envoy"></a>Gebruikers toewijzen aan gezant

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersvoorzieningen configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Envoy nodig hebben. Eenmaal besloten, u deze gebruikers en / of groepen toewijzen aan Envoy door het volgen van de instructies hier:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-envoy"></a>Belangrijke tips voor het toewijzen van gebruikers aan Envoy

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Envoy wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Envoy toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-envoy"></a>Automatische gebruikersvoorziening configureren voor Envoy 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Envoy te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on voor Envoy in te schakelen, volgens de instructies in de [Envoy single sign-on tutorial](envoy-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersvoorziening voor Envoy in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Envoy**.

    ![De Envoy-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://app.envoy.com/scim/v2` **Beheerdersreferenties** invoer in **Tenant-URL**in . Volg de walkthrough zoals beschreven in stap 6 om het **geheime token** van je Envoy-account op te halen.

6. Meld u aan bij uw [envoy admin console](https://dashboard.envoy.com/login). Klik op **Integraties**.

    ![Gezant Integraties](media/envoy-provisioning-tutorial/envoy01.png)

    Klik op **Installeren** voor de **Microsoft Azure SCIM-integratie.**

    ![Envoy installeren](media/envoy-provisioning-tutorial/envoy02.png)

    Klik op **Opslaan** voor **synchronisatie van alle gebruikers.** 

    ![Gezant Opslaan](media/envoy-provisioning-tutorial/envoy03.png)

    Haal het geheime token gevuld op.
    
    ![Gezant OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

7. Klik op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Envoy wanneer u de velden in stap 5 bevolken. Als de verbinding mislukt, moet u ervoor zorgen dat uw Envoy-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer Azure **Active Directory-gebruikers synchroniseren met Envoy**in de sectie **Toewijzingen.**
    
    ![Envoy Gebruikerskenmerken](media/envoy-provisioning-tutorial/envoy-user-mappings.png)
    
11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Envoy in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Envoy voor updatebewerkingen te matchen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Envoy Gebruikerskenmerken](media/envoy-provisioning-tutorial/envoy-user-attribute.png)

12. Selecteer Azure **Active Directory-groepen synchroniseren met Envoy**onder de sectie **Toewijzingen** .

    ![Envoy User Mappings](media/envoy-provisioning-tutorial/envoy-group-mapping.png)

13. Bekijk de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Envoy in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in Envoy voor updatebewerkingen te matchen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Envoy User Mappings](media/envoy-provisioning-tutorial/envoy-group-attributes.png)
    
14. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

15. Als u de Azure AD-inrichtingsservice voor Envoy wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

16. Definieer de gebruikers en/of groepen die u aan Envoy wilt inrichten door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

17. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Envoy.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

