---
title: 'Zelfstudie: Afschrijving van succesfactoren configureren in Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van attribuutafschrijving naar SuccessFactors vanuit Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060045"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Zelfstudie: Attribuutterugschrijven configureren van Azure AD naar SAP SuccessFactors (Voorbeeld)
Het doel van deze zelfstudie is om de stappen weer te geven die u moet uitvoeren om kenmerken van Azure AD naar SuccessFactors Employee Central terug te schrijven. Het enige kenmerk dat momenteel wordt ondersteund voor terugschrijven, is het e-mailkenmerk. 

## <a name="overview"></a>Overzicht

Nadat u inkomende integratie hebt ingesteld met behulp van [SuccessFactors voor on-premises AD-inrichtingsapp](sap-successfactors-inbound-provisioning-tutorial.md) of [SuccessFactors naar Azure](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) AD-inrichtingsapp, u de app SuccessFactors Writeback optioneel configureren om e-mailadres terug te schrijven naar SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Voor wie is deze gebruikersinrichtingsoplossing het meest geschikt?

Deze SuccessFactors Writeback-oplossing voor het inrichten van gebruikers is bij uitstek geschikt voor:

* Organisaties die Office 365 gebruiken die gezaghebbende kenmerken die door IT worden beheerd (zoals e-mailadres) willen terugschrijven naar SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>SuccessFactors configureren voor de integratie

Een veelvoorkomende vereiste van alle SuccessFactors-inrichtingsconnectoren is dat ze referenties van een SuccessFactors-account met de juiste machtigingen nodig hebben om de OData-API's van SuccessFactors aan te roepen. In deze sectie worden stappen beschreven om het serviceaccount in SuccessFactors te maken en de juiste machtigingen toe te kennen. 

* [API-gebruikersaccount maken/identificeren in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Een rol api-machtigingen maken](#create-an-api-permissions-role)
* [Een machtigingsgroep maken voor de API-gebruiker](#create-a-permission-group-for-the-api-user)
* [Machtigingsrol verlenen aan de machtigingsgroep](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API-gebruikersaccount maken/identificeren in SuccessFactors
Werk samen met uw SuccessFactors-beheerteam of implementatiepartner om een gebruikersaccount te maken of te identificeren in SuccessFactors dat wordt gebruikt om de OData-API's aan te roepen. De gebruikersnaam en wachtwoordreferenties van dit account zijn vereist bij het configureren van de inrichtingsapps in Azure AD. 

### <a name="create-an-api-permissions-role"></a>Een rol api-machtigingen maken

* Meld u aan bij SAP SuccessFactors met een gebruikersaccount dat toegang heeft tot het Beheercentrum.
* Zoek *naar Machtigingenrollen beheren*en selecteer **vervolgens Machtigingenrollen beheren** in de zoekresultaten.
  ![Machtigingsrollen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Klik in de lijst met machtigingsrollen op **Nieuw maken**.
  > [!div class="mx-imgBorder"]
  > ![Nieuwe machtigingsrol maken](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Voeg een **rolnaam** en **-beschrijving toe** voor de nieuwe machtigingsrol. De naam en beschrijving moeten aangeven dat de rol voor API-gebruiksmachtigingen is.
  > [!div class="mx-imgBorder"]
  > ![Machtigingsroldetail](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Klik onder Machtigingsinstellingen op **Machtiging...** en scrol vervolgens naar beneden in de machtigingslijst en klik op **Hulpmiddelen voor integratie beheren**. Schakel het selectievakje **Beheerder toegang geven tot De OData-API in via basisverificatie**.
  > [!div class="mx-imgBorder"]
  > ![Integratietools beheren](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Schuif omlaag in hetzelfde vak en selecteer **Employee Central API**. Voeg machtigingen toe zoals hieronder wordt weergegeven om te lezen met behulp van de ODATA API en bewerk met ODATA API. Selecteer de bewerkingsoptie als u van plan bent hetzelfde account te gebruiken voor het scenario Writeback to SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Schrijfmachtigingen lezen](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Klik op **Gereed**. Klik **op Wijzigingen opslaan**.

### <a name="create-a-permission-group-for-the-api-user"></a>Een machtigingsgroep maken voor de API-gebruiker

* Zoek in het SuccesFactors-beheercentrum naar *Machtigingsgroepen beheren*en selecteer **vervolgens Machtigingsgroepen beheren** in de zoekresultaten.
  > [!div class="mx-imgBorder"]
  > ![Machtigingsgroepen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Klik in het venster Machtigingsgroepen beheren op **Nieuw maken**.
  > [!div class="mx-imgBorder"]
  > ![Nieuwe groep toevoegen](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Voeg een groepsnaam toe voor de nieuwe groep. De groepsnaam moet aangeven dat de groep voor API-gebruikers is.
  > [!div class="mx-imgBorder"]
  > ![Naam van de machtigingsgroep](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Leden toevoegen aan de groep. U bijvoorbeeld **gebruikersnaam** selecteren in het vervolgkeuzemenu Groep Personen en vervolgens de gebruikersnaam invoeren van het API-account dat voor de integratie wordt gebruikt. 
  > [!div class="mx-imgBorder"]
  > ![Groepsleden toevoegen](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Klik **op Gereed** om de machtigingsgroep te maken.

### <a name="grant-permission-role-to-the-permission-group"></a>Machtigingsrol verlenen aan de machtigingsgroep

* Zoek in het SuccesFactors-beheercentrum naar *Machtigingenrollen beheren*en selecteer **vervolgens Machtigingsrollen beheren** in de zoekresultaten.
* Selecteer in de **lijst met machtigingsrollen**de rol die u hebt gemaakt voor API-gebruiksmachtigingen.
* Klik **onder Deze rol verlenen aan...**, klik op Knop **Toevoegen...**
* Selecteer **Machtigingsgroep...** klik in het vervolgkeuzemenu op **Selecteren...** om het venster Groepen te openen om te zoeken en selecteer de groep die hierboven is gemaakt. 
  > [!div class="mx-imgBorder"]
  > ![Machtigingsgroep toevoegen](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Controleer de machtigingsrolverlening aan de machtigingsgroep. 
  > [!div class="mx-imgBorder"]
  > ![Machtigingsrol en groepsgegevens](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Klik **op Wijzigingen opslaan**.

## <a name="configuring-successfactors-writeback"></a>Terugschrijven van succesfactoren configureren

In deze sectie vindt u stappen voor 

* [De inrichtingsconnector-app toevoegen en connectiviteit configureren met SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Kenmerkentoewijzingen configureren](#part-2-configure-attribute-mappings)
* [Gebruikersinrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Deel 1: De inrichtingsconnector-app toevoegen en connectiviteit configureren met SuccessFactors

**Ga als een te meer metsuccesfactoren terug:**

1. Ga naar <https://portal.azure.com>

2. Selecteer **Azure Active Directory** op de linkernavigatiebalk

3. Selecteer **Enterprise-toepassingen**en **vervolgens Alle toepassingen**.

4. Selecteer **Een toepassing toevoegen**en selecteer de categorie **Alles.**

5. Zoek naar **SuccessFactors Writeback**en voeg die app toe vanuit de galerij.

6. Nadat de app is toegevoegd en het scherm met app-details wordt weergegeven, selecteert u **Provisioning**

7. De **inrichtingsmodus** **Mode** wijzigen in **Automatisch**

8. Vul de sectie **Beheerdersreferenties** als volgt in:

   * **Gebruikersnaam beheerder** : voer de gebruikersnaam van het Account van de SuccessFactors API-gebruikers in, waarbij de bedrijfs-id is toegevoegd. Het heeft het formaat: **\@gebruikersnaam companyID**

   * **Beheerderswachtwoord –** Voer het wachtwoord in van het Account van de Api-gebruikersaccount van SuccessFactors. 

   * **URL van tenant –** Voer de naam in van het eindpunt van de OData API-services van SuccesFactors. Voer alleen de hostnaam van de server in zonder http of https. Deze waarde moet eruit zien als: **api-server-name.successfactors.com**.

   * **Melding e-mail –** Voer uw e-mailadres in en schakel het selectievakje 'E-mail verzenden als er mislukt' in.
    > [!NOTE]
    > De Azure AD Provisioning Service verzendt e-mailmelding als de inrichtingstaak in een [quarantainestatus](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) terechtkomt.

   * Klik op de knop **Verbinding testen.** Als de verbindingstest slaagt, klikt u bovenaan op de knop **Opslaan.** Als dit niet lukt, controleert u of de referenties van SuccessFactors en de URL geldig zijn.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Zodra de referenties zijn opgeslagen, wordt in de sectie **Toewijzingen** de standaardtoewijzing **seintoewijzing seerstatus van Azure Active Directory-gebruikers aan SuccessFactors weergegeven**

### <a name="part-2-configure-attribute-mappings"></a>Deel 2: Toewijzingen van kenmerken configureren

In deze sectie configureert u hoe gebruikersgegevens stromen van SuccessFactors naar Active Directory.

1. Klik op het tabblad Inrichten onder **Toewijzingen**op **Azure Active Directory Users synchroniseren met Succesfactoren**.

1. In het veld **Bronobjectbereik** u selecteren welke sets gebruikers in Azure AD in aanmerking moeten worden genomen voor Writeback, door een set op kenmerken gebaseerde filters te definiëren. De standaardscope is 'alle gebruikers in Azure AD'. 
   > [!TIP]
   > Wanneer u de inrichtingsapp voor de eerste keer configureert, moet u uw kenmerktoewijzingen en -expressies testen en verifiëren om ervoor te zorgen dat deze het gewenste resultaat oplevert. Microsoft raadt aan om de scopingfilters onder **Source Object Scope** te gebruiken om uw toewijzingen te testen met enkele testgebruikers van Azure AD. Zodra u hebt geverifieerd dat de toewijzingen werken, u het filter verwijderen of geleidelijk uitbreiden met meer gebruikers.

1. Het veld **Doelobjectacties** ondersteunt alleen de **bewerking Bijwerken.**

1. In de sectie **Toewijzingen van kenmerken** u alleen de overeenkomende id wijzigen die wordt gebruikt om een SuccesFactors-gebruikersprofiel te koppelen aan azure AD-gebruiker en welk kenmerk in Azure AD als bron van e-mail dient. 
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >De terugschrijftekst van SuccessFactors ondersteunt alleen het e-mailkenmerk. Gebruik Geen **Nieuwe toewijzing toevoegen** om nieuwe kenmerken toe te voegen. 

1. Als u uw toewijzingen wilt opslaan, klikt u boven aan de sectie Kenmerktoewijzing op **Opslaan.**

Zodra de configuratie van de toewijzing van kenmerken is voltooid, u [nu de service voor het inrichten van de gebruiker inschakelen en starten.](#enable-and-launch-user-provisioning)

## <a name="enable-and-launch-user-provisioning"></a>Gebruikersinrichting inschakelen en starten

Zodra de app-configuraties voor het inrichten van 1-1-apps zijn voltooid, u de inrichtingsservice inschakelen in de Azure-portal.

> [!TIP]
> Wanneer u de inrichtingsservice inschakelt, wordt standaard de inrichtingsbewerking gestart voor alle gebruikers in het bereik. Als er fouten zijn in de problemen met de toewijzing of workday-gegevens, kan de inrichtingstaak mislukken en in de quarantainestatus gaan. Om dit te voorkomen, raden we u aan het filter **Source Object Scope** te configureren en uw kenmerktoewijzingen te testen met een paar testgebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Zodra u hebt geverifieerd dat de toewijzingen werken en geven u de gewenste resultaten, dan u ofwel verwijderen van het filter of geleidelijk uit te breiden naar meer gebruikers op te nemen.

1. Stel op het tabblad **Inrichten** de **inrichtingsstatus** in **op Aan**.

2. Klik op **Opslaan**.

3. Met deze bewerking wordt de eerste synchronisatie gestart, die een variabel aantal uren kan duren, afhankelijk van het aantal gebruikers dat zich in de tenant SuccessFactors bevindt. U de voortgangsbalk controleren om de voortgang van de synchronisatiecyclus bij te houden. 

4. Controleer op elk gewenst moment het tabblad **Controlelogboeken** in de Azure-portal om te zien welke acties de inrichtingsservice heeft uitgevoerd. De controlelogboeken bevatten alle afzonderlijke synchronisatiegebeurtenissen die door de inrichtingsservice worden uitgevoerd, zoals welke gebruikers buiten Workday worden voorgelezen en vervolgens worden toegevoegd of bijgewerkt naar Active Directory. 

5. Zodra de eerste synchronisatie is voltooid, wordt een controleoverzichtrapport geschreven op het tabblad **Inname,** zoals hieronder wordt weergegeven.

   > [!div class="mx-imgBorder"]
   > ![Voortgangsbalk inrichten](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen SuccessFactors en Azure Active Directory](successfactors-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het exporteren en importeren van uw inrichtingsconfiguraties](../app-provisioning/export-import-provisioning-configuration.md)

