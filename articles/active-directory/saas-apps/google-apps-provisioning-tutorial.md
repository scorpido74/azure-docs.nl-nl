---
title: 'Zelfstudie: G Suite configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het automatisch inrichten en de-provisionen van gebruikersaccounts van Azure AD naar G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969a2fb5444ae8ece2aa302c04a5bbb85dcca917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057699"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Zelfstudie: G Suite configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in G Suite en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor G Suite.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> De G Suite-connector is onlangs bijgewerkt in oktober 2019. Wijzigingen in de G Suite-connector zijn onder andere:
> - Ondersteuning toegevoegd voor extra G Suite-gebruikers- en groepskenmerken. 
> - Bijgewerkte G Suite-doelkenmerkennamen die overeenkomen met wat [hier](https://developers.google.com/admin-sdk/directory)is gedefinieerd.
> - Standaardtoewijzingen voor kenmerken bijgewerkt.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met G Suite, hebt u de volgende items nodig:

- Een Azure AD-tenant
- [Een G Suite-tenant](https://gsuite.google.com/pricing.html)
- Een gebruikersaccount op een G Suite met beheerdersmachtigingen.

## <a name="assign-users-to-g-suite"></a>Gebruikers toewijzen aan G Suite

Azure Active Directory gebruikt een concept genaamd toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot G Suite nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan G Suite door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Belangrijke tips voor het toewijzen van gebruikers aan G Suite

* Het wordt aanbevolen dat één Azure AD-gebruiker aan G Suite wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan G Suite toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="setup-g-suite-for-provisioning"></a>Installatie G Suite voor inrichten

Voordat u G Suite configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-inrichting op G Suite inschakelen.

1. Meld u aan bij de [G Suite-beheerconsole](https://admin.google.com/) met uw beheerdersaccount en selecteer **Beveiliging**. Als u de koppeling niet ziet, wordt deze mogelijk verborgen onder het menu **Meer besturingselementen** onder aan het scherm.

    ![Selecteer beveiliging.][10]

1. Selecteer **API-verwijzing**op de pagina **Beveiliging** .

    ![Selecteer API-verwijzing.][15]

1. Selecteer **API-toegang inschakelen**.

    ![Selecteer API-verwijzing.][16]

   > [!IMPORTANT]
   > Voor elke gebruiker die u wilt inrichten op G Suite, **moet** zijn gebruikersnaam in Azure AD zijn gekoppeld aan een aangepast domein. Gebruikersnamen die eruit zien, bob@contoso.onmicrosoft.com worden bijvoorbeeld niet geaccepteerd door G Suite. Aan de andere bob@contoso.com kant, wordt geaccepteerd. U het domein van een bestaande gebruiker wijzigen door de instructies [hier te volgen.](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)

1. Nadat u de gewenste aangepaste domeinen hebt toegevoegd en geverifieerd met Azure AD, moet u deze opnieuw verifiëren met G Suite. Als u domeinen in G Suite wilt verifiëren, raadpleegt u de volgende stappen:

    a. Selecteer **Domeinen**in de [G Suite-beheerconsole](https://admin.google.com/).

    ![Domeinen selecteren][20]

    b. Selecteer **Een domein of een domeinalias toevoegen**.

    ![Een nieuw domein toevoegen][21]

    c. Selecteer **Een ander domein toevoegen**en typ vervolgens de naam van het domein dat u wilt toevoegen.

    ![Typ uw domeinnaam][22]

    d. Selecteer **Doorgaan en domeineigendom verifiëren.** Volg vervolgens de stappen om te controleren of u eigenaar bent van de domeinnaam. Zie [Uw site-eigendom verifiëren](https://support.google.com/webmasters/answer/35179)voor uitgebreide instructies over het verifiëren van uw domein met Google.

    e. Herhaal de voorgaande stappen voor eventuele extra domeinen die u wilt toevoegen aan G Suite.

1. Bepaal vervolgens welk beheerdersaccount u wilt gebruiken om de inrichting van gebruikers in G Suite te beheren. Navigeer naar **Beheerdersrollen**.

    ![Google-apps selecteren][26]

1. Bewerk de **bevoegdheden Bevoegdheden** voor die rol voor de **rol Beheerder** van dat account. Zorg ervoor dat u alle **beheerders-API-bevoegdheden** inschakelt, zodat dit account kan worden gebruikt voor inrichten.

    ![Google-apps selecteren][27]

## <a name="add-g-suite-from-the-gallery"></a>G Suite toevoegen vanuit de galerie

Als u G Suite wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u G Suite vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen. 

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

1. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

1. Typ G **Suite**in het zoekvak, selecteer **G Suite** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![G Suite in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Automatische gebruikersvoorziening configureren voor G Suite 

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in G Suite te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor G Suite, volgens de instructies in de [G Suite Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

> [!NOTE]
> Zie [Directory API](https://developers.google.com/admin-sdk/directory)voor meer informatie over het Directory API-eindpunt van G Suite.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor G Suite in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **G Suite**in de lijst met toepassingen .

    ![De koppeling G Suite in de lijst met toepassingen](common/all-applications.png)

1. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

1. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

1. Selecteer onder de sectie **Beheerdersreferenties** de optie **Autoriseren**. Het opent een dialoogvenster voor Google-autorisatie in een nieuw browservenster.

    ![G Suite Autoriseren](media/google-apps-provisioning-tutorial/authorize.png)

1. Controleer of u Azure AD-machtigingen wilt geven om wijzigingen aan te brengen in uw G Suite-tenant. Selecteer **Accepteren**.

    ![Machtigingen bevestigen.][28]

1. Selecteer in de Azure-portal **Testverbinding** om ervoor te zorgen dat Azure AD verbinding kan maken met uw app. Als de verbinding mislukt, moet u ervoor zorgen dat uw G Suite-account machtigingen voor teambeheer heeft. Probeer vervolgens de stap **Autoriseren** opnieuw.

1. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

1. Klik op **Opslaan**.

1. Selecteer Azure **Active Directory-gebruikers synchroniseren met G Suite**onder de sectie **Toewijzingen** .

    ![Gebruikerstoewijzingen van G Suite](media/google-apps-provisioning-tutorial/usermappings.png)

1. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar G Suite in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in G Suite te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Gebruikerskenmerken van G Suite](media/google-apps-provisioning-tutorial/userattributes.png)

1. Selecteer Azure **Active Directory-groepen synchroniseren met G Suite**onder de sectie **Toewijzingen** .

    ![G Suite-groepstoewijzingen](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar G Suite in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in G Suite te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren. De gebruikersinterface geeft de standaardset kenmerkentoewijzingen weer tussen Azure AD en G Suite. U ervoor kiezen om extra kenmerken toe te voegen, zoals Org Unit door op Nieuwe toewijzing toevoegen te klikken.

    ![G Suite-groepskenmerken](media/google-apps-provisioning-tutorial/groupattributes.png)

1. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

1. Als u de Azure AD-inrichtingsservice voor G Suite wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

1. Definieer de gebruikers en/of groepen die u aan G Suite wilt inrichten door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

1. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op G Suite.

> [!NOTE]
> Als de gebruikers al een bestaand persoonlijk/consumentenaccount hebben met behulp van het e-mailadres van de Azure AD-gebruiker, kan dit een probleem veroorzaken dat kan worden opgelost door het Google Transfer Tool te gebruiken voordat de directorysynchronisatie wordt uitgevoerd.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>Algemene problemen
* Autorisatiefouten kunnen optreden wanneer het account dat wordt gebruikt om een verbinding tot stand te brengen, niet voor een beheerder in GSuite is. Zorg ervoor dat het account dat wordt gebruikt om toegang te autoriseren beheerdersmachtigingen heeft voor **alle domeinen** waarmee gebruikers moeten worden ingericht. 
* Azure AD ondersteunt het uitschakelen van gebruikers in GSuite, zodat ze geen toegang hebben tot de toepassing, maar gebruikers in GSuite niet verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
