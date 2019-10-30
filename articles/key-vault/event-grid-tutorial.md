---
title: Belang rijke kluis meldingen ontvangen en hierop reageren met Azure Event Grid
description: Meer informatie over het integreren van Key Vault met Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 56490ede89a5859ef73d9110b46ea55fd9b96d54
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033553"
---
# <a name="how-to-receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Procedure: belang rijke kluis meldingen ontvangen en hierop reageren met Azure Event Grid (preview-versie)

Key Vault integratie met Azure Event Grid, momenteel in preview, kunnen gebruikers een melding ontvangen wanneer de status van een geheim dat is opgeslagen in de sleutel kluis is gewijzigd. Zie [Key Vault bewaken met Azure Event grid](event-grid-overview.md)voor een overzicht van de functie.

In deze hand leiding wordt uitgelegd hoe u Key Vault meldingen ontvangt via Azure Event Grid en hoe u kunt reageren op status wijzigingen met Azure Automation.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Een sleutel kluis in uw Azure-abonnement. U kunt snel een nieuwe sleutel kluis maken aan de hand van de stappen in [set en haal een geheim op uit Azure Key Vault met behulp van Azure cli](quick-create-cli.md)

## <a name="concepts"></a>Concepten

Azure Event Grid is een gebeurtenisservice voor de cloud. In deze hand leiding abonneert u zich op gebeurtenissen voor sleutel kluis en stuurt u gebeurtenissen naar Azure Automation. Wanneer een van de geheimen in de sleutel kluis bijna verloopt, wordt Event Grid op de hoogte gesteld van de status wijziging en wordt een HTTP-bericht naar het eind punt genoteerd. Een webhook activeert vervolgens een Azure Automation uitvoering van Power shell-script.

![image](media/image1.png)

## <a name="create-an-azure-automation-account"></a>Een Azure Automation-account maken

Maak een Azure Automation-account via de [Azure Portal](https://portal.azure.com).

1.  Ga naar portal.azure.com en meld u aan bij uw abonnement.

1.  Typ ' Automation-accounts ' in het zoekvak.

1.  Selecteer in de sectie ' Services ' van de vervolg keuzelijst van de zoek balk de optie Automation-accounts.

1.  Klik op Toevoegen.

    ![](media/image2.png)

1.  Vul de vereiste gegevens in de Blade Automation-account toevoegen in en selecteer maken.

## <a name="create-a-runbook"></a>Een Runbook maken

Nadat uw Azure Automation-account klaar is, maakt u een runbook.

![](media/image3.png)

1.  Selecteer het Automation-account dat u zojuist hebt gemaakt.

1.  Selecteer Runbooks in het gedeelte proces automatisering.

1.  Klik op het maken van een runbook.

1.  Geef uw runbook een naam en selecteer Power shell als het type runbook.

1.  Klik op het runbook dat u hebt gemaakt en selecteer de knop bewerken.

1.  Voer de volgende code in (voor test doeleinden) en klik op de knop publiceren. Hiermee wordt het resultaat van de ontvangen POST-aanvraag uitgevoerd.

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

![](media/image4.png)

## <a name="create-a-webhook"></a>Een webhook maken

Maak nu een webhook om uw zojuist gemaakte runbook te activeren.

1.  Selecteer webhooks in het gedeelte resources van het runbook dat u zojuist hebt gepubliceerd.

1.  Klik op webhook toevoegen.

    ![](media/image5.png)

1.  Selecteer nieuwe webhook maken.

1. Geef de webhook een naam, stel een verval datum in en **Kopieer de URL**.

    > [!IMPORTANT] 
    > U kunt de URL niet weer geven nadat u deze hebt gemaakt. Zorg ervoor dat u een kopie opslaat van een beveiligde locatie waar u deze kunt gebruiken voor de rest van deze hand leiding.

1. Klik op para meters en voer instellingen uit en selecteer OK. Voer geen para meters in. Hiermee wordt de knop maken ingeschakeld.

1. Selecteer OK en selecteer maken.

    ![](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Een Event Grid-abonnement maken

Maak een Event Grid-abonnement via de [Azure Portal](https://portal.azure.com).

1.  Open de Azure Portal met behulp van de volgende koppeling: https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true

1.  Ga naar de sleutel kluis en selecteer het tabblad gebeurtenissen. Als u het tabblad gebeurtenissen niet kunt zien, moet u ervoor zorgen dat u de [Preview-versie van de portal](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true)gebruikt.

    ![](media/image7.png)

1.  Klik op de knop abonnement + gebeurtenis.

1.  Maak een beschrijvende naam voor het abonnement.

1.  Kies Event Grid schema.

1.  ' Resource onderwerp ' moet de sleutel kluis zijn die u wilt bewaken voor status wijzigingen.

1.  Voor filteren op gebeurtenis typen, alle selectie vakjes ingeschakeld ("9 geselecteerd").

1.  Selecteer webhook voor "eindpunt type".

1.  Selecteer een eind punt selecteren. Plak in het deel venster nieuwe context de webhook-URL van de [webhook maken](#create-a-webhook) Step into het veld eind punt van de abonnee.

1.  Selecteer selectie bevestigen in het deel venster context.

1.  Selecteer maken.

    ![](media/image8.png)

## <a name="test-and-verify"></a>Testen en verifiëren

Controleer of de eigenschap van uw Event Grid-abonnement is geconfigureerd.  Bij deze test wordt ervan uitgegaan dat u de melding ' geheime nieuwe versie is gemaakt ' in het [abonnement Create a Event grid](#create-an-event-grid-subscription)hebt geabonneerd en dat u over de benodigde bevoegdheden beschikt om een nieuwe versie van een geheim te maken in een sleutel kluis.

![](media/image9.png)

![](media/image10.png)

1.  Ga naar de sleutel kluis op de Azure Portal

1.  Maak een nieuw geheim. Stel voor test doeleinden de verval datum in op de volgende dag.

1.  Navigeer naar het tabblad gebeurtenissen in uw sleutel kluis.

1.  Selecteer het event grid-abonnement dat u hebt gemaakt.

1.  Onder metrische gegevens ziet u of een gebeurtenis is vastgelegd. Er worden twee gebeurtenissen verwacht: SecretNewVersion en SecretNearExpiry. Dit valideert dat Event grid de status wijziging van het geheim in uw sleutel kluis heeft vastgelegd.

    ![](media/image11.png)

1.  Ga naar uw Azure Automation-account.

1.  Selecteer het tabblad Runbooks en selecteer het runbook dat u hebt gemaakt.

1.  Selecteer het tabblad webhooks en controleer of de tijds tempel van de laatste keer geactiveerd is binnen 60 seconden na het maken van het nieuwe geheim.  Hiermee wordt bevestigd dat Event Grid een bericht naar de webhook hebt gemaakt met de gebeurtenis Details van de status wijziging in uw sleutel kluis en de webhook werd geactiveerd.

    ![](media/image12.png)

1. Ga terug naar uw Runbook en selecteer het tabblad Overzicht.

1. Bekijk de lijst met recente taken. U ziet dat er een taak is gemaakt en dat de status voltooid is.  Hiermee wordt bevestigd dat de webhook het runbook heeft geactiveerd om het script uit te voeren.

    ![](media/image13.png)

1. Selecteer de recente taak en Bekijk de POST-aanvraag die is verzonden van Event grid naar de webhook. Controleer de JSON en zorg ervoor dat de para meters voor de sleutel kluis en het gebeurtenis type juist zijn. Als de para meter "gebeurtenis type" in het JSON-object overeenkomt met de gebeurtenis die is opgetreden in de sleutel kluis (in dit voor beeld is micro soft. SecretNearExpiry) de test is voltooid.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="unable-to-create-event-subscription"></a>Kan geen gebeurtenis abonnement maken

Registreer Event Grid en Key Vault provider opnieuw in de resource providers van uw Azure-abonnement. Zie [Azure-resource providers en-typen](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd! Als u alle bovenstaande stappen hebt gevolgd, kunt u nu programmatisch reageren op status wijzigingen van geheimen die zijn opgeslagen in uw sleutel kluis.

Als u een systeem op basis van polling gebruikt om te zoeken naar status wijzigingen van geheimen in uw sleutel kluizen, migreert u naar met behulp van deze meldings functie. U kunt ook het test script in uw runbook vervangen door code om uw geheimen op een programmatische manier te vernieuwen wanneer ze op het punt staan te verlopen.

Meer informatie:

- [Overzicht van Azure Key Vault](key-vault-overview.md)
- [Overzicht van Azure Event Grid](../event-grid/overview.md)
- [Key Vault bewaken met Azure Event Grid (preview-versie)](event-grid-overview.md)
- [Azure Event Grid-gebeurtenis schema voor Azure Key Vault (preview-versie)](../event-grid/event-schema-key-vault.md)
- [Overzicht van Azure Automation](../automation/index.yml)