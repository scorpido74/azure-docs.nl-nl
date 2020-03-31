---
title: 'Zelfstudie: Zscaler-bèta configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor De Bèta van Zscaler.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 505cd7a3350c937885f0f03268ef326a4f784258
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062724"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Zelfstudie: Zscaler-bèta configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Zscaler Beta en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Bèta van Zscaler.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../active-directory-saas-app-provisioning.md).
>


## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al het volgende hebt:

* Een Azure AD-tenant
* Een Zscaler Beta-tenant
* Een gebruikersaccount in De Bèta van Zscaler met beheerdersmachtigingen

> [!NOTE]
> De integratie van Azure AD-inrichting is gebaseerd op de Zscaler Beta SCIM API, die beschikbaar is voor Zscaler Beta-ontwikkelaars voor accounts met het Enterprise-pakket.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Zscaler Beta toevoegen vanuit de galerie

Voordat u Zscaler-bèta configureert voor automatische gebruikersvoorziening met Azure AD, moet u Zscaler-bèta vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u Zscaler-bèta wilt toevoegen vanuit de Azure AD-toepassingsgalerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Zscaler Beta**, selecteer **Zscaler Beta** in het resultaatvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Zscaler Beta in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Gebruikers toewijzen aan Zscaler Beta

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot De Bèta van Zscaler. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Zscaler Beta door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Belangrijke tips voor het toewijzen van gebruikers aan Zscaler Beta

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan De Bèta van Zscaler om de automatische configuratie van de gebruikersvoorziening te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker toewijst aan De Bèta van Zscaler, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Automatische gebruikersvoorziening configureren voor Zscaler-bèta

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in De Bèta van Zscaler te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor Zscaler Beta, volgens de instructies in de [Zscaler Beta single sign-on tutorial](zscaler-beta-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersinrichting voor Zscaler-bèta in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **vervolgens De Bèta van Zscaler**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zscaler Beta**in de lijst met toepassingen .

    ![De Zscaler Beta-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Voer onder de sectie **Beheerdersreferenties** de **URL van de tenant** en het geheime **token** van uw Zscaler-bètaaccount in zoals beschreven in stap 6.

6. Als u de **URL van** de tenant en het geheime **token wilt**verkrijgen, navigeert u naar Beheer > **verificatie-instellingen** in de gebruikersinterface van de Bètaportal Zscaler en klikt u op **SAML** onder **Verificatietype**.

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Klik op **SAML configureren** om de **SAML-opties voor configuratie** te openen.

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Selecteer **SCIM-gebaseerde inrichting inschakelen** om **basis-URL** en **dragertoken**op te halen en sla vervolgens de instellingen op. Kopieer de **url van** de basis naar **tenant**en token **aan toonder** naar **Secret Token** in de Azure-portal.

7. Klik op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met De Bèta van Zscaler wanneer u de velden in stap 5 bevolken. Als de verbinding mislukt, moet u ervoor zorgen dat uw Zscaler-bètaaccount beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**in.

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Klik op **Opslaan**.

10. Selecteer azure **Active Directory-gebruikers synchroniseren met de bètaversie van Zscaler**onder de sectie **Toewijzingen** .

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Zscaler Beta in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in De Bèta van Zscaler te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Selecteer Azure **Active Directory-groepen synchroniseren met De bèta van Zscaler**onder de sectie **Toewijzingen** .

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Zscaler Beta in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen Matching** worden gebruikt om de groepen in De Bèta van Zscaler te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](./../active-directory-saas-scoping-filters.md)

15. Als u de Azure AD-inrichtingsservice voor De Bèta van Zscaler wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Definieer de gebruikers en/of groepen die u wilt inrichten voor De Bèta van Zscaler door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Zscaler Beta Provisioning](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op De Bèta van Zscaler.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../active-directory-saas-provisioning-reporting.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
