---
title: 'Zelf studie: Configure SAP SuccessFactors write-back in Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van een kenmerk write-back naar SAP SuccessFactors vanuit Azure AD
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
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: f150d6abf2ac6a423a99d3347df9bf0adc9b294b
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809928"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>Zelf studie: kenmerk write-back van Azure AD naar SAP SuccessFactors configureren
Het doel van deze zelf studie is het weer geven van de stappen voor het terugschrijven van kenmerken van Azure AD naar SAP SuccessFactors Employee Central. 

## <a name="overview"></a>Overzicht

U kunt de SAP SuccessFactors write-app configureren voor het schrijven van specifieke kenmerken van Azure Active Directory naar SAP SuccessFactors Employee Central. De SuccessFactors write inrichtings-app ondersteunt het toewijzen van waarden aan de volgende centrale kenmerken van werk nemers:

* E-mail werk
* Gebruikersnaam
* Zakelijk telefoon nummer (inclusief land nummer, netnummer, cijfer en uitbrei ding)
* Zakelijke telefoon nummer primaire vlag
* Mobiele telefoon nummer (inclusief land nummer, netnummer, getal)
* Primaire vlag voor mobiele telefoon 
* Gebruikers-custom01-custom15-kenmerken
* loginMethod-kenmerk

> [!NOTE]
> Deze app heeft geen afhankelijkheid van de SuccessFactors inkomende integratie-apps voor het inrichten van gebruikers rechten. U kunt deze onafhankelijk van [SuccessFactors configureren voor on-premises AD-](sap-successfactors-inbound-provisioning-tutorial.md) inrichtings-app of [SUCCESSFACTORS naar Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) -inrichtings toepassing.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Wie is deze oplossing voor het inrichten van de gebruiker geschikt voor?

Deze SuccessFactors write-inrichtings oplossing voor gebruikers is ideaal voor:

* Organisaties die Office 365 gebruiken voor het terugschrijven van gezaghebbende kenmerken die door IT worden beheerd (zoals e-mail adres, telefoon nummer, gebruikers naam), worden weer gegeven in SuccessFactors werk nemers centraal.

## <a name="configuring-successfactors-for-the-integration"></a>SuccessFactors configureren voor de integratie

Alle SuccessFactors-inrichtings connectors vereisen referenties van een SuccessFactors-account met de juiste machtigingen om de Centraal OData-Api's van de werk nemers aan te roepen. In deze sectie worden de stappen beschreven voor het maken van het service account in SuccessFactors en het verlenen van de juiste machtigingen. 

* [API-gebruikers account maken/identificeren in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Een functie voor API-machtigingen maken](#create-an-api-permissions-role)
* [Een machtigings groep maken voor de API-gebruiker](#create-a-permission-group-for-the-api-user)
* [Machtigings functie verlenen aan de machtigings groep](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API-gebruikers account maken/identificeren in SuccessFactors
Werk samen met uw SuccessFactors-beheer team of implementatie partner om een gebruikers account in SuccessFactors te maken of te identificeren dat wordt gebruikt om de OData-Api's aan te roepen. De referenties van de gebruikers naam en het wacht woord van dit account zijn vereist bij het configureren van de inrichtings-apps in azure AD. 

### <a name="create-an-api-permissions-role"></a>Een functie voor API-machtigingen maken

1. Meld u aan bij SAP SuccessFactors met een gebruikers account dat toegang heeft tot het beheer centrum.
1. Zoek naar *machtigings rollen beheren*en selecteer vervolgens **machtigings rollen beheren** in de zoek resultaten.

   ![Machtigings rollen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. Klik in de lijst machtigings rollen op **nieuwe maken**.

   > [!div class="mx-imgBorder"]
   > ![Nieuwe machtigings functie maken](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. Voeg een **rolnaam** en een **Beschrijving** voor de nieuwe machtigings functie toe. De naam en beschrijving moeten aangeven dat de rol is voor API-gebruiks machtigingen.

   > [!div class="mx-imgBorder"]
   > ![Details van machtigings rol](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. Klik onder machtigings instellingen op **machtiging...**, Schuif omlaag in de lijst met machtigingen en klik op **integratie hulpprogramma's beheren**. Schakel het selectie vakje in om de beheerder toe te **staan om toegang te krijgen tot ODATA API via basis verificatie**.

   > [!div class="mx-imgBorder"]
   > ![Integratie hulpprogramma's beheren](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. Schuif omlaag in hetzelfde vak en selecteer de **centrale API voor werk nemers**. Voeg machtigingen toe, zoals hieronder wordt weer gegeven, voor meer informatie over het gebruik van ODATA API en Edit met de ODATA-API. Selecteer de optie bewerken als u hetzelfde account wilt gebruiken voor het SuccessFactors-scenario write-back naar. 

   > [!div class="mx-imgBorder"]
   > ![Lees machtigingen voor schrijven](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Klik op **gereed**. Klik op **Wijzigingen opslaan**.

### <a name="create-a-permission-group-for-the-api-user"></a>Een machtigings groep maken voor de API-gebruiker

1. Zoek in het SuccessFactors-beheer centrum naar *machtigings groepen beheren*en selecteer vervolgens **machtigings groepen beheren** in de zoek resultaten.

   > [!div class="mx-imgBorder"]
   > ![Machtigings groepen beheren](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. Klik in het venster machtigings groepen beheren op **nieuwe maken**.

   > [!div class="mx-imgBorder"]
   > ![Nieuwe groep toevoegen](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. Voeg een groeps naam voor de nieuwe groep toe. De groeps naam moet aangeven dat de groep voor API-gebruikers is.

   > [!div class="mx-imgBorder"]
   > ![Naam van de machtigings groep](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. Leden toevoegen aan de groep. U kunt bijvoorbeeld **gebruikers naam** selecteren in de vervolg keuzelijst personen groep en vervolgens de gebruikers naam invoeren van het API-account dat wordt gebruikt voor de integratie. 

   > [!div class="mx-imgBorder"]
   > ![Groepsleden toevoegen](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. Klik op **gereed** om het maken van de machtigings groep te volt ooien.

### <a name="grant-permission-role-to-the-permission-group"></a>Machtigings functie verlenen aan de machtigings groep

1. Zoek in SuccessFactors-beheer centrum naar *machtigings rollen beheren*en selecteer vervolgens **machtigings rollen beheren** in de zoek resultaten.
1. Selecteer in de **lijst machtigings rollen**de rol die u hebt gemaakt voor de machtigingen voor API-gebruik.
1. Onder **deze rol toekennen aan..**. klikt u op de knop **toevoegen..** ..
1. Selecteer de **machtigings groep..** . in de vervolg keuzelijst en klik vervolgens op **selecteren...** om het venster groepen te openen, te zoeken en de eerder gemaakte groep te selecteren. 

   > [!div class="mx-imgBorder"]
   > ![Machtigings groep toevoegen](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Controleer de machtigings rol verlenen aan de machtigings groep. 
   > [!div class="mx-imgBorder"]
   > ![Rol en groeps Details van machtiging](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Klik op **Wijzigingen opslaan**.

## <a name="preparing-for-successfactors-writeback"></a>Write-back van SuccessFactors voorbereiden

De SuccessFactors write inrichtings-app gebruikt bepaalde *code* waarden voor het instellen van e-mail en telefoon nummers in werk nemers centraal. Deze *code* waarden worden ingesteld als constante waarden in de kenmerk toewijzings tabel en zijn verschillend voor elk SuccessFactors-exemplaar. In deze sectie wordt gebruikgemaakt van [postman](https://www.postman.com/downloads/) om de code waarden op te halen. U kunt [krul](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) of een ander soortgelijk hulp programma gebruiken om HTTP-aanvragen te verzenden. 

### <a name="download-and-configure-postman-with-your-successfactors-tenant"></a>Postman downloaden en configureren met uw SuccessFactors-Tenant

1. [Berichtman](https://www.postman.com/downloads/) downloaden
1. Maak een ' nieuwe verzameling ' in de Postman-app. Noem het ' SuccessFactors '. 

   > [!div class="mx-imgBorder"]
   > ![Nieuwe postman-verzameling](./media/sap-successfactors-inbound-provisioning/new-postman-collection.png)

1. Op het tabblad autorisatie voert u de referenties in van de API-gebruiker die in de vorige sectie is geconfigureerd. Configureer type als basis verificatie. 

   > [!div class="mx-imgBorder"]
   > ![Postman-autorisatie](./media/sap-successfactors-inbound-provisioning/postman-authorization.png)

1. Sla de configuratie op. 

### <a name="retrieve-constant-value-for-emailtype"></a>Constante waarde ophalen voor emailType

1. Klik in postman op het weglatings teken (...) dat is gekoppeld aan de verzameling SuccessFactors en voeg een ' nieuwe aanvraag ' met de naam ' e-mail typen ophalen ' toe, zoals hieronder wordt weer gegeven. 

   > [!div class="mx-imgBorder"]
   > ![Postman-e-mail aanvraag](./media/sap-successfactors-inbound-provisioning/postman-email-request.png)

1. Open het aanvraag paneel ' e-mail type ophalen '. 
1. Voeg in de GET-URL de volgende URL toe en vervang deze door `successFactorsAPITenantName` de API-Tenant voor uw SuccessFactors-exemplaar. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecEmailType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![E-mail type van Postman ophalen](./media/sap-successfactors-inbound-provisioning/postman-get-email-type.png)

1. Het tabblad autorisatie neemt de auth over die is geconfigureerd voor de verzameling. 
1. Klik op verzenden om de API-aanroep aan te roepen. 
1. Bekijk in de hoofd tekst van het antwoord de JSON-resultatenset en zoek naar de ID die overeenkomt met de `externalCode = B` . 

   > [!div class="mx-imgBorder"]
   > ![E-mail type antwoord postman](./media/sap-successfactors-inbound-provisioning/postman-email-type-response.png)

1. Noteer deze waarde als de constante die moet worden gebruikt met *emailType* in de kenmerk toewijzings tabel.

### <a name="retrieve-constant-value-for-phonetype"></a>Constante waarde ophalen voor phoneType

1. Klik in postman op het weglatings teken (...) dat is gekoppeld aan de SuccessFactors-verzameling en voeg een ' nieuwe aanvraag ' met de naam ' Get Phone types ' toe, zoals hieronder wordt weer gegeven. 

   > [!div class="mx-imgBorder"]
   > ![Postman-telefoon aanvraag](./media/sap-successfactors-inbound-provisioning/postman-phone-request.png)

1. Open het aanvraag paneel ' telefoon type ophalen '. 
1. Voeg in de GET-URL de volgende URL toe en vervang deze door `successFactorsAPITenantName` de API-Tenant voor uw SuccessFactors-exemplaar. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecPhoneType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Telefoon type van Postman-Get](./media/sap-successfactors-inbound-provisioning/postman-get-phone-type.png)

1. Het tabblad autorisatie neemt de auth over die is geconfigureerd voor de verzameling. 
1. Klik op verzenden om de API-aanroep aan te roepen. 
1. Bekijk in de hoofd tekst van het antwoord de JSON-resultatenset en zoek naar de *id* die overeenkomt met de `externalCode = B` en `externalCode = C` . 

   > [!div class="mx-imgBorder"]
   > ![Postman-telefoon](./media/sap-successfactors-inbound-provisioning/postman-phone-type-response.png)

1. Noteer deze waarden als de constanten die moeten worden gebruikt met *businessPhoneType* en *cellPhoneType* in de kenmerk toewijzings tabel.

## <a name="configuring-successfactors-writeback-app"></a>SuccessFactors write-app configureren

Deze sectie bevat stappen voor 

* [De inrichtings connector-app toevoegen en connectiviteit configureren voor SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Kenmerk toewijzingen configureren](#part-2-configure-attribute-mappings)
* [Gebruikers inrichting inschakelen en starten](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Deel 1: de app voor de inrichtings connector toevoegen en connectiviteit met SuccessFactors configureren

**SuccessFactors write-back configureren:**

1. Ga naar <https://portal.azure.com>

2. Selecteer in de linker navigatie balk de optie **Azure Active Directory**

3. Selecteer **bedrijfs toepassingen**en vervolgens **alle toepassingen**.

4. Selecteer **een toepassing toevoegen**en selecteer de categorie **alle** .

5. Zoek naar **SuccessFactors write**en voeg die app toe vanuit de galerie.

6. Nadat de app is toegevoegd en het scherm met details van de app wordt weer gegeven, selecteert u **inrichting** maken

7. De **inrichtings** **modus** wijzigen in **automatisch**

8. Voer de sectie **beheerders referenties** als volgt uit:

   * **Gebruikers naam beheerder** : Voer de gebruikers naam in van het gebruikers account van de SUCCESSFACTORS-API, waarbij de bedrijfs-id is toegevoegd. Het heeft de volgende indeling: **gebruikers naam \@ companyID**

   * **Beheerders wachtwoord –** Voer het wacht woord van het gebruikers account van de SuccessFactors-API in. 

   * **Tenant-URL:** Voer de naam van het SuccessFactors OData API services-eind punt in. Voer alleen de hostnaam van de server in zonder http of https. Deze waarde moet er als volgt uitzien: `api4.successfactors.com` .

   * **E-mail melding-** Voer uw e-mail adres in en schakel het selectie vakje e-mail verzenden als er een fout is opgetreden in.
    > [!NOTE]
    > De Azure AD-inrichtings service verzendt een e-mail melding als de inrichtings taak een [quarantaine](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) status heeft.

   * Klik op de knop **verbinding testen** . Als de verbindings test is geslaagd, klikt u bovenaan op de knop **Opslaan** . Als dit mislukt, controleert u of de SuccessFactors-referenties en-URL geldig zijn.
    >[!div class="mx-imgBorder"]
    >![Azure-portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Zodra de referenties zijn opgeslagen, wordt de standaard toewijzing weer gegeven in de sectie **toewijzingen** . Vernieuw de pagina als de kenmerk toewijzingen niet zichtbaar zijn.  

### <a name="part-2-configure-attribute-mappings"></a>Deel 2: kenmerk toewijzingen configureren

In deze sectie configureert u hoe gebruikers gegevens stromen van SuccessFactors naar Active Directory.

1. Klik op het tabblad inrichting onder **toewijzingen**op **Azure Active Directory gebruikers inrichten**.

1. In het veld **bereik van bron object** kunt u selecteren welke groepen gebruikers in azure AD moeten worden beschouwd als write-back, door een set op kenmerken gebaseerde filters te definiëren. Het standaard bereik is alle gebruikers in azure AD. 
   > [!TIP]
   > Wanneer u de inrichtings-app voor de eerste keer configureert, moet u de kenmerk toewijzingen en expressies testen en controleren om ervoor te zorgen dat het gewenste resultaat wordt weer geven. Micro soft raadt aan om de bereik filters onder het bereik van de **bron object** te gebruiken om uw toewijzingen te testen met een aantal test gebruikers van Azure AD. Wanneer u hebt gecontroleerd of de toewijzingen werken, kunt u het filter verwijderen of het bestand geleidelijk uitbreiden om meer gebruikers op te geven.

1. Het veld **acties doel object** ondersteunt alleen de bewerking **Update** .

1. In de toewijzings tabel in de sectie **kenmerk toewijzingen** kunt u de volgende Azure Active Directory kenmerken toewijzen aan SuccessFactors. De volgende tabel bevat richt lijnen voor het toewijzen van de kenmerken voor write-back. 

   | \# | Azure AD-kenmerk | SuccessFactors-kenmerk | Opmerkingen |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | Dit kenmerk is standaard de overeenkomende id. In plaats van werk nemers kunt u elk ander Azure AD-kenmerk gebruiken dat de waarde kan opslaan die gelijk is aan personIdExternal in SuccessFactors.    |
   | 2 | mail | e-mail | Bron van e-mail kenmerk toewijzen. Voor test doeleinden kunt u userPrincipalName toewijzen aan e-mail. |
   | 3 | 8448 | emailType | Deze constante waarde is de SuccessFactors ID-waarde die aan zakelijke e-mail is gekoppeld. Werk deze waarde bij zodat deze overeenkomt met uw SuccessFactors-omgeving. Zie de sectie [constante waarde ophalen voor emailType](#retrieve-constant-value-for-emailtype) voor de stappen om deze waarde in te stellen. |
   | 4 | waar | emailIsPrimary | Gebruik dit kenmerk om zakelijke e-mail in te stellen als primair in SuccessFactors. Als zakelijk e-mail adres niet primair is, stelt u deze vlag in op ONWAAR. |
   | 5 | userPrincipalName | [custom01 – custom15] | Met een **nieuwe toewijzing toevoegen**kunt u optioneel userPrincipalName of een Azure AD-kenmerk schrijven naar een aangepast kenmerk dat beschikbaar is in het SuccessFactors-gebruikers object.  |
   | 6 | on-premises-samAccountName | gebruikersnaam | Met een **nieuwe toewijzing toevoegen**kunt u optioneel een on-premises sAMAccountName toewijzen aan het kenmerk SuccessFactors username. |
   | 7 | Eenmalige aanmelding | loginMethod | Als SuccessFactors-Tenant is ingesteld voor [gedeeltelijke SSO](https://apps.support.sap.com/sap/support/knowledge/en/2320766)en vervolgens nieuwe toewijzing toevoegen gebruikt, kunt u eventueel loginMethod instellen op een constante waarde van ' SSO ' of ' pwd '. |
   | 8 | telephoneNumber | businessPhoneNumber | Gebruik deze toewijzing om *telephoneNumber* uit te stromen van Azure AD naar SuccessFactors Business/Work-telefoon nummer. |
   | 9 | 10605 | businessPhoneType | Deze constante waarde is de SuccessFactors ID-waarde gekoppeld aan de zakelijke telefoon. Werk deze waarde bij zodat deze overeenkomt met uw SuccessFactors-omgeving. Zie de sectie [constante waarde ophalen voor phoneType](#retrieve-constant-value-for-phonetype) voor de stappen om deze waarde in te stellen. |
   | 10 | waar | businessPhoneIsPrimary | Gebruik dit kenmerk om de primaire vlag voor het zakelijke telefoon nummer in te stellen. Geldige waarden zijn True of false. |
   | 11 | mobiel | cellPhoneNumber | Gebruik deze toewijzing om *telephoneNumber* uit te stromen van Azure AD naar SuccessFactors Business/Work-telefoon nummer. |
   | 12 | 10606 | cellPhoneType | Deze constante waarde is de SuccessFactors ID-waarde die is gekoppeld aan de mobiele telefoon. Werk deze waarde bij zodat deze overeenkomt met uw SuccessFactors-omgeving. Zie de sectie [constante waarde ophalen voor phoneType](#retrieve-constant-value-for-phonetype) voor de stappen om deze waarde in te stellen. |
   | 13 | onjuist | cellPhoneIsPrimary | Gebruik dit kenmerk om de primaire vlag voor het mobiele telefoon nummer in te stellen. Geldige waarden zijn True of false. |
 
1. Valideer en controleer uw kenmerk toewijzingen. 
 
    >[!div class="mx-imgBorder"]
    >![Toewijzing van write-kenmerk](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. Klik op **Opslaan** om de toewijzingen op te slaan. Vervolgens worden de API-expressies voor het JSON-pad bijgewerkt om de phoneType-codes in uw SuccessFactors-exemplaar te gebruiken. 
1. Selecteer **Geavanceerde opties weergeven**. 

    >[!div class="mx-imgBorder"]
    >![Geavanceerde opties weergeven](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Klik op **kenmerk lijst bewerken voor SuccessFactors**. 

   > [!NOTE] 
   > Als de optie **kenmerk lijst bewerken voor SuccessFactors** niet wordt weer gegeven in de Azure Portal, gebruikt u de URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* voor toegang tot de pagina. 

1. In de **expressie kolom API** in deze weer gave worden de JSON Path-expressies weer gegeven die worden gebruikt door de connector. 
1. Werk de JSON Path-expressies voor zakelijke telefoon en mobiele telefoon bij om de ID-waarde (*businessPhoneType* en *cellPhoneType*) te gebruiken die overeenkomt met uw omgeving. 

    >[!div class="mx-imgBorder"]
    >![Wijziging in het JSON-pad voor het telefoon nummer](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. Klik op **Opslaan** om de toewijzingen op te slaan.

## <a name="enable-and-launch-user-provisioning"></a>Gebruikers inrichting inschakelen en starten

Zodra de configuratie van de SuccessFactors-inrichting is voltooid, kunt u de inrichtings service inschakelen in de Azure Portal.

> [!TIP]
> Wanneer u de inrichtings service inschakelt, worden er standaard inrichtings bewerkingen gestart voor alle gebruikers binnen het bereik. Als er fouten zijn opgetreden in de toewijzings-of gegevens problemen, kan de inrichtings taak mislukken en gaat u naar de status van de quarantaine. Om dit te voor komen best practice, raden we u aan om het bereik filter voor **bron objecten** te configureren en uw kenmerk toewijzingen te testen met enkele test gebruikers voordat u de volledige synchronisatie voor alle gebruikers start. Wanneer u hebt gecontroleerd of de toewijzingen werken en u de gewenste resultaten krijgt, kunt u het filter verwijderen of het bestand geleidelijk uitbreiden om meer gebruikers op te geven.

1. Stel op het tabblad **inrichten** de **inrichtings status** in **op aan**.

2. Klik op **Opslaan**.

3. Met deze bewerking wordt de eerste synchronisatie gestart, wat een variabel aantal uur kan duren, afhankelijk van het aantal gebruikers in de SuccessFactors-Tenant. U kunt de voortgangs balk controleren om de voortgang van de synchronisatie cyclus bij te houden. 

4. Controleer op elk gewenst moment het tabblad **controle logboeken** in de Azure Portal om te zien welke acties de inrichtings service heeft uitgevoerd. In de controle logboeken worden alle afzonderlijke synchronisatie gebeurtenissen vermeld die worden uitgevoerd door de inrichtings service, zoals welke gebruikers worden gelezen uit werk nemers centraal en vervolgens worden toegevoegd of bijgewerkt aan Active Directory. 

5. Zodra de initiële synchronisatie is voltooid, wordt een overzichts rapport van de controle op het tabblad **inrichten** geschreven, zoals hieronder wordt weer gegeven.

   > [!div class="mx-imgBorder"]
   > ![Voortgangs balk inrichten](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Ondersteunde scenario's, bekende problemen en beperkingen

Raadpleeg de [sectie write-scenario's](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) in de naslag gids voor SAP SuccessFactors-integratie. 

## <a name="next-steps"></a>Volgende stappen

* [Uitgebreide informatie over Azure AD en SAP SuccessFactors-integratie](../app-provisioning/sap-successfactors-integration-reference.md)
* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen SuccessFactors en Azure Active Directory](successfactors-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het exporteren en importeren van uw inrichtings configuraties](../app-provisioning/export-import-provisioning-configuration.md)

