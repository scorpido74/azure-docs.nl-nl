---
title: 'Zelf studie: MindTickle configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op MindTickle.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 6ae7b6ac9c6feb539e4da3627ebbabd3c5b628fb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77061216"
---
# <a name="tutorial-configure-mindtickle-for-automatic-user-provisioning"></a>Zelf studie: MindTickle configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in MindTickle en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in MindTickle.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een MindTickle-Tenant](https://www.mindtickle.com/)
* Een gebruikers account in MindTickle met beheerders machtigingen.

## <a name="assigning-users-to-mindtickle"></a>Gebruikers toewijzen aan MindTickle

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot MindTickle. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan MindTickle door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mindtickle"></a>Belang rijke tips voor het toewijzen van gebruikers aan MindTickle

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan MindTickle om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan MindTickle, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-mindtickle-for-provisioning"></a>MindTickle instellen voor inrichting

Voordat u MindTickle configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten inschakelen op MindTickle.


1.  Neem contact op met het [ondersteunings team van MindTickle](mailto:help@mindtickle.com) om het JWT-token op te halen dat nodig is voor het configureren van scim-inrichting.


## <a name="add-mindtickle-from-the-gallery"></a>MindTickle toevoegen vanuit de galerie

Als u MindTickle wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u MindTickle van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om MindTickle toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **MindTickle**in het zoekvak, selecteer **MindTickle** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![MindTickle in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mindtickle"></a>Automatische gebruikers inrichting configureren voor MindTickle 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in MindTickle te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor MindTickle, gevolgd door de instructies in de [MindTickle-zelf studie voor eenmalige aanmelding](mindtickle-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar in de compliment

### <a name="to-configure-automatic-user-provisioning-for-mindtickle-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor MindTickle in azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **MindTickle** in de lijst met toepassingen.

    ![Het koppeling naar MindTickle in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://admin.mindtickle.com/scim` in de Tenant- **URL**. Voer de **JWT-token** waarde in die u eerder hebt opgehaald in het tekstvak geheim token, geef de **JWT-token** waarde op die is opgegeven door het MindTickle-ondersteunings team. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met myPolicies. Als de verbinding mislukt, zorg er dan voor dat uw MindTickle-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met MindTickle**.

    ![MindTickle-gebruikers toewijzingen](media/mindtickle-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar MindTickle in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in MindTickle voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![MindTickle-gebruikers toewijzingen](media/mindtickle-provisioning-tutorial/userattribute.png)

12. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD-inrichtings service voor **MindTickle wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor MindTickle door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan bij de volgende synchronisaties. Voor meer informatie over hoe lang het duurt voor het inrichten van gebruikers en/of groepen, raadpleegt u [hoe lang het duurt om gebruikers](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)in te richten. 

U kunt de **huidige status** sectie gebruiken om de voortgang te controleren en koppelingen naar uw inrichtings activiteiten rapport te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op MindTickle. Zie [de status van gebruikers inrichten controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie. Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor informatie over het vastleggen van Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
