---
title: 'Zelf studie: IDEO configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op IDEO.
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
ms.openlocfilehash: d6bc3170162710e86359b374d1ef707bba0ce268
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152539"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>Zelf studie: IDEO configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in IDEO en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in IDEO.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een IDEO-Tenant](https://www.shape.space/product/pricing)
* Een gebruikers account op IDEO | Vorm met beheerders machtigingen.

## <a name="assign-users-to-ideo"></a>Gebruikers toewijzen aan IDEO

Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot IDEO. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan IDEO door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-ideo"></a>Belang rijke tips voor het toewijzen van gebruikers aan IDEO

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan IDEO om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan IDEO, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-ideo-for-provisioning"></a>IDEO instellen voor inrichting

Voordat u IDEO configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u bepaalde inrichtings gegevens ophalen van IDEO.

1. Voor een **geheim token** neemt u contact op met het ideo-ondersteunings team op productsupport@ideo.com. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van uw ideo-toepassing in de Azure Portal. 

## <a name="add-ideo-from-the-gallery"></a>IDEO toevoegen vanuit de galerie

Als u IDEO wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u IDEO van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **ideo**in het zoekvak en selecteer **ideo** in het deel venster resultaten. 

    ![IDEO in de lijst met resultaten](common/search-new-app.png)

5. Selecteer de knop **Aanmelden voor ideo** , waarmee u wordt doorgestuurd naar de aanmeldings pagina van ideo. 

    ![IDEO OIDC toevoegen](media/ideo-provisioning-tutorial/signup.png)

6. Als IDEO een OpenIDConnect-app is, kiest u aanmelden bij IDEO met uw micro soft-werk account.

    ![IDEO OIDC-aanmelding](media/ideo-provisioning-tutorial/login.png)

7. Accepteer na een geslaagde verificatie de toestemming prompt voor de pagina toestemming. De toepassing wordt vervolgens automatisch toegevoegd aan uw Tenant en u wordt doorgestuurd naar uw IDEO-account.

    ![IDEO OIDc toestemming](media/ideo-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-ideo"></a>Automatische gebruikers inrichting configureren voor IDEO 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in IDEO te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor IDEO in azure AD:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **ideo**.

    ![De koppeling IDEO in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://profile.ideo.com/api/scim/v2` in de **Tenant-URL**. Voer de waarde in die u hebt opgehaald uit het IDEO-ondersteunings team in een **geheim token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met ideo. Als de verbinding mislukt, zorg er dan voor dat uw IDEO-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met ideo**.

    ![IDEO-gebruikers toewijzingen](media/ideo-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar IDEO in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in ideo voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![IDEO-gebruikers kenmerken](media/ideo-provisioning-tutorial/userattributes.png)

10. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

11. Als u de Azure AD-inrichtings service voor **ideo wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](media/ideo-provisioning-tutorial/groupmappings.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor IDEO door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](media/ideo-provisioning-tutorial/groupattributes.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op ideo.

Zie voor meer informatie over het lezen van de Azure AD inrichting logboeken [rapportage over het inrichten van automatische gebruikersaccounts](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Aanvullende resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)


