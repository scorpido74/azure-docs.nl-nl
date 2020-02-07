---
title: 'Zelf studie: SuccessFactors inrichtings inrichting configureren in Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van binnenkomende Provisioning van SuccessFactors naar Azure AD
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063219"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>Zelf studie: SAP SuccessFactors configureren voor Azure AD-gebruikers inrichten (preview-versie)
Het doel van deze zelf studie is het weer geven van de stappen die u moet uitvoeren om werk nemers in te richten op SuccessFactors-werk nemers centraal in Azure Active Directory, met een optionele write-back van e-mail adres naar SuccessFactors. Deze integratie is in open bare preview en ondersteunt het ophalen van meer dan [70 + gebruikers kenmerken](../app-provisioning/sap-successfactors-attribute-reference.md) van SuccessFactors Employee Central. 

>[!NOTE]
>Gebruik deze zelf studie als de gebruikers die u wilt inrichten via SuccessFactors alleen Cloud gebruikers zijn die geen on-premises AD-account nodig hebben. Als de gebruikers alleen een on-premises AD-account of AD-en Azure AD-account nodig hebben, raadpleegt u de zelf studie over het [configureren van SAP SuccessFactors om](sap-successfactors-inbound-provisioning-tutorial.md#overview) de gebruikers inrichting te Active Directory. 

## <a name="overview"></a>Overzicht

De [Azure Active Directory User Provisioning Service](../app-provisioning/user-provisioning.md) kan worden geïntegreerd met het [SuccessFactors van werk nemers](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) , zodat de identiteits levenscyclus van gebruikers kan worden beheerd. 

De werk stromen voor het inrichten van SuccessFactors-gebruikers die worden ondersteund door de Azure AD User Provisioning-Service, maken het mogelijk om de volgende scenario's voor human resources en Identity Lifecycle Management te automatiseren:

* **Nieuwe werk nemers inhuren** : wanneer een nieuwe werk nemer wordt toegevoegd aan SuccessFactors, wordt automatisch een gebruikers account gemaakt in azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md), met een terugschrijf bewerking van het e-mail adres naar SuccessFactors.

* **Updates van werknemers kenmerken en-profielen** : wanneer een werknemers record wordt bijgewerkt in SuccessFactors (zoals hun naam, titel of Manager), wordt het gebruikers account automatisch bijgewerkt Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

* **Beëindiging van werk nemers** : wanneer een werk nemer wordt beëindigd in SuccessFactors, wordt het gebruikers account automatisch uitgeschakeld in azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

* Opnieuw **inhuren van werk nemers** : wanneer een werk nemer opnieuw wordt ingehuurd in SuccessFactors, kan het oude account automatisch opnieuw worden geactiveerd of worden ingericht (afhankelijk van uw voor keur) naar Azure Active Directory en optioneel Office 365 en [andere SaaS-toepassingen die worden ondersteund door Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Wie is deze oplossing voor het inrichten van de gebruiker geschikt voor?

Deze SuccessFactors voor het Azure Active Directory van de oplossing voor het inrichten van gebruikers is in het ideale geval geschikt voor:

* Organisaties die behoefte hebben aan een vooraf ontwikkelde, op de cloud gebaseerde oplossing voor het inrichten van SuccessFactors-gebruikers

* Organisaties die directe gebruikers inrichting van SuccessFactors naar Azure Active Directory vereisen

* Organisaties die gebruikers moeten inrichten op basis van gegevens die zijn verkregen van de [SuccessFactors Employee Central (EG)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisaties die Office 365 gebruiken voor e-mail

## <a name="solution-architecture"></a>Architectuur van de oplossing

In deze sectie wordt de end-to-end-architectuur voor het inrichten van gebruikers voor Cloud gebruikers beschreven. Er zijn twee gerelateerde stromen:

* **Gezaghebbende HR-gegevens stroom: van SuccessFactors tot Azure Active Directory:** In deze stroom worden werk gebeurtenissen (zoals nieuwe aanmeldingen, overdrachten, afsluitingen) voor het eerst uitgevoerd in de Cloud SuccessFactors werk nemer Central en vervolgens worden de gebeurtenis gegevens stromen naar Azure Active Directory. Afhankelijk van de gebeurtenis kan dit leiden tot het maken/bijwerken/inschakelen/uitschakelen van bewerkingen in azure AD.
* **Write-back-upflow: van on-premises Active Directory naar SuccessFactors:** Zodra het account is gemaakt in Azure Active Directory, kan de waarde van het e-mail kenmerk of de UPN die in azure AD is gegenereerd, worden teruggeschreven naar SuccessFactors.

  ![Overzicht](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Gegevens stroom van end-to-end-gebruikers

1. Het HR-team voert werknemers transacties (samenvoegers/verplaatsingen/Leavers of nieuwe huur/overdrachten/afsluitingen) uit in SuccessFactors werk nemers centraal
2. De Azure AD-inrichtings service voert geplande synchronisaties uit van identiteiten van SuccessFactors EC en identificeert wijzigingen die moeten worden verwerkt om te synchroniseren met on-premises Active Directory.
3. De Azure AD-inrichtings service bepaalt de wijziging en roept de bewerking voor het maken/bijwerken/inschakelen/uitschakelen van de gebruiker aan in azure AD.
4. Als de [SuccessFactors write-app](sap-successfactors-writeback-tutorial.md) is geconfigureerd, wordt het e-mail adres van de gebruiker opgehaald van Azure AD. 
5. Azure AD Provisioning Service schrijft het e-mail kenmerk terug naar SuccessFactors, op basis van het gebruikte kenmerk dat wordt gebruikt.

## <a name="planning-your-deployment"></a>Uw implementatie plannen

Het configureren van in de Cloud HR gestuurde gebruikers inrichten van SuccessFactors naar Azure AD vereist een aanzienlijke planning voor verschillende aspecten, zoals:

* De overeenkomende ID bepalen 
* Kenmerk toewijzing
* Kenmerk transformatie 
* Bereikfilters

Raadpleeg het [implementatie plan voor Cloud HR](../app-provisioning/plan-cloud-hr-provision.md) voor uitgebreide richt lijnen rond deze onderwerpen. 

## <a name="configuring-successfactors-for-the-integration"></a>SuccessFactors configureren voor de integratie

Een algemene vereiste van alle SuccessFactors-inrichtings connectors is dat er referenties van een SuccessFactors-account nodig zijn met de juiste machtigingen om de SuccessFactors OData-Api's aan te roepen. In deze sectie worden de stappen beschreven voor het maken van het service account in SuccessFactors en het verlenen van de juiste machtigingen. 

* [API-gebruikers account maken/identificeren in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Een functie voor API-machtigingen maken](#create-an-api-permissions-role)
* [Een machtigings groep maken voor de API-gebruiker](#create-a-permission-group-for-the-api-user)
* [Machtigings functie verlenen aan de machtigings groep](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API-gebruikers account maken/identificeren in SuccessFactors
Werk samen met uw SuccessFactors-beheer team of implementatie partner om een gebruikers account in SuccessFactors te maken of te identificeren dat wordt gebruikt om de OData-Api's aan te roepen. De referenties van de gebruikers naam en het wacht woord van dit account zijn vereist bij het configureren van de inrichtings-apps in azure AD. 

### <a name="create-an-api-permissions-role"></a>Een functie voor API-machtigingen maken

* Meld u aan bij SAP SuccessFactors met een gebruikers account dat toegang heeft tot het beheer centrum.
* Zoek naar *machtigings rollen beheren*en selecteer vervolgens **machtigings rollen beheren** in de zoek resultaten.
  Machtigings rollen ![beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Klik in de lijst machtigings rollen op **nieuwe maken**.
  > [!div class="mx-imgBorder"]
  > ![nieuwe machtigings functie maken](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Voeg een **rolnaam** en een **Beschrijving** voor de nieuwe machtigings functie toe. De naam en beschrijving moeten aangeven dat de rol is voor API-gebruiks machtigingen.
  > [!div class="mx-imgBorder"]
  > Details van ![-machtigings rol](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Klik onder machtigings instellingen op **machtiging...** , Schuif omlaag in de lijst met machtigingen en klik op **integratie hulpprogramma's beheren**. Schakel het selectie vakje in om de beheerder toe te **staan om toegang te krijgen tot ODATA API via basis verificatie**.
  > [!div class="mx-imgBorder"]
  > ![integratie hulpprogramma's beheren](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Schuif omlaag in hetzelfde vak en selecteer de **centrale API voor werk nemers**. Voeg machtigingen toe, zoals hieronder wordt weer gegeven, voor meer informatie over het gebruik van ODATA API en Edit met de ODATA-API. Selecteer de optie bewerken als u van plan bent hetzelfde account te gebruiken voor het terugschrijven naar het SuccessFactors-scenario. 
  > [!div class="mx-imgBorder"]
  > ![lees schrijf machtigingen](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Klik op **gereed**. Klik op **Wijzigingen opslaan**.

### <a name="create-a-permission-group-for-the-api-user"></a>Een machtigings groep maken voor de API-gebruiker

* Zoek in het SuccessFactors-beheer centrum naar *machtigings groepen beheren*en selecteer vervolgens **machtigings groepen beheren** in de zoek resultaten.
  > [!div class="mx-imgBorder"]
  > ![beheren van machtigings groepen](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Klik in het venster machtigings groepen beheren op **nieuwe maken**.
  > [!div class="mx-imgBorder"]
  > nieuwe groep ![toevoegen](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Voeg een groeps naam voor de nieuwe groep toe. De groeps naam moet aangeven dat de groep voor API-gebruikers is.
  > [!div class="mx-imgBorder"]
  > ![naam van de machtigings groep](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Leden toevoegen aan de groep. U kunt bijvoorbeeld **gebruikers naam** selecteren in de vervolg keuzelijst personen groep en vervolgens de gebruikers naam invoeren van het API-account dat wordt gebruikt voor de integratie. 
  > [!div class="mx-imgBorder"]
  > ![Groepsleden toevoegen](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Klik op **gereed** om het maken van de machtigings groep te volt ooien.

### <a name="grant-permission-role-to-the-permission-group"></a>Machtigings functie verlenen aan de machtigings groep

* Zoek in SuccessFactors-beheer centrum naar *machtigings rollen beheren*en selecteer vervolgens **machtigings rollen beheren** in de zoek resultaten.
* Selecteer in de **lijst machtigings rollen**de rol die u hebt gemaakt voor de machtigingen voor API-gebruik.
* Onder **deze rol toekennen aan..** . klikt u op de knop **toevoegen..** ..
* Selecteer de **machtigings groep..** . in de vervolg keuzelijst en klik vervolgens op **selecteren...** om het venster groepen te openen, te zoeken en de eerder gemaakte groep te selecteren. 
  > [!div class="mx-imgBorder"]
  > ![een machtigings groep toe](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Controleer de machtigings rol verlenen aan de machtigings groep. 
  > [!div class="mx-imgBorder"]
  > ![machtigings-en groeps detail](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Klik op **Wijzigingen opslaan**.

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Gebruikers inrichten van SuccessFactors naar Azure AD configureren

In deze sectie vindt u de stappen voor het inrichten van gebruikers accounts van SuccessFactors naar Azure AD.

* [De inrichtings connector-app toevoegen en connectiviteit configureren voor SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Kenmerk toewijzingen configureren](#part-2-configure-attribute-mappings)
* [Gebruikers inrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Deel 1: de app voor de inrichtings connector toevoegen en connectiviteit met SuccessFactors configureren

**SuccessFactors configureren voor Azure AD-inrichting:**

1. Ga naar <https://portal.azure.com>

2. Selecteer in de linker navigatie balk de optie **Azure Active Directory**

3. Selecteer **bedrijfs toepassingen**en vervolgens **alle toepassingen**.

4. Selecteer **een toepassing toevoegen**en selecteer de categorie **alle** .

5. Zoek **SuccessFactors naar Azure Active Directory gebruikers inrichten**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weer gegeven, selecteert u **inrichting** maken

7. De **inrichtings** **modus** wijzigen in **automatisch**

8. Voer de sectie **beheerders referenties** als volgt uit:

   * **Gebruikers naam beheerder** : Voer de gebruikers naam in van het gebruikers account van de SUCCESSFACTORS-API, waarbij de bedrijfs-id is toegevoegd. Het heeft de volgende indeling: **gebruikers naam\@companyID**

   * **Beheerders wachtwoord –** Voer het wacht woord van het gebruikers account van de SuccessFactors-API in. 

   * **Tenant-URL:** Voer de naam van het SuccessFactors OData API services-eind punt in. Voer alleen de hostnaam van de server in zonder http of https. Deze waarde moet er als volgt uitzien: **API-server-name.successfactors.com**.

   * **E-mail melding-** Voer uw e-mail adres in en schakel het selectie vakje e-mail verzenden als er een fout is opgetreden in.
    > [!NOTE]
    > De Azure AD-inrichtings service verzendt een e-mail melding als de inrichtings taak een [quarantaine](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) status heeft.

   * Klik op de knop **verbinding testen** . Als de verbindings test is geslaagd, klikt u bovenaan op de knop **Opslaan** . Als dit mislukt, controleert u of de SuccessFactors-referenties en-URL geldig zijn.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Zodra de referenties zijn opgeslagen, wordt in de sectie **toewijzingen** de standaard toewijzing weer gegeven **SuccessFactors-gebruikers synchroniseren met Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>Deel 2: kenmerk toewijzingen configureren

In deze sectie configureert u hoe gebruikers gegevens stromen van SuccessFactors naar Active Directory.

1. Klik op het tabblad inrichting onder **toewijzingen**op **SuccessFactors gebruikers synchroniseren om Azure Active Directory**.

1. In het veld **bereik van bron object** kunt u selecteren welke groepen gebruikers in SuccessFactors het bereik moeten hebben voor het inrichten van Azure AD door een set op kenmerken gebaseerde filters te definiëren. Het standaard bereik is alle gebruikers in SuccessFactors. Voorbeeld filters:

   * Voor beeld: bereik voor gebruikers met personIdExternal tussen 1000000 en 2000000 (m.u.v. 2000000)

      * Kenmerk: personIdExternal

      * Operator: overeenkomende REGEX

      * Waarde: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Voor beeld: alleen werk nemers en niet-voorwaardelijke werk nemers

      * Kenmerk: EmployeeID

      * Operator: IS niet NULL

   > [!TIP]
   > Wanneer u de inrichtings-app voor de eerste keer configureert, moet u de kenmerk toewijzingen en expressies testen en controleren om ervoor te zorgen dat het gewenste resultaat wordt weer geven. Micro soft raadt aan om de bereik filters onder het bereik van de **bron object** te gebruiken om uw toewijzingen te testen met een aantal test gebruikers van SuccessFactors. Wanneer u hebt gecontroleerd of de toewijzingen werken, kunt u het filter verwijderen of het bestand geleidelijk uitbreiden om meer gebruikers op te geven.

   > [!CAUTION] 
   > Het standaard gedrag van de inrichtings engine is het uitschakelen/verwijderen van gebruikers die buiten het bereik vallen. Dit is mogelijk niet wenselijk in uw SuccessFactors naar Azure AD-integratie. Als u dit standaard gedrag wilt overschrijven, raadpleegt u het artikel [verwijdering overs laan van gebruikers accounts die buiten het bereik](../app-provisioning/skip-out-of-scope-deletions.md) vallen
  
1. In het veld **acties doel object** kunt u globaal filteren welke acties er worden uitgevoerd op Active Directory. **Create** en **Update** worden het meest gebruikt.

1. In de sectie **kenmerk toewijzingen** kunt u definiëren hoe afzonderlijke SuccessFactors kenmerken worden toegewezen aan Active Directory kenmerken.

  >[!NOTE]
  >Voor de volledige lijst met SuccessFactors-kenmerken die door de toepassing worden ondersteund, verwijzen wij u naar [SuccessFactors-kenmerk verwijzing](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Klik op een bestaande kenmerk toewijzing om het bij te werken of Klik onder aan het scherm op **nieuwe toewijzing toevoegen** om nieuwe toewijzingen toe te voegen. Een individuele kenmerk toewijzing ondersteunt de volgende eigenschappen:

      * **Toewijzings type**

         * **Direct** : schrijft de waarde van het kenmerk SuccessFactors naar het kenmerk AD, zonder wijzigingen

         * **Constante** : een statische, constante teken reeks waarde schrijven naar het AD-kenmerk

         * **Expressie** : Hiermee kunt u een aangepaste waarde schrijven naar het AD-kenmerk, op basis van een of meer SuccessFactors-kenmerken. [Zie dit artikel over expressies voor meer informatie](../app-provisioning/functions-for-customizing-application-data.md).

      * **Bron kenmerk** -het gebruikers kenmerk van SuccessFactors

      * **Standaard waarde** : optioneel. Als het bron kenmerk een lege waarde heeft, wordt deze waarde in plaats daarvan door de toewijzing geschreven.
            De meest voorkomende configuratie is om dit leeg te laten.

      * **Doel kenmerk** : het gebruikers kenmerk in Active Directory.

      * **Objecten met dit kenmerk matchen** : bepaalt of deze toewijzing moet worden gebruikt om gebruikers te identificeren tussen SuccessFactors en Active Directory. Deze waarde wordt doorgaans ingesteld op het veld worker-ID voor SuccessFactors, die meestal wordt toegewezen aan een van de kenmerken van de werk nemer-ID in Active Directory.

      * **Overeenkomende prioriteit** : meerdere overeenkomende kenmerken kunnen worden ingesteld. Wanneer er meerdere zijn, worden deze geëvalueerd in de volg orde die is gedefinieerd door dit veld. Zodra er een overeenkomst wordt gevonden, worden er geen verdere overeenkomende kenmerken geëvalueerd.

      * **Deze toewijzing Toep assen**

         * **Altijd** : deze toewijzing Toep assen op zowel het maken van gebruikers als bij het bijwerken van acties

         * **Alleen tijdens het maken** : pas deze toewijzing alleen toe bij het maken van gebruikers acties

1. Klik boven aan de sectie kenmerk toewijzing op **Opslaan** om uw toewijzingen op te slaan.

Zodra de configuratie van de kenmerk toewijzing is voltooid, kunt u [de User Provisioning Service nu inschakelen en starten](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Gebruikers inrichting inschakelen en starten

Zodra de configuratie van de SuccessFactors-inrichting is voltooid, kunt u de inrichtings service inschakelen in de Azure Portal.

> [!TIP]
> Wanneer u de inrichtings service inschakelt, worden er standaard inrichtings bewerkingen gestart voor alle gebruikers binnen het bereik. Als er fouten optreden in de toewijzings-of workday-gegevens problemen, kan de inrichtings taak mislukken en gaat u naar de quarantaine status. Om dit te voor komen best practice, raden we u aan om het bereik filter voor **bron objecten** te configureren en uw kenmerk toewijzingen te testen met enkele test gebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Wanneer u hebt gecontroleerd of de toewijzingen werken en u de gewenste resultaten krijgt, kunt u het filter verwijderen of het bestand geleidelijk uitbreiden om meer gebruikers op te geven.

1. Stel op het tabblad **inrichten** de **inrichtings status** in **op aan**.

2. Klik op **Opslaan**.

3. Met deze bewerking wordt de eerste synchronisatie gestart, wat een variabel aantal uur kan duren, afhankelijk van het aantal gebruikers in de SuccessFactors-Tenant. U kunt de voortgangs balk controleren om de voortgang van de synchronisatie cyclus bij te houden. 

4. Controleer op elk gewenst moment het tabblad **controle logboeken** in de Azure Portal om te zien welke acties de inrichtings service heeft uitgevoerd. In de controle logboeken worden alle afzonderlijke synchronisatie gebeurtenissen weer gegeven die door de inrichtings service worden uitgevoerd, bijvoorbeeld welke gebruikers worden gelezen uit de werk dagen en vervolgens worden toegevoegd of bijgewerkt aan Active Directory. 

5. Zodra de initiële synchronisatie is voltooid, wordt een overzichts rapport van de controle op het tabblad **inrichten** geschreven, zoals hieronder wordt weer gegeven.

   > [!div class="mx-imgBorder"]
   > voortgangs balk bij het inrichten van ![](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over ondersteunde SuccessFactors-kenmerken voor inkomende inrichting](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Meer informatie over het configureren van write-back van e-mail naar SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen SuccessFactors en Azure Active Directory](successfactors-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het exporteren en importeren van uw inrichtings configuraties](../app-provisioning/export-import-provisioning-configuration.md)


