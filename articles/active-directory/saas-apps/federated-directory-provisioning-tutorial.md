---
title: 'Zelfstudie: Federatieve directory configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Federated Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057899"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Zelfstudie: Federatieve directory configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Federated Directory en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen naar Federated Directory.

> [!NOTE]
>  In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* [Een federatieve directory](https://www.federated.directory/pricing).
* Een gebruikersaccount in Federated Directory met beheerdersmachtigingen.

## <a name="assign-users-to-federated-directory"></a>Gebruikers toewijzen aan federated directory
Azure Active Directory gebruikt een concept genaamd toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Federated Directory. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Federated Directory door de instructies hier te volgen:

 * [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Belangrijke tips voor het toewijzen van gebruikers aan Federated Directory
 * Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Federated Directory om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Federated Directory toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Federatiedirectory instellen voor inrichten

Voordat u Federated Directory configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-inrichting op Federated Directory inschakelen.

1. Aanmelden bij uw [Federatieve Map-beheerconsole](https://federated.directory/of)

    ![Zelfstudie voor federatieve directory](media/federated-directory-provisioning-tutorial/companyname.png)

2. Navigeer naar **mappen > gebruikersmappen** en selecteer uw tenant. 

    ![federatieve map](media/federated-directory-provisioning-tutorial/ad-user-directories.png)

3.  Als u een permanente token aan toonder wilt genereren, navigeert u naar **DirectoryKeys > Nieuwe sleutel maken.** 

    ![federatieve map](media/federated-directory-provisioning-tutorial/federated01.png)

4. Maak een directorytoets. 

    ![federatieve map](media/federated-directory-provisioning-tutorial/federated02.png)
    

5. Kopieer de waarde **Access Token.** Deze waarde wordt ingevoerd in het veld **Geheim token** op het tabblad Provisioning van uw Federatiedirectorytoepassing in de Azure-portal. 

    ![federatieve map](media/federated-directory-provisioning-tutorial/federated03.png)
    
## <a name="add-federated-directory-from-the-gallery"></a>Federatieve map toevoegen vanuit de galerie

Als u Federated Directory wilt configureren voor automatische gebruikersinrichting met Azure AD, moet u Federated Directory toevoegen uit de Azure AD-toepassingsgalerie aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Federatieve directory toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **Federated**Directory in het zoekvak, selecteer **Federated Directory** in het deelvenster Resultaten.

    ![Federatieve directory in de lijst met resultaten](common/search-new-app.png)

5. Navigeer naar de **URL** die hieronder in een aparte browser is gemarkeerd. 

    ![federatieve map](media/federated-directory-provisioning-tutorial/loginpage1.png)

6. Klik **op Inloggen**.

    ![federatieve map](media/federated-directory-provisioning-tutorial/federated04.png)

7.  Aangezien Federated Directory een OpenIDConnect-app is, kiest u ervoor om in te loggen bij Federated Directory met uw Microsoft-werkaccount.
    
    ![federatieve map](media/federated-directory-provisioning-tutorial/loginpage3.png)
 
8. Na een succesvolle verificatie accepteert u de toestemmingsprompt voor de toestemmingspagina. De applicatie wordt dan automatisch toegevoegd aan uw tenant en u wordt doorgestuurd naar uw Federated Directory-account.

    ![federatieve directory Voeg SCIM toe](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Automatische gebruikersvoorziening configureren voor Federated Directory 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Federated Directory te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor Federated Directory in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Federated Directory**in de lijst met toepassingen .

    ![De federatieve mapkoppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://api.federated.directory/v2/` **Beheerdersreferenties** invoer in Tenant-URL in. Voer de waarde in die u eerder hebt opgehaald en opgeslagen uit Federated Directory in **Secret Token**. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Federated Directory. Als de verbinding mislukt, moet u ervoor zorgen dat uw Federatiedirectory-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer onder de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met federatieve directory**.

    ![Zelfstudie voor federatieve directory](media/federated-directory-provisioning-tutorial/user-mappings.png)
    
    
11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Federated Directory in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Federated Directory te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Zelfstudie voor federatieve directory](media/federated-directory-provisioning-tutorial/user-attributes.png)
    

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Federated Directory wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten in Federated Directory door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op federated directory.

Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md) voor meer informatie over het lezen van de azure AD-inrichtingslogboeken.
## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
