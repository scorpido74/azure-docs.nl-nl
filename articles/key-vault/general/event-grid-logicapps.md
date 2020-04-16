---
title: E-mail wanneer de status Key Vault van de geheime wijzigingen wordt gewijzigd
description: Handleiding om Logic Apps te gebruiken om te reageren op wijzigingen in belangrijke vaultgeheimen
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423274"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Logische apps gebruiken om e-mail te ontvangen over statuswijzigingen van belangrijke kluisgeheimen

In deze handleiding leert u hoe u reageren op Azure Key Vault-gebeurtenissen die via [Azure Event Grid](../../event-grid/index.yml) worden ontvangen met behulp van Azure Logic [Apps](../../logic-apps/index.yml). Tegen het einde hebt u een Azure-logica-app ingesteld om een meldingsmail te verzenden telkens wanneer een geheim wordt gemaakt in Azure Key Vault.

Zie Key Vault met Azure Event Grid [(preview)](event-grid-overview.md)voor een overzicht van azure key vault / Azure Event Grid.

## <a name="prerequisites"></a>Vereisten

- Een e-mailaccount van elke e-mailprovider die wordt ondersteund door Azure Logic Apps (zoals Office 365 Outlook). Dit e-mailaccount wordt gebruikt voor het verzenden van de gebeurtenismeldingen. Zie [Overzicht van connectors](/connectors) voor een volledige lijst met ondersteunde Logic App-connectors.
- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Een sleutelkluis in uw Azure-abonnement. U snel een nieuwe sleutelkluis maken door de stappen in Set te volgen [en een geheim op te halen uit Azure Key Vault met Azure CLI.](../secrets/quick-create-cli.md)

## <a name="create-a-logic-app-via-event-grid"></a>Een logische app maken via gebeurtenisraster

Maak eerst Logic App met gebeurtenisrasterhandler en abonneer je op azure key vault "SecretNewVersionCreated"-gebeurtenissen.

Voer de volgende stappen uit om een Azure Event Grid-abonnement te maken:

1. Ga in de Azure-portal naar uw sleutelkluis, selecteer **Gebeurtenissen > aan de slag** en klik op Logische **apps**

    
    ![Key Vault - evenementenpagina](../media/eventgrid-logicapps-kvsubs.png)

1. Op **Logic Apps Designer** de verbinding valideren en op Doorgaan **klikken** 
 
    ![Logic App Designer - verbinding](../media/eventgrid-logicappdesigner1.png)

1. Ga als volgt te werk in het scherm **Wanneer een resourcegebeurtenis plaatsvindt:**
    - Laat **abonnement** en **bronnaam** als standaard.
    - Selecteer **Microsoft.KeyVault.vaults** voor het **resourcetype**.
    - Selecteer **Microsoft.keyvault.SecretNewVersionGemaakt** voor **gebeurtenistypeitem - 1**.

    ![Logic App Designer - gebeurtenishandler](../media/eventgrid-logicappdesigner2.png)

1. Selecteer **+ Nieuwe stap** Hiermee wordt een venster geopend om een actie te kiezen.
1. Zoek naar **e-mail**. Zoek en selecteer de bijbehorende connector op basis van uw e-mailprovider. In deze zelfstudie wordt **Office 365 Outlook gebruikt.** De stappen voor andere e-mailproviders zijn vergelijkbaar.
1. Selecteer de actie **Een e-mail verzenden (V2).**

   ![Logic App Designer - e-mail toevoegen](../media/eventgrid-logicappdesigner3.png)

1. Maak uw e-mailsjabloon:
    - **Naar:** Voer het e-mailadres in om de e-mails met meldingen te ontvangen. Gebruik voor deze zelfstudie een e-mailaccount dat toegankelijk is voor testdoeleinden.
    - **Onderwerp** en **Hoofdtekst**: typ hier het onderwerp en de tekst voor uw e-mail. Selecteer JSON-eigenschappen in het selectiehulpmiddel om dynamische inhoud op te nemen op basis van gegevens van gebeurtenissen. U de gegevens van `@{triggerBody()?['Data']}`de gebeurtenis ophalen met behulp van.

    Uw e-mailsjabloon kan er in dit voorbeeld uitzien.

    ![Logic App Designer - e-mail toevoegen](../media/eventgrid-logicappdesigner4.png)

8. Klik **op Opslaan als**.
9. Voer een **naam** in voor een nieuwe logische app en klik op **Maken**.
    
    ![Logic App Designer - e-mail toevoegen](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testen en verifiëren

1.  Ga naar uw sleutelkluis op de Azure-portal en selecteer **Gebeurtenissen > gebeurtenisabonnementen**.  Controleren of er een nieuw abonnement is gemaakt
    
    ![Logic App Designer - e-mail toevoegen](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Ga naar je sleutelkluis, selecteer **Geheimen**en selecteer **+ Genereren/importeren**. Maak een nieuw geheim voor testdoeleinden en geef de sleutel een naam en bewaar de resterende parameters in hun standaardinstellingen.

    ![Key Vault - Secret maken](../media/eventgrid-logicapps-kv-create-secret.png)

1. Geef **op het scherm Een geheim** maken elke naam, elke waarde en selecteer **Maken**.

Wanneer het geheim wordt gemaakt, wordt een e-mail ontvangen op de geconfigureerde adressen.

## <a name="next-steps"></a>Volgende stappen

- Overzicht: [Key Vault bewaken met Azure Event Grid (voorbeeld)](event-grid-overview.md)
- How to: [Route key vault notifications to Azure Automation](event-grid-tutorial.md).
- [Azure Event Grid-gebeurtenisschema voor Azure Key Vault (voorbeeld)](../../event-grid/event-schema-key-vault.md)
- Meer informatie over [Azure Event Grid](../../event-grid/index.yml).
- Meer informatie over de [Logic Apps-functie van Azure App Service](../../logic-apps/index.yml).
