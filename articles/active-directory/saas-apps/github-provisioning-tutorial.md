---
title: 'Zelfstudie: Gebruikersinrichting voor GitHub - Azure AD'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor GitHub.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82f7252f2d9cdd2c54fae593d8463bfe84bd6ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057648"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Zelfstudie: GitHub configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in GitHub en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar GitHub.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

* Een Azure Active directory tenant
* Een GitHub-organisatie, gemaakt in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), waarvoor een [GitHub Enterprise-abonnement](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) vereist is
* Een gebruikersaccount in GitHub met beheerdersmachtigingen voor de organisatie
* Zorg ervoor dat OAuth-toegang is verstrekt voor uw organisatie zoals [hier](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization) beschreven

> [!NOTE]
> De integratie van Azure AD-inrichting is gebaseerd op de [GitHub SCIM-API](https://developer.github.com/v3/scim/), die beschikbaar is voor [GitHub Enterprise Cloud-klanten](https://help.github.com/articles/github-s-products/#github-enterprise) op het [GitHub Enterprise-factureringsplan](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations).

## <a name="assigning-users-to-github"></a>Gebruikers toewijzen aan GitHub

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd. 

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw GitHub-app. Eenmaal besloten, u deze gebruikers toewijzen aan uw GitHub-app door de instructies hier te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Belangrijke tips voor het toewijzen van gebruikers aan GitHub

* Het wordt aanbevolen dat één Azure AD-gebruiker aan GitHub wordt toegewezen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan GitHub toewijst, moet u de rol **Gebruiker** of een andere geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. De functie **Standaardtoegang** werkt niet voor inrichten en deze gebruikers worden overgeslagen.

## <a name="configuring-user-provisioning-to-github"></a>Gebruikersvoorziening configureren voor GitHub

In deze sectie u uw Azure AD verbinden met de API voor het inrichten van gitHub-gebruikersaccounts en de inrichtingsservice configureren om toegewezen gebruikersaccounts in GitHub te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzing in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-On voor GitHub in te schakelen, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Automatische gebruikersaccountvoorziening configureren naar GitHub in Azure AD

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

2. Als u GitHub al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw voorbeeld van GitHub via het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **GitHub** in de toepassingsgalerie. Selecteer GitHub in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

3. Selecteer uw exemplaar van GitHub en selecteer vervolgens het tabblad **Inrichten.**

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![GitHub-inrichting](./media/github-provisioning-tutorial/GitHub1.png)

5. Klik onder de sectie **Beheerdersreferenties** op **Autoriseren**. Met deze bewerking wordt een GitHub-autorisatiedialoogvenster geopend in een nieuw browservenster. Houd er rekening mee dat u ervoor moet zorgen dat u bent goedgekeurd om toegang te autoriseren. Volg de [aanwijzingen die hier](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)beschreven zijn.

6. Meld u in het nieuwe venster aan bij GitHub met uw Admin-account. Selecteer in het dialoogvenster resulterende autorisatie het GitHub-team waarvoor u inrichting wilt inschakelen en selecteer vervolgens **Toestaan**. Ga na voltooiing terug naar de Azure-portal om de inrichtingsconfiguratie te voltooien.

    ![Dialoogvenster Autorisatie](./media/github-provisioning-tutorial/GitHub2.png)

7. Voer in de Azure-portal **url van de tenant** in en klik op Verbinding **testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw GitHub-app. Als de verbinding mislukt, zorg er dan voor dat uw GitHub-account beheerdersmachtigingen heeft en **tenant-URl** correct is `https://api.github.com/scim/v2/organizations/<Organization_name>`ingevoerd en probeer vervolgens de stap 'Autoriseren' opnieuw (u **tenant-URL** per regel vormen:, u uw organisaties vinden onder uw GitHub-account: > **Instellingenorganisaties**). **Settings**

    ![Dialoogvenster Autorisatie](./media/github-provisioning-tutorial/GitHub3.png)

8. Voer het e-mailadres in van een persoon of groep die meldingen van provisioning-fout moet ontvangen in het veld **Meldingse e-mail** en schakel het selectievakje 'Een e-mailmelding verzenden wanneer er een fout optreedt' in.

9. Klik op **Opslaan**.

10. Selecteer Azure **Active Directory-gebruikers synchroniseren met GitHub**onder de sectie Toewijzingen .

11. Controleer in de sectie **Toewijzingen van kenmerken** de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar GitHub. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in GitHub te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

12. Als u de Azure AD-inrichtingsservice voor GitHub wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen**

13. Klik op **Opslaan**.

Met deze bewerking wordt de eerste synchronisatie gestart van gebruikers en/of groepen die aan GitHub zijn toegewezen in de sectie Gebruikers en groepen. De eerste synchronisatie duurt langer om uit te voeren dan de daaropvolgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties van de inrichtingsservice worden beschreven.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
