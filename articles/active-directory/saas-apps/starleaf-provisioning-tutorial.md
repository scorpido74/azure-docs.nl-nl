---
title: 'Zelf studie: StarLeaf configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op StarLeaf.
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
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 520373fc6a05bcaada973273e3553f9da623c669
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77064288"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Zelf studie: StarLeaf configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in StarLeaf en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in StarLeaf.

> [!NOTE]
>  In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector is momenteel beschikbaar als preview-versie. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een StarLeaf-Tenant](https://www.starleaf.com/solutions/).
* Een gebruikers account in StarLeaf met beheerders machtigingen.

## <a name="assign-users-to-starleaf"></a>Gebruikers toewijzen aan StarLeaf
Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u bepalen welke gebruikers en groepen in azure AD toegang nodig hebben tot StarLeaf. Vervolgens kunt u de gebruikers en groepen toewijzen aan StarLeaf door [deze instructies](../manage-apps/assign-user-or-group-access-portal.md)te volgen.

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Belang rijke tips voor het toewijzen van gebruikers aan StarLeaf

* Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan StarLeaf om de configuratie van automatische gebruikers inrichting te testen. U kunt later extra gebruikers en groepen toewijzen.

* Wanneer u een gebruiker toewijst aan StarLeaf, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol standaard toegang worden uitgesloten van het inrichten.

## <a name="set-up-starleaf-for-provisioning"></a>StarLeaf instellen voor inrichting

Voordat u StarLeaf configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten configureren in StarLeaf:

1. Meld u aan bij de [StarLeaf-beheer console](https://portal.starleaf.com/#page=login). Navigeer naar **integraties**  >  **integratie toevoegen**.

    ![SCIM StarLeaf toevoegen](media/starleaf-provisioning-tutorial/image00.png)

2. Selecteer het **type** dat moet worden Microsoft Azure Active Directory. Voer een passende naam in **naam**in. Klik op **Toepassen**.

    ![SCIM StarLeaf toevoegen](media/starleaf-provisioning-tutorial/image01.png)

3.  De **scim basis-URL** en **toegangs token** waarden worden vervolgens weer gegeven. Deze waarden worden ingevoerd in de velden **Tenant-URL** en **geheim-token** op het tabblad inrichten van uw StarLeaf-toepassing in de Azure Portal. 

    ![StarLeaf-token maken](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>StarLeaf toevoegen vanuit de galerie

Als u StarLeaf wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u StarLeaf van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om StarLeaf toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **StarLeaf**in het zoekvak en selecteer **StarLeaf** in het deel venster resultaten.
    ![StarLeaf in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Automatische gebruikers inrichting configureren voor StarLeaf

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in StarLeaf te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **StarLeaf**.

    ![De koppeling StarLeaf in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. In het gedeelte beheerders referenties voert u de **scim basis-URL** en **toegangs token** waarden in die respectievelijk eerder zijn opgehaald in de **Tenant-URL** en het **geheime token** . Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met StarLeaf. Als de verbinding mislukt, zorg er dan voor dat uw StarLeaf-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **een e-mail melding verzenden wanneer een fout** is opgetreden in.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met StarLeaf**.

    ![StarLeaf-token maken](media/starleaf-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar StarLeaf in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in StarLeaf voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![StarLeaf-token maken](media/starleaf-provisioning-tutorial/userattribute.png)


10. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.


11. Als u de Azure AD-inrichtings service voor **StarLeaf wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor StarLeaf door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op StarLeaf.

Voor meer informatie over het lezen van de Azure AD-inrichtings logboeken raadpleegt u [rapportage over automatische gebruikers accounts inrichten](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Connector beperkingen

* StarLeaf biedt momenteel geen ondersteuning voor het inrichten van groepen. 
* StarLeaf vereist dat de waarden voor **e-mail** en **gebruikers naam** dezelfde bron waarde hebben.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Het inrichten van gebruikers accounts beheren voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [controleren van Logboeken en het ophalen van rapporten over inrichtings activiteiten](../app-provisioning/check-status-user-account-provisioning.md).
