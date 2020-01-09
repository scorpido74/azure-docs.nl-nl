---
title: 'Zelf studie: SuccessFactors write-back configureren in Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van kenmerk terugschrijven naar SuccessFactors vanuit Azure AD
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
ms.openlocfilehash: 84ab5da993541012fd2199a30d03f5c69e88bf2c
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530031"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Zelf studie: kenmerk terugschrijven van Azure AD naar SAP SuccessFactors configureren (preview)
Het doel van deze zelf studie is het weer geven van de stappen die u moet uitvoeren voor het terugschrijven van kenmerken van Azure AD naar SuccessFactors Employee Central. Het enige kenmerk dat momenteel wordt ondersteund voor write-back is het e-mail kenmerk. 

## <a name="overview"></a>Overzicht

Nadat u de integratie van binnenkomende inrichtingen hebt ingesteld met behulp [van SuccessFactors naar een on-premises AD-](sap-successfactors-inbound-provisioning-tutorial.md) inrichtings-app of [SuccessFactors naar de Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) -inrichtings toepassing, kunt u de SuccessFactors write-app optioneel configureren om het e-mail adres terug te schrijven naar SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Wie is deze oplossing voor het inrichten van de gebruiker geschikt voor?

Deze SuccessFactors write-inrichtings oplossing voor gebruikers is ideaal voor:

* Organisaties die Office 365 gebruiken voor het terugschrijven van gezaghebbende kenmerken die door IT worden beheerd (zoals een e-mail adres), terug naar SuccessFactors

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

## <a name="configuring-successfactors-writeback"></a>SuccessFactors write-back configureren

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

   * **Gebruikers naam beheerder** : Voer de gebruikers naam in van het gebruikers account van de SUCCESSFACTORS-API, waarbij de bedrijfs-id is toegevoegd. Het heeft de volgende indeling: **gebruikers naam\@companyID**

   * **Beheerders wachtwoord –** Voer het wacht woord van het gebruikers account van de SuccessFactors-API in. 

   * **Tenant-URL:** Voer de naam van het SuccessFactors OData API services-eind punt in. Voer alleen de hostnaam van de server in zonder http of https. Deze waarde moet er als volgt uitzien: **API-server-name.successfactors.com**.

   * **E-mail melding-** Voer uw e-mail adres in en schakel het selectie vakje e-mail verzenden als er een fout is opgetreden in.
    > [!NOTE]
    > De Azure AD-inrichtings service verzendt een e-mail melding als de inrichtings taak een [quarantaine](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) status heeft.

   * Klik op de knop **verbinding testen** . Als de verbindings test is geslaagd, klikt u bovenaan op de knop **Opslaan** . Als dit mislukt, controleert u of de SuccessFactors-referenties en-URL geldig zijn.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Zodra de referenties zijn opgeslagen, wordt in de sectie **toewijzingen** de standaard toewijzing voor het **synchroniseren van Azure Active Directory gebruikers met SuccessFactors** weer gegeven.

### <a name="part-2-configure-attribute-mappings"></a>Deel 2: kenmerk toewijzingen configureren

In deze sectie configureert u hoe gebruikers gegevens stromen van SuccessFactors naar Active Directory.

1. Klik op het tabblad inrichting onder **toewijzingen**op **Azure Active Directory gebruikers synchroniseren met SuccessFactors**.

1. In het veld **bereik van bron object** kunt u selecteren welke gebruikers in azure AD moeten worden overwogen voor het terugschrijven van items door een set op kenmerken gebaseerde filters te definiëren. Het standaard bereik is alle gebruikers in azure AD. 
   > [!TIP]
   > Wanneer u de inrichtings-app voor de eerste keer configureert, moet u de kenmerk toewijzingen en expressies testen en controleren om ervoor te zorgen dat het gewenste resultaat wordt weer geven. Micro soft raadt aan om de bereik filters onder het bereik van de **bron object** te gebruiken om uw toewijzingen te testen met een aantal test gebruikers van Azure AD. Wanneer u hebt gecontroleerd of de toewijzingen werken, kunt u het filter verwijderen of het bestand geleidelijk uitbreiden om meer gebruikers op te geven.

1. Het veld **acties doel object** ondersteunt alleen de bewerking **Update** .

1. In de sectie **kenmerk toewijzingen** kunt u alleen de overeenkomende id wijzigen die wordt gebruikt voor het koppelen van een SuccessFactors-gebruikers profiel aan een Azure AD-gebruiker en welk kenmerk in azure AD fungeert als de bron van e-mail. 
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >De SuccessFactors write back ondersteunt alleen het e-mail kenmerk. Maak geen gebruik van **nieuwe toewijzing toevoegen** om nieuwe kenmerken toe te voegen. 

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

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)
* [Meer informatie over het configureren van eenmalige aanmelding tussen SuccessFactors en Azure Active Directory](successfactors-tutorial.md)
* [Meer informatie over het integreren van andere SaaS-toepassingen met Azure Active Directory](tutorial-list.md)
* [Meer informatie over het exporteren en importeren van uw inrichtings configuraties](../manage-apps/export-import-provisioning-configuration.md)

