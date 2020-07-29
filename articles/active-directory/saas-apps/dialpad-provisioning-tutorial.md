---
title: 'Zelf studie: dialpad configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op dialpad.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058339"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Zelf studie: dialpad configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in dialpad en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in dialpad.

> [!NOTE]
>  In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.

> Deze connector is momenteel beschikbaar als preview-versie. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een dialpad-Tenant](https://www.dialpad.com/pricing/).
* Een gebruikers account in dialpad met beheerders machtigingen.

## <a name="assign-users-to-dialpad"></a>Gebruikers toewijzen aan dialpad
Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot dialpad. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan dialpad door de volgende instructies te volgen:
 
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Belang rijke tips voor het toewijzen van gebruikers aan dialpad

 * U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan dialpad om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Dialpad, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol standaard toegang worden uitgesloten van het inrichten.

## <a name="setup-dialpad-for-provisioning"></a>Dialpad instellen voor inrichting

Voordat u dialpad configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u bepaalde inrichtings gegevens ophalen van dialpad.

1. Meld u aan bij de [dialpad-beheer console](https://dialpadbeta.com/login) en selecteer **beheer instellingen**. Controleer of **mijn bedrijf** is geselecteerd in de vervolg keuzelijst. Navigeer naar **Authentication > API-sleutels**.

    ![SCIM dialpad toevoegen](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Genereer een nieuwe sleutel door te klikken op **een sleutel toevoegen** en de eigenschappen van uw geheime token te configureren.

    ![SCIM dialpad toevoegen](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![SCIM dialpad toevoegen](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Klik op de knop **Klik om de waarde weer te geven** voor de API-sleutel die u hebt gemaakt en kopieer de weer gegeven waarde. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van uw dialpad-toepassing in de Azure Portal. 

    ![Dialpad-token maken](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Dialpad toevoegen vanuit de galerie

Als u dialpad wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u dialpad van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om dialpad toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **dialpad**in het zoekvak en selecteer **dialpad** in het deel venster resultaten.
    ![Dialpad in de lijst met resultaten](common/search-new-app.png)

5. Navigeer naar de hieronder gemarkeerde **URL** in een afzonderlijke browser. 

    ![SCIM dialpad toevoegen](media/dialpad-provisioning-tutorial/dialpad05.png)

6. Selecteer in de rechter bovenhoek **aanmelden > dialpad online gebruiken**.

    ![SCIM dialpad toevoegen](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Als dialpad een OpenIDConnect-app is, kiest u aanmelden bij dialpad met uw micro soft-werk account.

    ![SCIM dialpad toevoegen](media/dialpad-provisioning-tutorial/loginpage.png)

8. Accepteer na een geslaagde verificatie de toestemming prompt voor de pagina toestemming. De toepassing wordt vervolgens automatisch toegevoegd aan uw Tenant en u wordt doorgestuurd naar uw dialpad-account.

    ![SCIM dialpad toevoegen](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Automatische gebruikers inrichting configureren voor dialpad

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in dialpad te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor dialpad in azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **dialpad**.

    ![De koppeling dialpad in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://dialpad.com/scim` in de Tenant- **URL**. Voer de waarde in die u hebt opgehaald en eerder hebt opgeslagen uit dialpad in een **geheim token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met dialpad. Als de verbinding mislukt, zorg er dan voor dat uw dialpad-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met dialpad**.

    ![Dialpad-gebruikers toewijzingen](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar dialpad in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in dialpad voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Dialpad-gebruikers kenmerken](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

11. Als u de Azure AD-inrichtings service voor **dialpad wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor dialpad door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op dialpad.

Voor meer informatie over het lezen van de Azure AD-inrichtings logboeken raadpleegt u [rapportage over automatische gebruikers accounts inrichten](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Connector beperkingen
* Dialpad biedt geen ondersteuning voor de naam van een groep. Dit betekent dat wijzigingen in de **DisplayName** van een groep in azure ad niet worden bijgewerkt en niet worden weer gegeven in dialpad.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
