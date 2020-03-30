---
title: 'Zelfstudie: Dialpad configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen naar Dialpad.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058339"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Zelfstudie: Dialpad configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Dialpad en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen naar Dialpad.

> [!NOTE]
>  In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

> Deze connector bevindt zich momenteel in Voorbeeld. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* [Een Dialpad-tenant.](https://www.dialpad.com/pricing/)
* Een gebruikersaccount in Dialpad met beheerdersmachtigingen.

## <a name="assign-users-to-dialpad"></a>Gebruikers toewijzen aan dialpad
Azure Active Directory gebruikt een concept genaamd toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Dialpad nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Dialpad door de instructies hier te volgen:
 
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Belangrijke tips voor het toewijzen van gebruikers aan Dialpad

 * Het wordt aanbevolen dat één Azure AD-gebruiker aan Dialpad wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Dialpad toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten.

## <a name="setup-dialpad-for-provisioning"></a>Dialpad instellen voor inrichten

Voordat u Dialpad configureert voor automatische gebruikersvoorziening met Azure AD, moet u bepaalde informatie ophalen van dialpad.

1. Meld u aan bij de [beheerconsole van](https://dialpadbeta.com/login) uw Dialpad en selecteer **Beheerinstellingen**. Controleer of **Mijn bedrijf** is geselecteerd in de vervolgkeuzelijst. Navigeer naar **Verificatie > API-sleutels**.

    ![Dialpad SCIM toevoegen](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Genereer een nieuwe sleutel door op **Een sleutel toevoegen** te klikken en de eigenschappen van uw geheime token te configureren.

    ![Dialpad SCIM toevoegen](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Dialpad SCIM toevoegen](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Klik **op** de knop Klik om de waarde weer te geven voor uw onlangs gemaakte API-sleutel en kopieer de weergegeven waarde. Deze waarde wordt ingevoerd in het veld **Geheim token** op het tabblad Provisioning van uw Dialpad-toepassing in de Azure-portal. 

    ![Kiespad Token maken](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Dialpad toevoegen vanuit de galerie

Als u Dialpad wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u Dialpad uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u Dialpad wilt toevoegen vanuit de Azure AD-toepassingsgalerie, voert u de volgende stappen uit:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Dialpad**in het zoekvak , selecteer **Dialpad** in het resultatenpaneel.
    ![Kiesblok in de resultatenlijst](common/search-new-app.png)

5. Navigeer naar de **URL** die hieronder in een aparte browser is gemarkeerd. 

    ![Dialpad SCIM toevoegen](media/dialpad-provisioning-tutorial/dialpad05.png)

6. Selecteer **Rechtsboven log in > Gebruik Dialpad online.**

    ![Dialpad SCIM toevoegen](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Aangezien Dialpad een OpenIDConnect-app is, kiest u ervoor om met uw Microsoft-werkaccount in te loggen op Dialpad.

    ![Dialpad SCIM toevoegen](media/dialpad-provisioning-tutorial/loginpage.png)

8. Na een succesvolle verificatie accepteert u de toestemmingsprompt voor de toestemmingspagina. De applicatie wordt dan automatisch toegevoegd aan uw tenant en u wordt doorgestuurd naar uw Dialpad-account.

    ![Dialpad SCIM toevoegen](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Automatische gebruikersvoorziening configureren naar Dialpad

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Dialpad te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Ga als een automatisch beheer van de gebruiker voor Dialpad in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Dialpad**in de lijst met toepassingen .

    ![De koppeling Dialpad in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://dialpad.com/scim` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer de waarde in die u eerder hebt opgehaald en opgeslagen vanaf Dialpad in **Secret Token**. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Dialpad. Als de verbinding mislukt, moet u ervoor zorgen dat uw Dialpad-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Dialpad**onder de sectie **Toewijzingen** .

    ![Gebruikerstoewijzingen voor dialpad](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Dialpad in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Dialpad te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Gebruikerskenmerken van dialpad](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

11. Als u de Azure AD-inrichtingsservice voor Dialpad wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten op Dialpad door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op dialpad.

Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md) voor meer informatie over het lezen van de azure AD-inrichtingslogboeken.
##  <a name="connector-limitations"></a>Verbindingsbeperkingen
* Dialpad ondersteunt vandaag de dag geen groepsnamen. Dit betekent dat wijzigingen in de **weergaveNaam** van een groep in Azure AD niet worden bijgewerkt en weergegeven in Dialpad.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
