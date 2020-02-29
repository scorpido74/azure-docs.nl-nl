---
title: Belang rijke kluis meldingen ontvangen en hierop reageren met Azure Event Grid
description: Meer informatie over het integreren van Key Vault met Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 5eaf4cf702e56df932a61ab277dff6b34d97854d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185026"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Belang rijke kluis meldingen ontvangen en hierop reageren met Azure Event Grid (preview-versie)

Azure Key Vault integratie met Azure Event Grid (momenteel in Preview), wordt de melding van de gebruiker ingeschakeld wanneer de status van een geheim dat is opgeslagen in een sleutel kluis is gewijzigd. Zie [Key Vault bewaken met Event grid](event-grid-overview.md)voor een overzicht van deze functie.

In deze hand leiding wordt beschreven hoe u Key Vault meldingen ontvangt via Event Grid en hoe u kunt reageren op status wijzigingen via Azure Automation.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Een sleutel kluis in uw Azure-abonnement. U kunt snel een nieuwe sleutel kluis maken aan de hand van de stappen in [set en haal een geheim op uit Azure Key Vault met behulp van Azure cli](quick-create-cli.md).

## <a name="concepts"></a>Concepten

Event Grid is een gebeurtenis service voor de Cloud. Als u de stappen in deze hand leiding volgt, kunt u zich abonneren op gebeurtenissen voor Key Vault en de gebeurtenissen naar Automation routeren. Wanneer een van de geheimen in de sleutel kluis bijna verloopt, wordt Event Grid op de hoogte gesteld van de status wijziging en wordt een HTTP-bericht naar het eind punt genoteerd. Een webhook activeert vervolgens een Automation-uitvoering van een Power shell-script.

![Stroom diagram HTTP POST](media/image1.png)

## <a name="create-an-automation-account"></a>Een Automation-account maken

Maak een Automation-account via de [Azure Portal](https://portal.azure.com):

1.  Ga naar portal.azure.com en meld u aan bij uw abonnement.

1.  Voer in het zoekvak **Automation-accounts**in.

1.  Selecteer **Automation-accounts**onder het gedeelte **Services** van de vervolg keuzelijst op de zoek balk.

1.  Selecteer **Toevoegen**.

    ![Deel venster Automation-accounts](media/image2.png)

1.  Voer de vereiste gegevens in het deel venster **Automation-account toevoegen** in en selecteer vervolgens **maken**.

## <a name="create-a-runbook"></a>Een runbook maken

Nadat uw Automation-account klaar is, maakt u een runbook.

![Een runbook-gebruikers interface maken](media/image3.png)

1.  Selecteer het Automation-account dat u zojuist hebt gemaakt.

1.  Selecteer **Runbooks** onder **proces automatisering**.

1.  Selecteer **een Runbook maken**.

1.  Geef uw runbook een naam en selecteer **Power shell** als het type runbook.

1.  Selecteer het runbook dat u hebt gemaakt en selecteer vervolgens de knop **bewerken** .

1.  Voer de volgende code in (voor test doeleinden) en selecteer de knop **publiceren** . Met deze actie wordt het resultaat van de ontvangen POST-aanvraag geretourneerd.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Runbook-gebruikers interface publiceren](media/image4.png)

## <a name="create-a-webhook"></a>Een webhook maken

Maak een webhook om uw zojuist gemaakte runbook te activeren.

1.  Selecteer **webhooks** in het gedeelte **resources** van het runbook dat u zojuist hebt gepubliceerd.

1.  Selecteer **webhook toevoegen**.

    ![Knop webhook toevoegen](media/image5.png)

1.  Selecteer **nieuwe webhook maken**.

1. Geef de webhook een naam, stel een verval datum in en kopieer de URL.

    > [!IMPORTANT] 
    > U kunt de URL niet weer geven nadat u deze hebt gemaakt. Zorg ervoor dat u een kopie opslaat op een veilige locatie waar u deze kunt gebruiken voor de rest van deze hand leiding.

1. Selecteer **para meters en voer instellingen uit** en selecteer **OK**. Voer geen para meters in. Hiermee wordt de knop **maken** ingeschakeld.

1. Selecteer **OK** en selecteer vervolgens **maken**.

    ![Nieuwe webhook-gebruikers interface maken](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Een Event Grid-abonnement maken

Maak een Event Grid-abonnement via de [Azure Portal](https://portal.azure.com).

1.  Ga naar de sleutel kluis en selecteer het tabblad **gebeurtenissen** . Als u deze niet kunt zien, moet u ervoor zorgen dat u de [Preview-versie van de portal](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true)gebruikt.

    ![Tabblad gebeurtenissen in Azure Portal](media/image7.png)

1.  Selecteer de knop **gebeurtenis abonnement** .

1.  Maak een beschrijvende naam voor het abonnement.

1.  Kies **Event grid schema**.

1.  De resource van het **onderwerp** moet de sleutel kluis zijn die u wilt bewaken voor status wijzigingen.

1.  **Als u wilt filteren op gebeurtenis typen**, moet u alle geselecteerde opties selecteren (**9 geselecteerd**).

1.  Selecteer **Webhook** voor **Eindpunttype**.

1.  Kies **een eind punt selecteren**. Plak in het deel venster nieuwe context de webhook-URL van de [webhook maken](#create-a-webhook) Step into het **abonnee-eindpunt** veld.

1.  Selecteer **selectie bevestigen** in het context venster.

1.  Selecteer **Maken**.

    ![Gebeurtenis abonnement maken](media/image8.png)

## <a name="test-and-verify"></a>Testen en verifiëren

Controleer of uw Event Grid-abonnement juist is geconfigureerd. Bij deze test wordt ervan uitgegaan dat u bent geabonneerd op de melding ' geheime nieuwe versie is gemaakt ' in het [abonnement een event Grid maken](#create-an-event-grid-subscription)en dat u over de benodigde machtigingen beschikt om een nieuwe versie van een geheim te maken in een sleutel kluis.

![Configuratie van Event Grid-abonnement testen](media/image9.png)

![Deel venster maken van een geheim](media/image10.png)

1.  Ga naar de sleutel kluis op de Azure Portal.

1.  Maak een nieuw geheim. Stel voor test doeleinden de verval datum in op de volgende dag.

1.  Selecteer op het tabblad **gebeurtenissen** in de sleutel kluis het event grid abonnement dat u hebt gemaakt.

1.  Controleer onder **metrische gegevens**of een gebeurtenis is vastgelegd. Er worden twee gebeurtenissen verwacht: SecretNewVersion en SecretNearExpiry. Deze gebeurtenissen valideren dat Event Grid de status wijziging van het geheim in uw sleutel kluis heeft vastgelegd.

    ![Deel venster metrische gegevens: controleren op vastgelegde gebeurtenissen](media/image11.png)

1.  Ga naar uw Automation-account.

1.  Selecteer het tabblad **Runbooks** en selecteer vervolgens het runbook dat u hebt gemaakt.

1.  Selecteer het tabblad **webhooks** en controleer of de tijds tempel van de laatste activering binnen 60 seconden ligt nadat u het nieuwe geheim hebt gemaakt. Dit resultaat bevestigt dat Event Grid een bericht naar de webhook hebt gemaakt met de gebeurtenis Details van de status wijziging in uw sleutel kluis en dat de webhook is geactiveerd.

    ![Tabblad webhooks, laatst geactiveerd tijds tempel](media/image12.png)

1. Ga terug naar uw runbook en selecteer het tabblad **overzicht** .

1. Bekijk de lijst met **recente taken** . U ziet dat er een taak is gemaakt en dat de status voltooid is. Hiermee wordt bevestigd dat de webhook het runbook heeft geactiveerd om het script uit te voeren.

    ![Lijst met recente taken voor webhook](media/image13.png)

1. Selecteer de recente taak en Bekijk de POST-aanvraag die is verzonden van Event Grid naar de webhook. Controleer de JSON en zorg ervoor dat de para meters voor de sleutel kluis en het gebeurtenis type juist zijn. Als de para meter "gebeurtenis type" in het JSON-object overeenkomt met de gebeurtenis die is opgetreden in de sleutel kluis (in dit voor beeld is micro soft. SecretNearExpiry), is de test geslaagd.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="you-cant-create-an-event-subscription"></a>U kunt geen gebeurtenis abonnement maken

Registreer Event Grid en de sleutel kluis provider in de resource providers van uw Azure-abonnement. Zie [Azure-resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd! Als u al deze stappen hebt uitgevoerd, bent u nu klaar om programmatisch te reageren op status wijzigingen van geheimen die zijn opgeslagen in uw sleutel kluis.

Als u een systeem op basis van een polling hebt gebruikt om te zoeken naar status wijzigingen van geheimen in uw sleutel kluizen, kunt u nu beginnen met het gebruik van deze meldings functie. U kunt ook het test script in uw runbook vervangen door code om uw geheimen op een programmatische manier te vernieuwen wanneer ze op het punt staan te verlopen.

Meer informatie:


- Overzicht: [Key Vault bewaken met Azure Event grid (preview-versie)](event-grid-overview.md)
- Procedure: [E-mail ontvangen wanneer een sleutel kluis geheim verandert](event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenis schema voor Azure Key Vault (preview-versie)](../event-grid/event-schema-key-vault.md)
- [Overzicht van Azure Key Vault](key-vault-overview.md)
- [Overzicht van Azure Event Grid](../event-grid/overview.md)
- [Overzicht van Azure Automation](../automation/index.yml)
