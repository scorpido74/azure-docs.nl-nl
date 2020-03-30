---
title: 'Zelfstudie: IDEO configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor IDEO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: de4f06a3-83e9-46ce-80ee-03d706b91c81
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2019
ms.author: Zhchia
ms.openlocfilehash: f5f163109d648a4fc021b41325c6d585a5a7a3e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057563"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>Zelfstudie: IDEO configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in IDEO en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor IDEO.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een IDEO-huurder](https://www.shape.space/product/pricing)
* Een gebruikersaccount op IDEO | Vorm met beheerdersmachtigingen.

## <a name="assign-users-to-ideo"></a>Gebruikers toewijzen aan IDEO

Azure Active Directory gebruikt een concept genaamd toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot IDEO nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan IDEO door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-ideo"></a>Belangrijke tips voor het toewijzen van gebruikers aan IDEO

* Het wordt aanbevolen dat één Azure AD-gebruiker aan IDEO wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan IDEO toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-ideo-for-provisioning"></a>IDEO instellen voor inrichten

Voordat u IDEO configureert voor automatische gebruikersvoorziening met Azure AD, moet u bepaalde inprovisioningsgegevens ophalen bij IDEO.

1. Neem voor **Secret Token** contact productsupport@ideo.comop met het IDEO support team op . Deze waarde wordt ingevoerd in het veld **Geheim token** op het tabblad Provisioning van uw IDEO-toepassing in de Azure-portal. 

## <a name="add-ideo-from-the-gallery"></a>IDEO toevoegen vanuit de galerie

Als u IDEO wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u IDEO vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **IDEO**in het zoekvak , selecteer **IDEO** in het resultatenpaneel. 

    ![IDEO in de resultatenlijst](common/search-new-app.png)

5. Selecteer de knop **Aanmelden voor IDEO** waarmee u wordt omgeleid naar de aanmeldpagina van IDEO. 

    ![IDEO OIDC toevoegen](media/ideo-provisioning-tutorial/signup.png)

6. Aangezien IDEO een OpenIDConnect-app is, kiest u ervoor om met uw Microsoft-werkaccount in te loggen bij IDEO.

    ![IDEO OIDC login](media/ideo-provisioning-tutorial/login.png)

7. Na een succesvolle verificatie accepteert u de toestemmingsprompt voor de toestemmingspagina. De applicatie wordt dan automatisch toegevoegd aan uw tenant en u wordt doorgestuurd naar uw IDEO-account.

    ![IDEO OIDc Consent](media/ideo-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-ideo"></a>Automatische gebruikersinrichting configureren voor IDEO 

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in IDEO te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersvoorziening voor IDEO in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **IDEO**in de lijst met toepassingen .

    ![De IDEO-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://profile.ideo.com/api/scim/v2` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer de waarde in die u hebt opgehaald bij het IDEO-ondersteuningsteam in **Secret Token.** Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met IDEO. Als de verbinding mislukt, moet u ervoor zorgen dat uw IDEO-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met IDEO**onder de sectie **Toewijzingen** .

    ![IDEO-gebruikerstoewijzingen](media/ideo-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar IDEO in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in IDEO te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![IDEO-gebruikerskenmerken](media/ideo-provisioning-tutorial/userattributes.png)

10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

11. Als u de Azure AD-inrichtingsservice voor IDEO wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](media/ideo-provisioning-tutorial/groupmappings.png)

12. Definieer de gebruikers en/of groepen die u aan IDEO wilt inrichten door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](media/ideo-provisioning-tutorial/groupattributes.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op IDEO.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)


