---
title: 'Zelf studie: G Suite configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts vanuit Azure AD naar G suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54b158528a67dfe77f33f41f3bb4b4570eb4c508
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802196"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Zelf studie: G Suite configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om te demonstreren welke stappen moeten worden uitgevoerd in G suite en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in G suite.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.

> [!NOTE]
> De G suite-connector is onlangs bijgewerkt op oktober 2019. De volgende wijzigingen zijn aangebracht in de G suite-connector:
- Er is ondersteuning toegevoegd voor aanvullende gebruikers-en groeps kenmerken van G suite. 
- De namen van de doel kenmerken van G suite zijn bijgewerkt zodat deze overeenkomen met wat [hier](/azure/active-directory/manage-apps/customize-application-attributes)is gedefinieerd.
- De standaard kenmerk toewijzingen zijn bijgewerkt.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met G Suite, hebt u de volgende items nodig:

- Een Azure AD-Tenant
- [Een G suite-Tenant](https://gsuite.google.com/pricing.html)
- Een gebruikers account op een G suite met beheerders machtigingen.

## <a name="assign-users-to-g-suite"></a>Gebruikers toewijzen aan G suite

Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot G suite. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan G suite door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Belang rijke tips voor het toewijzen van gebruikers aan G suite

* Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan G suite om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan G suite, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-g-suite-for-provisioning"></a>Installatie van G suite voor inrichting

Voordat u G suite configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten inschakelen op G suite.

1. Meld u aan bij de [G suite-beheer console](https://admin.google.com/) met uw beheerders account en selecteer vervolgens **beveiliging**. Als de koppeling niet wordt weer gegeven, kan deze worden verborgen onder het menu **meer besturings elementen** onder aan het scherm.

    ![Selecteer beveiliging.][10]

2. Selecteer op de pagina **beveiliging** **API-verwijzing**.

    ![Selecteer API-verwijzing.][15]

3. Selecteer **API-toegang inschakelen**.

    ![Selecteer API-verwijzing.][16]

   > [!IMPORTANT]
   > Voor elke gebruiker die u wilt inrichten voor G suite, **moet** de gebruikers naam in azure AD zijn gekoppeld aan een aangepast domein. Bijvoorbeeld: gebruikers namen die eruitzien als bob@contoso.onmicrosoft.com worden niet geaccepteerd door G suite. @No__t_0 wordt daarentegen geaccepteerd. U kunt een bestaand gebruikers domein wijzigen door de instructies [hier](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)te volgen.

4.  Nadat u de gewenste aangepaste domeinen hebt toegevoegd en geverifieerd met Azure AD, moet u ze opnieuw controleren met G suite. Raadpleeg de volgende stappen om domeinen in G suite te controleren:

    a. Selecteer in de [G suite-beheer console](https://admin.google.com/)de optie **domeinen**.

    ![Domeinen selecteren][20]

    b. Selecteer **een domein of domein alias toevoegen**.

    ![Een nieuw domein toevoegen][21]

    c. Selecteer **een ander domein toevoegen**en typ de naam van het domein dat u wilt toevoegen.

    ![Typ uw domein naam][22]

    d. Selecteer **door gaan en domein eigendom verifiëren**. Volg vervolgens de stappen om te controleren of u eigenaar bent van de domein naam. Zie [uw site eigendom controleren](https://support.google.com/webmasters/answer/35179)voor uitgebreide instructies voor het verifiëren van uw domein met Google.

    e. Herhaal de voor gaande stappen voor extra domeinen die u wilt toevoegen aan G suite.

5. Bepaal vervolgens welk beheerders account u wilt gebruiken voor het beheren van gebruikers inrichten in G suite. Navigeer naar **beheerders rollen**.

    ![Google Apps selecteren][26]
    
6. Voor de **beheerdersrol** van dat account bewerkt u de **bevoegdheden** voor die rol. Zorg ervoor dat u alle **beheer-API-bevoegdheden** inschakelt, zodat dit account kan worden gebruikt voor het inrichten.

    ![Google Apps selecteren][27]

## <a name="add-g-suite-from-the-gallery"></a>G suite toevoegen vanuit de galerie

Als u G Suite wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u G suite toevoegen vanuit de Azure AD-toepassings galerie aan uw lijst met beheerde SaaS-toepassingen. 

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **g Suite**in, selecteer **g Suite** in het paneel resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![G Suite in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Automatische gebruikers inrichting configureren voor G suite 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in G suite te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding voor G suite in te scha kelen, gevolgd door de instructies in de [zelf studie over het eenmalige aanmelden bij de G suite](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor G suite in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **G suite**.

    ![De koppeling G Suite in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer **machtigen**onder de sectie **beheerders referenties** . Er wordt een Google-autorisatie venster geopend in een nieuw browser venster.

    ![G suite autoriseren](media/google-apps-provisioning-tutorial/authorize.png)

6. Bevestig dat u Azure AD-machtigingen wilt verlenen om wijzigingen aan te brengen in uw G suite-Tenant. Selecteer **Accepteren**.

    ![Bevestig machtigingen.][28]

7. In de Azure Portal selecteert u **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw app. Als de verbinding mislukt, controleert u of uw G suite-account team beheerders machtigingen heeft. Probeer de stap vervolgens opnieuw te **autoriseren** .

8. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met G suite**.

    ![G Suite-gebruikers toewijzingen](media/google-apps-provisioning-tutorial/usermappings.png)

10. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar G suite in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in G suite voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![G Suite-gebruikers kenmerken](media/google-apps-provisioning-tutorial/userattributes.png)

11. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met G suite**.

    ![G suite-groeps toewijzingen](media/google-apps-provisioning-tutorial/groupmappings.png)

12. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar G suite in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt voor het vergelijken van de groepen in G suite voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerken G suite-groep](media/google-apps-provisioning-tutorial/groupattributes.png)

13. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

14. Als u de Azure AD-inrichtings service voor G Suite wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

15. Definieer de gebruikers en/of groepen die u wilt inrichten voor G suite door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen.

    ![Inrichtings bereik](common/provisioning-scope.png)

16. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op G suite.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../manage-apps/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

> [!NOTE]
> Een andere levensvat bare optie voor het automatiseren van de gebruikers inrichting van G suite is het gebruik van [Google Cloud Directory Sync](https://support.google.com/a/answer/106368?hl=en). Met deze optie worden uw on-premises Active Directory-identiteiten naar G suite ingericht.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)


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
