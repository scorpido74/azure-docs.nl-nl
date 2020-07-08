---
title: 'Zelf studie: een Zscaler configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Zscaler.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77064169"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Zelf studie: een Zscaler configureren voor het automatisch inrichten van gebruikers

In deze zelf studie ziet u de stappen voor het uitvoeren van Zscaler één en Azure Active Directory (Azure AD) voor het configureren van Azure AD voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en groepen tot Zscaler één.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie voor meer informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen [gebruikers automatisch inrichten en ongedaan maken van de inrichting van SaaS-toepassingen (Software-as-a-Service) met Azure Active Directory](../active-directory-saas-app-provisioning.md).


## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u het volgende hebt:

* Een Azure AD-tenant.
* Een Zscaler één Tenant.
* Een gebruikers account in Zscaler met beheerders machtigingen.

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de Zscaler één SCIM-API. Deze API is beschikbaar voor de Zscaler van één ontwikkel aars voor accounts met het ondernemings pakket.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Zscaler één toevoegen vanuit Azure Marketplace

Voordat u Zscaler configureert voor het automatisch inrichten van gebruikers met Azure AD, voegt u Zscaler een van Azure Marketplace toe aan uw lijst met beheerde SaaS-toepassingen.

Voer de volgende stappen uit om Zscaler één van de Marketplace toe te voegen.

1. In de [Azure Portal](https://portal.azure.com), in het navigatie deel venster aan de linkerkant, selecteert u **Azure Active Directory**.

    ![Het Azure Active Directory pictogram](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Zscaler One** in en selecteer **Zscaler One** in het deel venster voor resultaten. Selecteer **toevoegen**om de toepassing toe te voegen.

    ![Zscaler één in de resultaten lijst](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Gebruikers toewijzen aan Zscaler één

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u bepalen welke gebruikers of groepen in azure AD toegang nodig hebben tot Zscaler. Volg de instructies in [een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)om deze gebruikers of groepen toe te wijzen aan Zscaler één.

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Belang rijke tips voor het toewijzen van gebruikers aan Zscaler One

* We raden u aan één Azure AD-gebruiker toe te wijzen aan Zscaler één om de configuratie van automatische gebruikers inrichting te testen. U kunt later aanvullende gebruikers of groepen toewijzen.

* Wanneer u een gebruiker toewijst aan Zscaler één, selecteert u een geldige toepassingsspecifieke rol, indien beschikbaar, in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Automatische gebruikers inrichting configureren voor Zscaler één

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service. Gebruik dit om gebruikers of groepen te maken, bij te werken en uit te scha kelen in Zscaler één op basis van gebruikers-of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt ook op SAML gebaseerde eenmalige aanmelding inschakelen voor Zscaler één. Volg de instructies in de [zelf studie Zscaler one single sign-on](zscaler-One-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Zscaler één in azure AD

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**  >  **alle toepassingen**  >  **Zscaler een**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Zscaler One**.

    ![De Zscaler één koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Zscaler één inrichting](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Zscaler één inrichtings modus](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Vul in het gedeelte **beheerders referenties** de vakken **Tenant-URL** en **geheim token** in met de instellingen voor uw Zscaler één account zoals beschreven in stap 6.

6. Als u de Tenant-URL en het geheime token wilt ophalen, gaat u naar **beheer**  >  **verificatie-instellingen** in de Zscaler-gebruikers interface van de portal. Onder **verificatie type**selecteert u **SAML**.

    ![Zscaler één verificatie-instellingen](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selecteer **SAML configureren** om de SAML-opties **configureren** te openen.

    ![Zscaler-SAML configureren](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Selecteer **op scim gebaseerde inrichting inschakelen** om de instellingen in basis- **URL** en **Bearer-token**op te halen. Sla vervolgens de instellingen op. Kopieer de **basis-URL** -instelling naar de **Tenant-url** in de Azure Portal. Kopieer de **Bearer-token** instelling naar een **geheim token** in de Azure Portal.

7. Nadat u de vakken in stap 5 hebt ingevuld, selecteert u **verbinding testen** om te controleren of Azure AD verbinding kan maken met Zscaler One. Als de verbinding mislukt, moet u ervoor zorgen dat uw Zscaler één account beheerders machtigingen heeft en probeer het opnieuw.

    ![Zscaler één test verbinding](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Voer in het vak **e-mail bericht** het e-mail adres van de persoon of groep in om de inrichtings fout meldingen te ontvangen. Schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![Zscaler Eén e-mail melding](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selecteer **Opslaan**.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren om één te Zscaler**.

    ![Zscaler één gebruikers synchronisatie](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zscaler, in de sectie **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Zscaler één voor update bewerkingen. Selecteer **Opslaan**om de wijzigingen op te slaan.

    ![Zscaler één overeenkomende gebruikers kenmerken](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren om één te Zscaler**.

    ![Zscaler één groeps synchronisatie](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zscaler, in de sectie **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt voor het vergelijken van de groepen in Zscaler één voor update bewerkingen. Selecteer **Opslaan**om de wijzigingen op te slaan.

    ![Zscaler één overeenkomende groeps kenmerken](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Als u bereik filters wilt configureren, volgt u de instructies in de [zelf studie](./../active-directory-saas-scoping-filters.md)voor het filteren op bereik.

15. Als u de Azure AD-inrichtings service voor Zscaler één wilt inschakelen, wijzigt u in de sectie **instellingen** de **inrichtings status** in **op aan**.

    ![Zscaler één inrichtings status](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definieer de gebruikers of groepen die u wilt inrichten voor Zscaler. Selecteer in de sectie **instellingen** de waarden die u in het **bereik**wilt.

    ![Zscaler één bereik](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**.

    ![Zscaler één opslaan](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan bij latere synchronisaties. Ze treden ongeveer elke 40 minuten in beslag, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. In het rapport worden alle acties beschreven die worden uitgevoerd door de Azure AD-inrichtings service op Zscaler One.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../active-directory-saas-provisioning-reporting.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Het inrichten van gebruikers accounts beheren voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
