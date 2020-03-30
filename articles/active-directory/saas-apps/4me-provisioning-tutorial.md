---
title: 'Zelfstudie: 4-inch configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor 4me.
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
ms.openlocfilehash: 423ba8c7aea9659a4c91f68a01392954c2ba6db2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059157"
---
# <a name="tutorial-configure-4me-for-automatic-user-provisioning"></a>Zelfstudie: 4 me configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in 4me en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen tot 4me.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een 4me huurder](https://www.4me.com/trial/)
* Een gebruikersaccount in 4me met beheerdersmachtigingen.

## <a name="add-4me-from-the-gallery"></a>4me toevoegen vanuit de galerie

Voordat u 4me configureert voor automatische gebruikersvoorziening met Azure AD, moet u 4me toevoegen uit de Azure AD-toepassingsgalerie aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om 4me toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **4me**in het zoekvak, selecteer **4me** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![4me in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-4me"></a>Gebruikers toewijzen aan 4me

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot 4me nodig hebben. Eenmaal besloten, u deze gebruikers en/ of groepen toewijzen aan 4me door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-4me"></a>Belangrijke tips voor het toewijzen van gebruikers aan 4me

* Het wordt aanbevolen dat één Azure AD-gebruiker aan 4me wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan 4me toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-4me"></a>Automatische gebruikersvoorziening configureren voor 4me 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in 4me te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on voor 4me in te schakelen, volgens de instructies in de [4me single sign-on tutorial](4me-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-4me-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor 4me in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **4me** in de lijst met toepassingen.

    ![De 4me-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Volg de walkthrough zoals beschreven in stap 6 om de **URL van tenant** en geheim **token** van uw 4me-account op te halen.

6. Meld u aan bij uw 4-me-beheerconsole. Navigeer naar **Instellingen**.

    ![4me-instellingen](media/4me-provisioning-tutorial/4me01.png)

    Typ **apps** in de zoekbalk.

    ![4me-apps](media/4me-provisioning-tutorial/4me02.png)

    Open de **vervolgkeuzelijst SCIM** om het Secret Token en het SCIM-eindpunt op te halen.

    ![4me SCIM](media/4me-provisioning-tutorial/4me03.png)

7. Klik op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met 4me wanneer u de velden in stap 5 bevolken. Als de verbinding mislukt, moet u ervoor zorgen dat uw 4-me-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer Azure **Active Directory Users synchroniseren tot 4me**onder de sectie **Toewijzingen** .

    ![4me Gebruikerstoewijzingen](media/4me-provisioning-tutorial/4me-user-mapping.png)
    
11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar 4me in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in 4me te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![4me Gebruikerstoewijzingen](media/4me-provisioning-tutorial/4me-user-attributes.png)
    
12. Selecteer Azure **Active Directory-groepen synchroniseren tot 4me**onder de sectie **Toewijzingen** .

    ![4me Gebruikerstoewijzingen](media/4me-provisioning-tutorial/4me-group-mapping.png)
    
13. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar 4me in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in 4me te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![4me-groepstoewijzingen](media/4me-provisioning-tutorial/4me-group-attribute.png)

14. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

15. Als u de Azure AD-inrichtingsservice voor 4me wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

16. Definieer de gebruikers en/of groepen die u aan 4me wilt inrichten door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

17. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op 4me.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Beperkingen voor connectoren

* 4me heeft verschillende SCIM-eindpunt-URL's voor test- en productieomgevingen. De eerste eindigt met **.qa,** terwijl de laatste eindigt met **.com**
* 4me gegenereerde Geheime Tokens hebben een vervaldatum van een maand van generatie.
* 4me ondersteunt geen **delete-bewerkingen**

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)