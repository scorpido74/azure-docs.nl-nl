---
title: 'Zelf studie: Figma automatisch inrichten van gebruikers configureren met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Figma.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: f5b14ecf061e8f0c53dc1387d1581bc780d190b9
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850872"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Zelf studie: Figma configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Figma en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in Figma.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een Figma-Tenant](https://www.figma.com/pricing/).
* Een gebruikers account in Figma met beheerders machtigingen.

## <a name="assign-users-to-figma"></a>Gebruikers toewijzen aan Figma.
Azure Active Directory gebruikt een concept met de naam toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot Figma. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan Figma door de volgende instructies te volgen:
 
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Belang rijke tips voor het toewijzen van gebruikers aan Figma

 * U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan Figma om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Figma, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol Standaardtoegang worden uitgesloten van het inrichten.

## <a name="set-up-figma-for-provisioning"></a>Figma instellen voor inrichting

Voordat u Figma configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u bepaalde inrichtings gegevens ophalen van Figma.

1. Meld u aan bij de [Figma-beheer console](https://www.Figma.com/). Klik op het tandwiel pictogram naast uw Tenant.

    :::image type="content" source="media/Figma-provisioning-tutorial/image0.png" alt-text="Scherm opname van de Figma-beheer console. Een Tenant met de naam een D scim-test is zichtbaar. Naast de Tenant is een tandwiel pictogram gemarkeerd." border="false":::

2. Navigeer naar **General > update log in Settings**.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma03.png" alt-text="Scherm opname van de Figma-beheer console. Een Tenant met de naam een D scim-test is zichtbaar. Naast de Tenant is een tandwiel pictogram gemarkeerd." border="false":::

3. Kopieer de **Tenant-id**. Deze waarde wordt gebruikt om de SCIM-eind punt-URL te maken die moet worden ingevoerd in het veld **Tenant-URL** op het tabblad inrichten van de Figma-toepassing in de Azure Portal.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma-tenantid.png" alt-text="Scherm opname van de Figma-beheer console. Een Tenant met de naam een D scim-test is zichtbaar. Naast de Tenant is een tandwiel pictogram gemarkeerd." border="false":::

4. Schuif omlaag en klik op **API-token genereren**.

    :::image type="content" source="media/Figma-provisioning-tutorial/token.png" alt-text="Scherm opname van de Figma-beheer console. Een Tenant met de naam een D scim-test is zichtbaar. Naast de Tenant is een tandwiel pictogram gemarkeerd." border="false":::

5. Kopieer de waarde van het  **API-token** . Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van uw Figma-toepassing in de Azure Portal. 

    :::image type="content" source="media/Figma-provisioning-tutorial/figma04.png" alt-text="Scherm opname van de Figma-beheer console. Een Tenant met de naam een D scim-test is zichtbaar. Naast de Tenant is een tandwiel pictogram gemarkeerd." border="false":::

## <a name="add-figma-from-the-gallery"></a>Figma toevoegen vanuit de galerie

Als u Figma wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Figma van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Figma**in het zoekvak, selecteer **Figma** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Figma in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Automatische gebruikers inrichting configureren voor Figma 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Figma te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor Figma, gevolgd door de instructies in de [Figma-zelf studie voor eenmalige aanmelding](figma-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Figma in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Figma** in de lijst met toepassingen.

    ![De koppeling Figma in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Scherm opname van de opties voor beheer met de inrichtings optie.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Scherm afbeelding van de vervolg keuzelijst voor de inrichtings modus met de automatische optie aangeroepen.](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://www.figma.com/scim/v2/<TenantID>` in de Tenant- **URL** waarbij **TenantID** de waarde is die u eerder hebt opgehaald van Figma. Voer de **API-token** waarde in een **geheim token**in. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Figma. Als de verbinding mislukt, zorg er dan voor dat uw Figma-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Figma**.

    ![Figma-gebruikers toewijzingen](media/Figma-provisioning-tutorial/figma05.png)

11. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Figma in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Figma voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Figma-gebruikers kenmerken](media/Figma-provisioning-tutorial/figma06.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor **Figma wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Figma door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Figma.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)