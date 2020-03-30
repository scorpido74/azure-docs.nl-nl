---
title: 'Zelfstudie: Zscaler configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Zscaler.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 8add1f57b566d746d464c1ca165938fc112a9784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062707"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Zelfstudie: Zscaler configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Zscaler en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen naar Zscaler.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al het volgende hebt:

* Een Azure AD-tenant.
* Een Zscaler huurder.
* Een gebruikersaccount in Zscaler met beheerdersmachtigingen.

> [!NOTE]
> De integratie azure AD-inrichting is gebaseerd op de Zscaler SCIM API, die beschikbaar is voor Zscaler-ontwikkelaars voor accounts met het Enterprise-pakket.

## <a name="adding-zscaler-from-the-gallery"></a>Zscaler toevoegen vanuit de galerie

Voordat u Zscaler configureert voor automatische gebruikersvoorziening met Azure AD, moet u Zscaler vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u Zscaler wilt toevoegen vanuit de Azure AD-toepassingsgalerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **Zscaler**in het zoekvak , selecteer **Zscaler** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![Zscaler in de resultatenlijst](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Gebruikers toewijzen aan Zscaler

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Zscaler nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Zscaler door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Belangrijke tips voor het toewijzen van gebruikers aan Zscaler

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Zscaler wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Zscaler toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Automatische gebruikersvoorziening configureren voor Zscaler

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Zscaler te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on voor Zscaler in te schakelen, volgens de instructies in de [Zscaler single sign-on tutorial](zscaler-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersvoorziening voor Zscaler in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Zscaler**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zscaler**in de lijst met toepassingen .

    ![De Koppeling Zscaler in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Voer onder de sectie **Beheerdersreferenties** de **URL van de tenant** en het geheime **token** van uw Zscaler-account in zoals beschreven in stap 6.

6. Als u de **URL van de tenant** en het geheime token **wilt**verkrijgen, navigeert u naar Beheer **> verificatie-instellingen** in de gebruikersinterface van de Zscaler-portal en klikt u op **SAML** onder **Verificatietype**.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Klik op **SAML configureren** om **SAML-opties voor configuratie** te openen.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Selecteer **SCIM-gebaseerde inrichting inschakelen** om **basis-URL** en **dragertoken**op te halen en sla vervolgens de instellingen op. Kopieer de **url van** de basis naar **tenant**en token **aan toonder** naar **Secret Token** in de Azure-portal.

7. Klik op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Zscaler wanneer u de velden in stap 5 bevolken. Als de verbinding mislukt, moet u ervoor zorgen dat uw Zscaler-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/test-connection.png)

8. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**in.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/notification.png)

9. Klik op **Opslaan**.

10. Selecteer Azure **Active Directory-gebruikers synchroniseren met Zscaler**onder de sectie **Toewijzingen** .

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Zscaler in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Zscaler te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Selecteer Azure **Active Directory-groepen synchroniseren met Zscaler**onder de sectie **Toewijzingen** .

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Zscaler in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in Zscaler te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](./../active-directory-saas-scoping-filters.md)

15. Als u de Azure AD-inrichtingsservice voor Zscaler wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Definieer de gebruikers en/of groepen die u aan Zscaler wilt inrichten door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/scoping.png)

17. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Zscaler Provisioning](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Zscaler.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../active-directory-saas-provisioning-reporting.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
