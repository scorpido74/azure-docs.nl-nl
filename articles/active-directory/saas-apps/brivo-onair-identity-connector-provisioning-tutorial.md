---
title: 'Zelf studie: Brivo OnAir Identity connector configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts naar Brivo OnAir Identity connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 542ce04c-ef7d-4154-9b0e-7f68e1154f6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 84d14d63cd7bb96a7a4f37d2a6c29f09a50b2420
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85848686"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Zelf studie: Brivo OnAir Identity connector configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Brivo OnAir Identity connector en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen naar Brivo OnAir Identity connector.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een Brivo OnAir Identity connector-Tenant](https://www.brivo.com/lp/quote)
* Een gebruikers account in Brivo OnAir Identity connector met senior beheerders machtigingen.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Gebruikers toewijzen aan Brivo OnAir Identity connector

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot de Brivo OnAir-identiteits connector. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan Brivo OnAir Identity connector door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Belang rijke tips voor het toewijzen van gebruikers aan Brivo OnAir Identity connector

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan Brivo OnAir Identity connector om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Brivo OnAir Identity connector, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Brivo OnAir-identiteits connector instellen voor inrichting

1. Meld u aan bij de [beheer console van uw Brivo OnAir-identiteits connector](https://acs.brivo.com/login/). Navigeer naar **account instellingen > account**.

   ![Beheer console van Brivo OnAir Identity connector](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2. Klik op het tabblad **Azure AD** . Op de pagina Details van **Azure AD** voert u het wacht woord van uw account voor de senior beheerder opnieuw in. Klik op **verzenden**.

   ![Brivo OnAir Identity connector Azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3. Klik op de knop **token kopiëren** en sla het **geheime token**op. Deze waarde wordt ingevoerd in het veld geheime token op het tabblad inrichten van uw Brivo OnAir Identity connector-toepassing in de Azure Portal.

   ![Brivo OnAir Identity connector-token](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Brivo OnAir Identity connector toevoegen vanuit de galerie

Voordat u Brivo OnAir Identity connector configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Brivo OnAir Identity connector toevoegen vanuit de Azure AD-toepassings galerie aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Brivo OnAir Identity connector toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Brivo OnAir Identity connector**, selecteer **Brivo OnAir Identity connector** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Brivo OnAir Identity connector in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Automatische gebruikers inrichting configureren voor Brivo OnAir-identiteits connector 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Brivo OnAir Identity connector te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Brivo OnAir Identity connector in azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Brivo OnAir Identity connector**.

    ![De koppeling Brivo OnAir Identity connector in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://scim.brivo.com/ActiveDirectory/v2/` in de Tenant- **URL**. Voer de waarde voor het **scim-verificatie token** in die eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Brivo OnAir Identity connector. Als de verbinding mislukt, zorg er dan voor dat uw Brivo OnAir Identity Connector-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Brivo OnAir-identiteits connector**.

    ![Brivo OnAir Identity connector-gebruikers toewijzingen](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Brivo OnAir Identity connector in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Brivo OnAir Identity connector voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Brivo OnAir Identity connector-gebruikers kenmerken](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Brivo OnAir-id-connector**.

    ![Brivo OnAir Identity connector-groeps toewijzingen](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Brivo OnAir Identity connector in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de groepen in Brivo OnAir Identity connector voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Brivo OnAir Identity connector-groeps kenmerken](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD-inrichtings service voor Brivo OnAir Identity connector wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Brivo OnAir Identity connector door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. Hiermee worden alle acties beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Brivo OnAir Identity connector.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)

