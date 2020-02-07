---
title: 'Zelf studie: gebruikers inrichten voor GitHub-Azure AD'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op GitHub.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057648"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Zelf studie: GitHub configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in GitHub en Azure AD om gebruikers accounts van Azure AD automatisch in te richten en te deactiveren naar GitHub.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

* Een Azure Active Directory-Tenant
* Een GitHub-organisatie, gemaakt in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), waarvoor een [GitHub Enterprise-abonnement](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) vereist is
* Een gebruikers account in GitHub met beheerders machtigingen voor de organisatie
* Zorg ervoor dat OAuth-toegang is ingesteld voor uw organisatie, zoals [hier](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization) wordt beschreven

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de [GITHUB scim-API](https://developer.github.com/v3/scim/), die beschikbaar is voor [github Enter prise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) -klanten in het [github Enter prise-facturerings plan](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations).

## <a name="assigning-users-to-github"></a>Gebruikers toewijzen aan GitHub

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd. 

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw GitHub-app. Nadat u hebt besloten, kunt u deze gebruikers toewijzen aan uw GitHub-app door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Belang rijke tips voor het toewijzen van gebruikers aan GitHub

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan GitHub om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan GitHub, moet u **de gebruikersrol** of een andere geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. De **standaard-Access** -rol werkt niet voor inrichten en deze gebruikers worden overgeslagen.

## <a name="configuring-user-provisioning-to-github"></a>Gebruikers inrichten configureren voor GitHub

In deze sectie vindt u instructies voor het verbinden van uw Azure AD-GitHub en het configureren van de inrichtings service om toegewezen gebruikers accounts in GitHub te maken, bij te werken en uit te scha kelen op basis van de gebruikers-en groeps toewijzing in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor GitHub, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies gelden voor elkaar.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Automatische toewijzing van gebruikers accounts configureren voor GitHub in azure AD

1. Blader in het [Azure Portal](https://portal.azure.com)naar het gedeelte **Azure Active Directory > Enter prise-apps > alle toepassingen** .

2. Als u GitHub al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van GitHub met behulp van het zoek veld. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **github** in de toepassings galerie. Selecteer GitHub in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

3. Selecteer uw exemplaar van GitHub en selecteer vervolgens het tabblad **inrichten** .

4. Stel de **inrichtings modus** in op **automatisch**.

    ![GitHub-inrichting](./media/github-provisioning-tutorial/GitHub1.png)

5. Klik onder de sectie **beheerders referenties** op **autoriseren**. Met deze bewerking wordt een dialoog venster voor GitHub-autorisatie geopend in een nieuw browser venster. Let op: u moet ervoor zorgen dat u bent goedgekeurd om toegang te verlenen. Volg de instructies die [hier](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)worden beschreven.

6. Meld u in het nieuwe venster aan bij GitHub met uw beheerders account. Selecteer in het dialoog venster resulterende autorisatie het GitHub-team waarvoor u het inrichten wilt inschakelen en selecteer vervolgens **autoriseren**. Als u klaar bent, keert u terug naar de Azure Portal om de inrichtings configuratie te volt ooien.

    ![Autorisatie dialoogvenster](./media/github-provisioning-tutorial/GitHub2.png)

7. Klik in de Azure Portal op de URL van de invoer **Tenant** en op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw github-app. Als de verbinding mislukt, controleert u of uw GitHub-account beheerders machtigingen heeft en of de **Tenant-URl** correct is gegenereerd. Probeer vervolgens de stap ' autoriseren ' opnieuw (u kunt een **Tenant-URl** instellen op regel: `https://api.github.com/scim/v2/organizations/<Organization_name>`, kunt u uw organisaties vinden onder uw github-account: **instellingen** > **organisaties**).

    ![Autorisatie dialoogvenster](./media/github-provisioning-tutorial/GitHub3.png)

8. Voer het e-mail adres in van een persoon of groep die in het veld met de **meldings-e-mail** inrichtings fout meldingen moet ontvangen en schakel het selectie vakje e-mail meldingen verzenden als er een fout optreedt.

9. Klik op **Opslaan**.

10. Selecteer in de sectie toewijzingen de optie **Azure Active Directory gebruikers synchroniseren met github**.

11. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar github. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in github voor bijwerk bewerkingen. Selecteer de knop Opslaan om door te voeren van eventuele wijzigingen.

12. Als u de Azure AD-inrichtings service voor GitHub wilt inschakelen, wijzigt u de **inrichtings status** **in in het** gedeelte **instellingen**

13. Klik op **Opslaan**.

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn toegewezen aan GitHub in de sectie gebruikers en groepen. De eerste synchronisatie langer duren om uit te voeren dan het volgende wordt gesynchroniseerd, die ongeveer elke 40 minuten optreden als de service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen te volgen naar activiteiten logboeken voor inrichtingen, die alle acties beschrijven die door de inrichtings service worden uitgevoerd.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
