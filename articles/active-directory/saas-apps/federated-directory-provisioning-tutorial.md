---
title: 'Zelf studie: federatieve mappen configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts in een federatieve Directory.
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
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 910aaac84dacb75cd76772a0bc2960d9bfa8bb70
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77057899"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Zelf studie: federatieve mappen configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in federatieve Directory en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in federatieve Directory.

> [!NOTE]
>  In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een federatieve Directory](https://www.federated.directory/pricing).
* Een gebruikers account in federatieve map met beheerders machtigingen.

## <a name="assign-users-to-federated-directory"></a>Gebruikers toewijzen aan federatieve Directory
Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot federatieve Directory. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan de federatieve Directory door de volgende instructies te volgen:

 * [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Belang rijke tips voor het toewijzen van gebruikers aan federatieve mappen
 * U wordt aangeraden één Azure AD-gebruiker aan federatieve Directory toe te wijzen om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker aan een federatieve Directory toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol standaard toegang worden uitgesloten van het inrichten.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Federatieve Directory instellen voor inrichting

Voordat u de federatieve Directory configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten inschakelen voor de federatieve Directory.

1. Meld u aan bij uw [federatieve Directory-beheer console](https://federated.directory/of)

    ![Zelf studie voor federatieve mappen](media/federated-directory-provisioning-tutorial/companyname.png)

2. Navigeer naar **directory's > gebruikers directory's** en selecteer uw Tenant. 

    ![Federatieve Directory](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Als u een permanente Bearer-token wilt genereren, navigeert u naar **Directory sleutels > nieuwe sleutel maken.** 

    ![Federatieve Directory](media/federated-directory-provisioning-tutorial/federated01.png)

4. Een directory sleutel maken. 

    ![Federatieve Directory](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Kopieer de waarde van het **toegangs token** . Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van uw federatieve Directory-toepassing in de Azure Portal. 

    ![Federatieve Directory](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Federatieve Directory toevoegen vanuit de galerie

Als u de federatieve Directory wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u de federatieve Directory vanuit de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Federated Directory toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **federatieve Directory**in, selecteer **federatieve Directory** in het paneel resultaten.

    ![Federatieve map in de lijst met resultaten](common/search-new-app.png)

5. Navigeer naar de hieronder gemarkeerde **URL** in een afzonderlijke browser. 

    ![Federatieve Directory](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Klik op **Aanmelden**.

    ![Federatieve Directory](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Als federatieve Directory is een OpenIDConnect-app, kiest u zich aanmelden bij federatieve Directory met uw micro soft-werk account.
    
    ![Federatieve Directory](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Accepteer na een geslaagde verificatie de toestemming prompt voor de pagina toestemming. De toepassing wordt vervolgens automatisch toegevoegd aan uw Tenant en u wordt doorgestuurd naar uw federatieve Directory-account.

    ![Federatieve Directory SCIM toevoegen](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Automatische gebruikers inrichting configureren voor federatieve Directory 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in federatieve Directory te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor federatieve Directory in azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Federated Directory**.

    ![De koppeling federatieve Directory in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://api.federated.directory/v2/` in de Tenant-URL. Voer de waarde in die u hebt opgehaald en eerder hebt opgeslagen in federatieve Directory in een **geheim token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met de federatieve Directory. Als de verbinding mislukt, zorg er dan voor dat uw federatieve Directory-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met federatieve Directory**.

    ![Zelf studie voor federatieve mappen](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar federatieve Directory in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt voor het vergelijken van de gebruikers accounts in de federatieve Directory voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Zelf studie voor federatieve mappen](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD-inrichtings service voor federatieve mappen wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor federatieve Directory door de gewenste waarden in het **bereik** in de sectie **instellingen** te kiezen.

    ![Inrichtings bereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. Hiermee worden alle acties beschreven die worden uitgevoerd door de Azure AD Provisioning-Service in federatieve Directory.

Voor meer informatie over het lezen van de Azure AD-inrichtings logboeken raadpleegt u [rapportage over automatische gebruikers accounts inrichten](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
