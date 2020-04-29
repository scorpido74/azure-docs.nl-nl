---
title: E-mail wanneer de status van het geheim Key Vault is gewijzigd
description: Hand leiding voor het gebruik van Logic Apps om te reageren op wijzigingen in Key Vault geheimen
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 53e8586486d9a9ebf870de350d5607f58977c0f5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423274"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Logic Apps gebruiken om e-mail te ontvangen over status wijzigingen in sleutel kluis geheimen

In deze hand leiding wordt beschreven hoe u kunt reageren op Azure Key Vault gebeurtenissen die via [Azure Event grid](../../event-grid/index.yml) worden ontvangen met behulp van [Azure Logic apps](../../logic-apps/index.yml). Aan het einde hebt u een Azure Logic app ingesteld voor het verzenden van een e-mail melding wanneer er een geheim wordt gemaakt in Azure Key Vault.

Zie [Key Vault bewaken met Azure Event grid (preview)](event-grid-overview.md)voor een overzicht van de integratie van Azure Key Vault/Azure Event grid.

## <a name="prerequisites"></a>Vereisten

- Een e-mail account van een e-mail provider die wordt ondersteund door Azure Logic Apps (zoals Office 365 Outlook). Dit e-mailaccount wordt gebruikt voor het verzenden van de gebeurtenismeldingen. Zie [Overzicht van connectors](/connectors) voor een volledige lijst met ondersteunde Logic App-connectors.
- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Een sleutel kluis in uw Azure-abonnement. U kunt snel een nieuwe sleutel kluis maken aan de hand van de stappen in [set en haal een geheim op uit Azure Key Vault met behulp van Azure cli](../secrets/quick-create-cli.md).

## <a name="create-a-logic-app-via-event-grid"></a>Een logische app maken via Event Grid

Maak eerst een logische app met Event grid-handler en Abonneer u op Azure Key Vault gebeurtenissen "SecretNewVersionCreated".

Voer de volgende stappen uit om een Azure Event Grid-abonnement te maken:

1. Ga in het Azure Portal naar uw sleutel kluis, selecteer **gebeurtenissen > aan de slag** en klik op **Logic apps**

    
    ![Key Vault-gebeurtenissen pagina](../media/eventgrid-logicapps-kvsubs.png)

1. In **Logic apps Designer** valideert u de verbinding en klikt u op **door gaan** 
 
    ![Logic app Designer-verbinding](../media/eventgrid-logicappdesigner1.png)

1. Ga als volgt te werk op het scherm **Wanneer een a-bron gebeurtenis zich voordoet** :
    - Geef de naam van het **abonnement** en de **resource** als standaard.
    - Selecteer **micro soft.** de sleutel kluis. kluizen voor het **bron type**.
    - Selecteer **micro soft. SecretNewVersionCreated** voor **gebeurtenis type-1**.

    ![Logic app Designer-gebeurtenis registratie](../media/eventgrid-logicappdesigner2.png)

1. Selecteer **+ nieuwe stap** Hiermee wordt een venster geopend om een actie te kiezen.
1. Zoek naar **e-mail**. Zoek en selecteer de bijbehorende connector op basis van uw e-mailprovider. In deze zelf studie wordt **Office 365 Outlook**gebruikt. De stappen voor andere e-mailproviders zijn vergelijkbaar.
1. Selecteer de actie **een E-mail verzenden (v2)** .

   ![Logic app Designer-e-mail toevoegen](../media/eventgrid-logicappdesigner3.png)

1. Uw e-mail sjabloon bouwen:
    - **Voor:** Voer het e-mail adres in voor de ontvangst van de meldings-e-mail. Gebruik voor deze zelfstudie een e-mailaccount dat toegankelijk is voor testdoeleinden.
    - **Onderwerp** en **Hoofdtekst**: typ hier het onderwerp en de tekst voor uw e-mail. Selecteer JSON-eigenschappen in het selectiehulpmiddel om dynamische inhoud op te nemen op basis van gegevens van gebeurtenissen. U kunt de gegevens van de gebeurtenis ophalen met `@{triggerBody()?['Data']}`.

    Uw e-mail sjabloon kan er als volgt uitzien.

    ![Logic app Designer-e-mail toevoegen](../media/eventgrid-logicappdesigner4.png)

8. Klik op **Opslaan als**.
9. Voer een **naam** in voor de nieuwe logische app en klik op **maken**.
    
    ![Logic app Designer-e-mail toevoegen](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testen en verifiëren

1.  Ga naar de sleutel kluis op de Azure Portal en selecteer **gebeurtenissen > gebeurtenis abonnementen**.  Controleren of een nieuw abonnement is gemaakt
    
    ![Logic app Designer-e-mail toevoegen](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Ga naar de sleutel kluis, selecteer **geheimen**en selecteer **+ genereren/importeren**. Een nieuw geheim voor test doeleinden maken naam van de sleutel en behoud de resterende para meters in de standaard instellingen.

    ![Key Vault-geheim maken](../media/eventgrid-logicapps-kv-create-secret.png)

1. Geef in het scherm **een geheim maken** een naam, een wille keurige waarde op en selecteer **maken**.

Wanneer het geheim wordt gemaakt, wordt er een e-mail bericht ontvangen op de geconfigureerde adressen.

## <a name="next-steps"></a>Volgende stappen

- Overzicht: [Key Vault bewaken met Azure Event grid (preview-versie)](event-grid-overview.md)
- Procedure: de [belangrijkste kluis meldingen door sturen naar Azure Automation](event-grid-tutorial.md).
- [Azure Event Grid-gebeurtenis schema voor Azure Key Vault (preview-versie)](../../event-grid/event-schema-key-vault.md)
- Meer informatie over [Azure Event Grid](../../event-grid/index.yml).
- Meer informatie over de [Logic Apps-functie van Azure App Service](../../logic-apps/index.yml).
