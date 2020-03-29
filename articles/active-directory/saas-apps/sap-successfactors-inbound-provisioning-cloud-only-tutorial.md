---
title: 'Zelfstudie: Succesfactoren binnenkomende inrichting configureren in Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van inkomende inrichting van SuccessFactors naar Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: fac4f61e-d942-4429-a298-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 09501a80d6ddcbbc9fa6cc08e36f47beb13d1663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063219"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>Zelfstudie: SAP-succesfactoren configureren voor azure AD-gebruikersinrichting (Voorbeeld)
Het doel van deze zelfstudie is om de stappen weer te geven die u moet uitvoeren om werknemersgegevens van SuccessFactors Employee Central in Te richten in Azure Active Directory, met optionele terugschrijfgegevens van e-mailadres naar SuccessFactors. Deze integratie is in openbare preview en ondersteunt het ophalen van meer dan [70 + gebruikerskenmerken](../app-provisioning/sap-successfactors-attribute-reference.md) van SuccessFactors Employee Central. 

>[!NOTE]
>Gebruik deze zelfstudie als de gebruikers die u wilt inrichten van SuccessFactors alleen cloudgebruikers zijn die geen on-premises AD-account nodig hebben. Als de gebruikers alleen on-premises AD-account of zowel AD- als Azure AD-account nodig hebben, raadpleegt u de zelfstudie over [het configureren van SAP SuccessFactors naar Active Directory-gebruikersinrichting.](sap-successfactors-inbound-provisioning-tutorial.md#overview) 

## <a name="overview"></a>Overzicht

De [Azure Active Directory-service voor het inrichten van gebruikers](../app-provisioning/user-provisioning.md) integreert met de Service [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) om de levenscyclus van gebruikers te beheren. 

De gebruikersinrichtingsworkflows van SuccesFactors die worden ondersteund door de Azure AD-service voor het inrichten van gebruikers, maken automatisering mogelijk van de volgende scenario's voor het beheer van human resources en identiteitslevenscyclus:

* **Nieuwe werknemers inhuren** - Wanneer een nieuwe werknemer wordt toegevoegd aan SuccessFactors, wordt een gebruikersaccount automatisch gemaakt in Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD,](../app-provisioning/user-provisioning.md)met terugschrijfinformatie van het e-mailadres naar SuccessFactors.

* **Werknemerskenmerk en profielupdates** : Wanneer een werknemersrecord wordt bijgewerkt in SuccessFactors (zoals hun naam, titel of manager), wordt hun gebruikersaccount automatisch bijgewerkt in Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD.](../app-provisioning/user-provisioning.md)

* **Werknemersbeëindigingen** - Wanneer een werknemer wordt beëindigd in SuccessFactors, wordt zijn gebruikersaccount automatisch uitgeschakeld in Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD.](../app-provisioning/user-provisioning.md)

* **Werknemer rehires** - Wanneer een werknemer opnieuw wordt aangenomen in SuccessFactors, kan hun oude account automatisch worden gereactiveerd of opnieuw ingericht (afhankelijk van uw voorkeur) naar Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD.](../app-provisioning/user-provisioning.md)

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Voor wie is deze gebruikersinrichtingsoplossing het meest geschikt?

Deze Oplossing voor het inrichten van Azure Active Directory-gebruikers is bij uitstek geschikt voor:

* Organisaties die een vooraf gebouwde, cloudgebaseerde oplossing willen voor de gebruikersinrichting van SuccessFactors

* Organisaties die directe gebruikersvoorziening van SuccessFactors naar Azure Active Directory vereisen

* Organisaties die vereisen dat gebruikers worden ingericht met behulp van gegevens die zijn verkregen uit de [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisaties die Office 365 gebruiken voor e-mail

## <a name="solution-architecture"></a>Oplossingsarchitectuur

In deze sectie wordt de end-to-end-inrichtingsoplossingsarchitectuur voor alleen cloudgebruikers beschreven. Er zijn twee gerelateerde stromen:

* **Gezaghebbende HR-gegevensstroom – van SuccesFactors tot Azure Active Directory:** In deze stroomwerkgebeurtenissen (zoals Nieuwe aanwervingen, Overdrachten, Beëindigingen) vinden eerst plaats in de cloud SuccessFactors Employee Central en vervolgens stromen de gebeurtenisgegevens uit in Azure Active Directory. Afhankelijk van de gebeurtenis kan dit leiden tot het maken/bijwerken/inschakelen/uitschakelen van bewerkingen in Azure AD.
* **E-mail Writeback Flow – van on-premises Active Directory naar SuccessFactors:** Zodra het aanmaken van een account is voltooid in Azure Active Directory, kan de waarde van het e-mailkenmerk of de UPN die is gegenereerd in Azure AD, worden teruggeschreven naar SuccessFactors.

  ![Overzicht](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Gegevensstroom van eind-tot-eindgebruiker

1. Het HR-team voert werknemerstransacties uit (Joiners/Verhuizers/Leavers of New Hires/Transfers/Terminations) in SuccessFactors Employee Central
2. De Azure AD Provisioning Service voert geplande synchronisaties van identiteiten van SuccessFactors EC uit en identificeert wijzigingen die moeten worden verwerkt voor synchronisatie met on-premises Active Directory.
3. De Azure AD Provisioning Service bepaalt de wijziging en roept create/update/enable/disable operation in voor de gebruiker in Azure AD.
4. Als de [App SuccessFactors Writeback](sap-successfactors-writeback-tutorial.md) is geconfigureerd, wordt het e-mailadres van de gebruiker opgehaald uit Azure AD. 
5. Azure AD-inrichtingsservice schrijft e-mailkenmerk terug aan SuccessFactors, op basis van het gebruikte overeenkomende kenmerk.

## <a name="planning-your-deployment"></a>Uw implementatie plannen

Het configureren van cloud HR-gestuurde gebruikersinrichting van SuccessFactors naar Azure AD vereist een aanzienlijke planning voor verschillende aspecten, zoals:

* De overeenkomende id bepalen 
* Kenmerktoewijzing
* Kenmerktransformatie 
* Bereikfilters

Raadpleeg het [cloud HR-implementatieplan](../app-provisioning/plan-cloud-hr-provision.md) voor uitgebreide richtlijnen rond deze onderwerpen. 

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

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Gebruikersvoorziening configureren van SuccessFactors naar Azure AD

In deze sectie vindt u stappen voor het inrichten van gebruikersaccounts van SuccessFactors naar Azure AD.

* [De inrichtingsconnector-app toevoegen en connectiviteit configureren met SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Kenmerkentoewijzingen configureren](#part-2-configure-attribute-mappings)
* [Gebruikersinrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Deel 1: De inrichtingsconnector-app toevoegen en connectiviteit configureren met SuccessFactors

**Ga als eerste voor een configureren op Azure AD-inrichting:**

1. Ga naar <https://portal.azure.com>

2. Selecteer **Azure Active Directory** op de linkernavigatiebalk

3. Selecteer **Enterprise-toepassingen**en **vervolgens Alle toepassingen**.

4. Selecteer **Een toepassing toevoegen**en selecteer de categorie **Alles.**

5. Zoek **naar SuccesFactors naar Azure Active Directory User Provisioning**en voeg die app toe vanuit de galerie.

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
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Zodra de referenties zijn opgeslagen, wordt in de sectie **Toewijzingen** de standaardtoewijzing **Spaarfactorengebruikers gesynchroniseerd in Azure Active Directory weergegeven.**

### <a name="part-2-configure-attribute-mappings"></a>Deel 2: Toewijzingen van kenmerken configureren

In deze sectie configureert u hoe gebruikersgegevens stromen van SuccessFactors naar Active Directory.

1. Klik op het tabblad Inrichten onder **Toewijzingen**op **Gebruikers van Succesfactoren synchroniseren naar Azure Active Directory**.

1. In het veld **Bronobjectbereik** u selecteren welke sets gebruikers in SuccessFactors in het bereik van Azure moeten zijn, door een set op kenmerken gebaseerde filters te definiëren. De standaardscope is "alle gebruikers in SuccessFactors". Voorbeeldfilters:

   * Voorbeeld: Scope voor gebruikers met personIdExternal tussen 1000000 en 2000000 (exclusief 2000000)

      * Kenmerk: personIdExternal

      * Operator: REGEX Match

      * Waarde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Voorbeeld: Alleen werknemers en geen voorwaardelijke werknemers

      * Kenmerk: EmployeeID

      * Operator: IS NIET NULL

   > [!TIP]
   > Wanneer u de inrichtingsapp voor de eerste keer configureert, moet u uw kenmerktoewijzingen en -expressies testen en verifiëren om ervoor te zorgen dat deze het gewenste resultaat oplevert. Microsoft raadt aan om de scopingfilters onder **Source Object Scope** te gebruiken om uw toewijzingen te testen met een paar testgebruikers van SuccessFactors. Zodra u hebt geverifieerd dat de toewijzingen werken, u het filter verwijderen of geleidelijk uitbreiden met meer gebruikers.

   > [!CAUTION] 
   > Het standaardgedrag van de inrichtingsengine is het uitschakelen/verwijderen van gebruikers die buiten het bereik vallen. Dit is mogelijk niet wenselijk in uw SuccessFactors naar Azure AD-integratie. Als u dit standaardgedrag wilt overschrijven, verwijst u naar het artikel [Verwijdering van gebruikersaccounts](../app-provisioning/skip-out-of-scope-deletions.md) overslaan die buiten het bereik vallen
  
1. In het veld **Doelobjectacties** u globaal filteren welke acties worden uitgevoerd in Active Directory. **Maken** en **bijwerken** komen het meest voor.

1. In de sectie **Toewijzingen van kenmerken** u definiëren hoe afzonderlijke SuccessFactors-kenmerken worden toegewezen aan Active Directory-kenmerken.

  >[!NOTE]
  >Raadpleeg de verwijzing naar [successfactors-attribuut Naslagrecht](../app-provisioning/sap-successfactors-attribute-reference.md) voor de volledige lijst met kenmerken van SuccessFactors die door de toepassing wordt ondersteund


1. Klik op een bestaande kenmerktoewijzing om deze bij te werken of klik op **Nieuwe toewijzing** onder aan het scherm toevoegen om nieuwe toewijzingen toe te voegen. Een afzonderlijke kenmerktoewijzing ondersteunt deze eigenschappen:

      * **Toewijzingstype**

         * **Direct** : hiermee schrijft u de waarde van het kenmerk SuccesFactors aan het AD-kenmerk, zonder wijzigingen

         * **Constant** - Schrijf een statische, constante tekenreekswaarde naar het AD-kenmerk

         * **Expressie** : hiermee u een aangepaste waarde naar het AD-kenmerk schrijven op basis van een of meer kenmerken van SuccessFactors. [Zie dit artikel over expressies voor meer informatie.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Bronkenmerk** - Het gebruikerskenmerk van SuccessFactors

      * **Standaardwaarde** – Optioneel. Als het bronkenmerk een lege waarde heeft, wordt deze waarde in de toewijzing geschreven.
            Meest voorkomende configuratie is om deze leeg te laten.

      * **Kenmerk Doel** : het gebruikerskenmerk in Active Directory.

      * **Objecten matchen met dit kenmerk** : of deze toewijzing al dan niet moet worden gebruikt om gebruikers op unieke wijze te identificeren tussen SuccessFactors en Active Directory. Deze waarde wordt meestal ingesteld op het veld Werknemers-id voor SuccesFactors, dat doorgaans is toegewezen aan een van de kenmerken van werknemers-id in Active Directory.

      * **Overeenkomende prioriteit** – Meerdere overeenkomende kenmerken kunnen worden ingesteld. Wanneer er meerdere zijn, worden ze geëvalueerd in de volgorde die door dit veld is gedefinieerd. Zodra een overeenkomst wordt gevonden, worden geen verdere overeenkomende kenmerken geëvalueerd.

      * **Deze toewijzing toepassen**

         * **Altijd** – Deze toewijzing toepassen op zowel acties voor het maken en bijwerken van gebruikers

         * **Alleen tijdens het maken** - Pas deze toewijzing alleen toe op acties voor het maken van gebruikers

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

* [Meer informatie over ondersteunde SuccessFactors-kenmerken voor inkomende inrichting](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Meer informatie over het configureren van e-mailterugschrijven naar SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen SuccessFactors en Azure Active Directory](successfactors-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het exporteren en importeren van uw inrichtingsconfiguraties](../app-provisioning/export-import-provisioning-configuration.md)


