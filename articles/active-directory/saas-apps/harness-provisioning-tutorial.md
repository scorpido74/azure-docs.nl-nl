---
title: 'Zelfstudie: Harnas configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te deprovisioneneren aan Harness.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 518d86fff04a23f1c1e63c44c53485b99f30637d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057830"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Zelfstudie: Harnas configureren voor automatische gebruikersinrichting

In dit artikel vindt u informatie over het configureren van Azure Active Directory (Azure AD) om gebruikers of groepen automatisch in te richten en te deprovisioneren op Harness.

> [!NOTE]
> In dit artikel wordt een connector beschreven die is gebouwd bovenop de Azure AD-service voor het inrichten van gebruikers. Zie [Gebruikersinrichting en deprovisioning voor saas-toepassingen automatiseren met Azure Active Directory voor](../app-provisioning/user-provisioning.md)belangrijke informatie over deze service en antwoorden op veelgestelde vragen.
>
> Deze connector is momenteel in preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in dit artikel wordt beschreven, gaat ervan uit dat u al de volgende voorwaarden hebt:

* Een Azure AD-tenant
* [Een Harnashuurder](https://harness.io/pricing/)
* Een gebruikersaccount in Harnas met *beheerdersmachtigingen*

## <a name="assign-users-to-harness"></a>Gebruikers toewijzen aan Harnas

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersinrichting worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, bepaalt u welke gebruikers of groepen in Azure AD toegang tot Harness nodig hebben. U deze gebruikers of groepen vervolgens toewijzen aan Harnas door de instructies te volgen in [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Belangrijke tips voor het toewijzen van gebruikers aan Harness

* We raden u aan één Azure AD-gebruiker aan Harness toe te wijzen om de automatische configuratie van gebruikersinrichting te testen. Extra gebruikers of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker aan Harnas toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoogvenster **Toewijzing.** Gebruikers met de *functie Standaardtoegang* zijn uitgesloten van inrichten.

## <a name="set-up-harness-for-provisioning"></a>Harnas instellen voor inrichten

1. Meld u aan bij uw [harnasbeheerconsole](https://app.harness.io/#/login)en ga vervolgens naar **Continuous Security** > **Access Management**.

    ![Beheerconsole benutten](media/harness-provisioning-tutorial/admin.png)

1. Selecteer **API-sleutels**.

    ![Koppeling API-sleutels benutten](media/harness-provisioning-tutorial/apikeys.png)

1. Selecteer **API-sleutel toevoegen**. 

    ![Koppeling API-sleutel toevoegen benutten](media/harness-provisioning-tutorial/addkey.png)

1. Ga als volgt te werk in het deelvenster **Api-sleutel toevoegen:**

    ![Deelvenster Api-sleutel toevoegen](media/harness-provisioning-tutorial/title.png)
   
   a. Geef in het vak **Naam** een naam op voor de sleutel.  
   b. Selecteer een optie in de vervolgkeuzelijst **Overgenomen machtigingen uit** de vervolgkeuzelijst. 
   
1. Selecteer **Indienen**.

1. Kopieer de **sleutel** voor later gebruik in deze zelfstudie.

    ![Token maken met harnas maken](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Harnas toevoegen vanuit de galerie

Voordat u Harnas configureert voor automatische gebruikersvoorziening met Azure AD, moet u Harnas uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**.

    ![De knop 'Azure Active Directory'](common/select-azuread.png)

1. Selecteer **Enterprise-toepassingen** > **Alle toepassingen**.

    ![De link 'Alle toepassingen'](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop 'Nieuwe toepassing'](common/add-new-app.png)

1. Typ **Harnas**in het zoekvak, selecteer **Harnas** in de lijst met resultaten en selecteer vervolgens de knop **Toevoegen** om de toepassing toe te voegen.

    ![Harnas in de resultatenlijst](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Automatische gebruikersinrichting configureren voor Harnas 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers of groepen in Harnas te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on voor Harness in te schakelen door de instructies in de [handleiding voor eenmalige aanmelding te](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)volgen. U eenmalige aanmelding configureren, onafhankelijk van automatische gebruikersinrichting, hoewel deze twee functies elkaar aanvullen.

> [!NOTE]
> Zie het artikel Harnas [API-sleutels](https://docs.harness.io/article/smloyragsm-api-keys) voor meer informatie over het Eindpunt van Harnas SCIM.

Ga als volgt te werk om automatische gebruikersinrichting voor Harnas in Azure AD te configureren:

1. Selecteer Enterprise Applications **All-toepassingen** > **All applications**in de [Azure-portal.](https://portal.azure.com)

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **Harnas**in de lijst met toepassingen .

    ![De koppeling Harnas in de lijst met toepassingen](common/all-applications.png)

1. Selecteer **Inrichten**.

    ![De knop Inrichten](common/provisioning.png)

1. Selecteer **Automatisch**in de vervolgkeuzelijst **Inprovisioning Mode** .

    ![De vervolgkeuzelijst 'Inrichtingsmodus'](common/provisioning-automatic.png)

1. Ga als volgt te werk onder **Beheerdersreferenties:**

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Voer **in** het vak **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** Url van tenant .  
   b. Voer in het vak **Secret Token** de waarde SCIM-verificatietoken in die u hebt opgeslagen in stap 6 van de sectie Harnas instellen voor inrichting.  
   c. Selecteer **Testverbinding** om ervoor te zorgen dat Azure AD verbinding kan maken met Harness. Als de verbinding mislukt, moet u ervoor zorgen dat uw *Harness-account beheerdersmachtigingen* heeft en het vervolgens opnieuw proberen.

1. Voer in het vak **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fout moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt** in.

    ![Het vak 'E-mail melden'](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer **onder Toewijzingen**de optie **Azure Active Directory-gebruikers synchroniseren om te benutten**.

    ![Koppeling 'Azure Active Directory-gebruikers synchroniseren om te benutten' gebruiken](media/harness-provisioning-tutorial/usermappings.png)

1. Controleer **onder Kenmerktoewijzingen**de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Harnas. De kenmerken die als Matching zijn *geselecteerd,* worden gebruikt om de gebruikersaccounts in Harnas te matchen voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen door te voeren.

    ![Deelvenster Gebruiker 'Toewijzingen kenmerken benutten'](media/harness-provisioning-tutorial/userattributes.png)

1. Selecteer **onder Toewijzingen**de optie **Azure Active Directory Groups synchroniseren om te benutten**.

    ![Koppeling 'Azure Active Directory-groepen synchroniseren om te benutten' gebruiken](media/harness-provisioning-tutorial/groupmappings.png)

1. Controleer **onder Kenmerktoewijzingen**de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Harnas. De kenmerken die zijn geselecteerd als *Eigenschappen matching* worden gebruikt om de groepen in Harnas te matchen voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen door te voeren.

    ![Deelvenster Groep 'Toewijzingen kenmerken'](media/harness-provisioning-tutorial/groupattributes.png)

1. Zie [Op kenmerken gebaseerde toepassingsinrichting op basis van kenmerken met scopingfilters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Schakel onder **Instellingen**de Azure AD-inrichtingsservice voor Harnas in om de **instelstatus** in te schakelen naar **Aan**.

    ![Inschakelen Status schakelen naar 'Aan'](common/provisioning-toggle-on.png)

1. Selecteer **onder Instellingen**in de vervolgkeuzelijst **Scope** hoe u de gebruikers of groepen die u indient voor Harnas wilt synchroniseren.

    ![Inrichtingskader](common/provisioning-scope.png)

1. Wanneer u klaar bent voor inlevering, selecteert u **Opslaan**.

    ![De knop Opslaan inrichten](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van de gebruikers of groepen die u indient. De eerste synchronisatie duurt langer om uit te voeren dan latere. Synchronisaties vinden ongeveer elke 40 minuten plaats, zolang de Azure AD-inrichtingsservice wordt uitgevoerd. Als u de voortgang wilt controleren, gaat u naar de sectie **Synchronisatiedetails.** U ook koppelingen volgen naar een rapportagerapport voor het inrichten van activiteiten, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Harnas.

Zie [Rapport over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor bedrijfsapps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
