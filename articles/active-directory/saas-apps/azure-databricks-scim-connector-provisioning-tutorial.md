---
title: 'Zelfstudie: Azure Databricks SCIM-connector configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het automatisch inrichten en de-inrichten van gebruikersaccounts van Azure AD naar Azure Databricks SCIM Connector.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: fe1260982edc877c049716bd74f1bb3e90d33b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370534"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Zelfstudie: Azure Databricks SCIM-connector configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel Azure Databricks SCIM Connector als Azure Active Directory (Azure AD) om automatische gebruikersinrichting te configureren. Wanneer azure AD is geconfigureerd, worden gebruikers en groepen automatisch ingericht en de-bepalingen naar [Azure Databricks SCIM Connector](https://databricks.com/) met behulp van de Azure AD Provisioning-service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Azure Databricks SCIM Connector
> * Gebruikers verwijderen in Azure Databricks SCIM Connector wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Azure Databricks SCIM Connector
> * Groepen en groepslidmaatschappen inrichten in Azure Databricks SCIM Connector

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikersaccount in Azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) om provisioning te configureren (bijvoorbeeld toepassingsbeheerder, cloudtoepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een Azure Databricks-account met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Step 1. Uw inrichtingsimplementatie plannen
1. Meer informatie over [hoe de inprovisioningservice werkt.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Bepaal wie in de ruimte voor [de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)zal zijn .
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Azure Databricks SCIM Connector](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>Stap 2. Azure Databricks SCIM Connector configureren om provisioning met Azure AD te ondersteunen

1. Als u Azure Databricks SCIM-inrichting wilt instellen, voegt u deze toe als een bron in uw Azure Active Directory-tenant en configureert u deze met behulp van de onderstaande instellingen.

    ![Azure Databricks-installatie](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Als u een persoonlijk toegangstoken wilt genereren in Azure Databricks, verwijst [u dit](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management)door.

3. Kopieer de **token**. Deze waarde wordt ingevoerd in het veld Geheim token op het tabblad Provisioning van uw Azure Databricks SCIM Connector-toepassing in de Azure-portal.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>Stap 3. Azure Databricks SCIM-connector toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg Azure Databricks SCIM Connector toe vanuit de Azure AD-toepassingsgalerie om de inrichting voor Azure Databricks SCIM Connector te beheren. Als u azure Databricks SCIM Connector voor SSO eerder hebt ingesteld, u dezelfde toepassing gebruiken. Het wordt echter aanbevolen om een aparte app te maken bij het testen van de integratie in eerste instantie. Meer informatie over het toevoegen van een toepassing uit de galerie [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Bepalen wie in het vermogen van de inrichting 

Met de Azure AD-inrichtingsservice u scopen die worden ingericht op basis van toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker /groep. Als u ervoor kiest om het bereik te bepalen wie op basis van toewijzing aan uw app wordt toegewezen, u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om scope die zal worden ingericht uitsluitend op basis van attributen van de gebruiker of groep, u gebruik maken van een scoping filter zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)beschreven. 

* Wanneer u gebruikers en groepen toewijs aan Azure Databricks SCIM Connector, moet u een andere rol dan **Standaardtoegang**selecteren. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten en worden gemarkeerd als niet effectief gerechtigd in de inrichtingslogboeken. Als de enige rol die beschikbaar is in de toepassing de standaardtoegangsrol is, u [het toepassingsmanifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een kleine set gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer de inrichtingsruimte is ingesteld op toegewezen gebruikers en groepen, u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, u een [op kenmerken gebaseerd scopingfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>Stap 5. Automatische gebruikersinrichting configureren voor Azure Databricks SCIM-connector 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!NOTE]
> Raadpleeg [dit](https://docs.databricks.com/dev-tools/api/latest/scim.html
)voor meer informatie over het SCIM-eindpunt van Azure Databricks.

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor Azure Databricks SCIM Connector in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer Azure **Databricks SCIM Connector**in de lijst met toepassingen .

    ![De koppeling Azure Databricks SCIM Connector in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** de scim-eindpuntwaarde in **tenant-URL**in . De tenant-URL moet `https://<region>.azuredatabricks.net/api/2.0/preview/scim` zich bevinden in de indeling waarin de **regio** te vinden is in de URL van de azure databricks-startpagina. Een SCIM-eindpunt voor **de westusregio** is `https://westus.azuredatabricks.net/api/2.0/preview/scim`bijvoorbeeld . Voer de tokenwaarde in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Azure Databricks SCIM Connector. Als de verbinding mislukt, moet u ervoor zorgen dat uw Azure Databricks SCIM Connector-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Provisioning](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fout moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden in wanneer er een fout optreedt.**

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer azure **active directory-gebruikers synchroniseren met Azure Databricks SCIM-connector**onder de sectie **Toewijzingen** .

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Azure Databricks SCIM Connector in de sectie **Attribute-Mapping.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Azure Databricks SCIM Connector te matchen voor updatebewerkingen. Als u ervoor kiest het [overeenkomende doelkenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de Azure Databricks SCIM Connector API filteringgebruikers ondersteunt op basis van dat kenmerk. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |displayName|Tekenreeks|
   |actief|Booleaans|

10. Selecteer azure **Active Directory-groepen synchroniseren met Azure Databricks SCIM-connector**onder de sectie **Toewijzingen** .

11. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Azure Databricks SCIM Connector in de sectie **Attribute-Mapping.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepen in Azure Databricks SCIM Connector te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

     |Kenmerk|Type|
     |---|---|
     |displayName|Tekenreeks|
     |leden|Naslaginformatie|

11. Selecteer azure **Active Directory-groepen synchroniseren met Azure Databricks SCIM-connector**onder de sectie **Toewijzingen** .

12. Als u de Azure AD-inrichtingsservice voor Azure Databricks SCIM-connector wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

13. Definieer de gebruikers en/of groepen die u wilt inrichten voor Azure Databricks SCIM Connector door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

14. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste cyclus duurt langer om uit te voeren dan de volgende cycli, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Zodra u de inrichting hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te controleren:

* Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers met succes of zonder succes zijn ingericht
* Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus te bekijken en hoe dicht deze bij voltooiing is
* Als de inrichtingsconfiguratie in een ongezonde status lijkt te zijn, wordt de toepassing in quarantaine geplaatst. Meer informatie over quarantainestaten [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing
* Databricks zet altijd hun gebruikersnaamwaarden om in kleine letters bij het opslaan naar hun directory, ongeacht de hoofdletters die we via SCIM naar hen sturen.
* Momenteel get aanvragen tegen Azure Databricks 'SCIM API voor gebruikers USER@contoso.com zijn hoofdlettergevoelig, dus als we user@contoso.comvragen voor het zal komen met 0 resultaten als ze het opslaan als .

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../manage-apps/check-status-user-account-provisioning.md)
