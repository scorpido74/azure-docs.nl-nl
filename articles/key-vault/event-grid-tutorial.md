---
title: Belangrijke vaultmeldingen ontvangen en beantwoorden met Azure Event Grid
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78185026"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Meldingen van belangrijke kluizen ontvangen en beantwoorden met Azure Event Grid (voorbeeld)

Azure Key Vault-integratie met Azure Event Grid (momenteel in preview) maakt gebruikersmelding mogelijk wanneer de status van een geheim dat is opgeslagen in een sleutelkluis is gewijzigd. Zie Key Vault met [gebeurtenisraster controleren voor](event-grid-overview.md)een overzicht van deze functie.

In deze handleiding wordt beschreven hoe u Key Vault-meldingen ontvangen via gebeurtenisraster en hoe u reageren op statuswijzigingen via Azure Automation.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Een sleutelkluis in uw Azure-abonnement. U snel een nieuwe sleutelkluis maken door de stappen in Set te volgen [en een geheim op te halen uit Azure Key Vault met Azure CLI.](quick-create-cli.md)

## <a name="concepts"></a>Concepten

Event Grid is een eventingservice voor de cloud. Als je de stappen in deze gids volgt, abonneer je je op evenementen voor Key Vault en leid je evenementen naar Automatisering. Wanneer een van de geheimen in de sleutelkluis bijna verloopt, wordt Gebeurtenisraster op de hoogte gebracht van de statuswijziging en wordt een HTTP-BERICHT naar het eindpunt gemaakt. Een webhaak activeert vervolgens een automatiseringsuitvoering van een PowerShell-script.

![HTTP POSTSTROOMdiagram](media/image1.png)

## <a name="create-an-automation-account"></a>Een Automation-account maken

Maak een Automatiseringsaccount via de [Azure-portal:](https://portal.azure.com)

1.  Ga naar portal.azure.com en meld je aan bij je abonnement.

1.  Voer in het zoekvak **Automatiseringsaccounts**in .

1.  Selecteer **automatiseringsaccounts**onder het gedeelte **Services** van de vervolgkeuzelijst op de zoekbalk .

1.  Selecteer **Toevoegen**.

    ![Deelvenster Automatiseringsaccounts](media/image2.png)

1.  Voer de vereiste gegevens in het deelvenster **Automatiseringsaccount toevoegen** in en selecteer **Vervolgens Maken**.

## <a name="create-a-runbook"></a>Een runbook maken

Nadat uw Automatiseringsaccount gereed is, maakt u een runbook.

![Een gebruikersinterface voor runbook maken](media/image3.png)

1.  Selecteer het automatiseringsaccount dat u zojuist hebt gemaakt.

1.  Selecteer **Runbooks** onder **Procesautomatisering**.

1.  Selecteer **Een runbook maken**.

1.  Geef uw runbook een naam en selecteer **PowerShell** als runbook-type.

1.  Selecteer het runbook dat u hebt gemaakt en selecteer vervolgens de knop **Bewerken.**

1.  Voer de volgende code in (voor testdoeleinden) en selecteer de knop **Publiceren.** Deze actie retourneert het resultaat van de ontvangen POST-aanvraag.

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

![Gebruikersinterface runbook publiceren](media/image4.png)

## <a name="create-a-webhook"></a>Een webhook maken

Maak een webhook om uw nieuw gemaakte runbook te activeren.

1.  Selecteer **Webhooks** in de sectie **Resources** van het runbook dat u zojuist hebt gepubliceerd.

1.  Selecteer **Webhook toevoegen**.

    ![Knop Webhook toevoegen](media/image5.png)

1.  Selecteer **Nieuwe webhook maken**.

1. Geef de webhook een naam, stel een vervaldatum in en kopieer de URL.

    > [!IMPORTANT] 
    > U de URL niet meer bekijken nadat u deze hebt gemaakt. Zorg ervoor dat u een kopie opslaat op een veilige locatie waar u toegang hebt voor de rest van deze handleiding.

1. Selecteer **Parameters en voer instellingen uit** en selecteer **OK**. Voer geen parameters in. Hiermee wordt de knop **Maken** ingeschakeld.

1. Selecteer **OK** en selecteer **Vervolgens Maken**.

    ![Nieuwe Webhook-gebruikersinterface maken](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Een Event Grid-abonnement maken

Maak een Event Grid-abonnement via de [Azure-portal.](https://portal.azure.com)

1.  Ga naar uw sleutelkluis en selecteer het tabblad **Gebeurtenissen.** Als u het niet zien, controleert u of u de [voorbeeldversie van de portal gebruikt.](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true)

    ![Tabblad Gebeurtenissen in Azure-portal](media/image7.png)

1.  Selecteer de knop **Evenementabonnement.**

1.  Maak een beschrijvende naam voor het abonnement.

1.  Kies **Gebeurtenisrasterschema**.

1.  **Onderwerp resource** moet de sleutelkluis zijn die u wilt controleren op statuswijzigingen.

1.  Voor **Filter naar gebeurtenistypen**laat u alle opties geselecteerd (**9 geselecteerd**).

1.  Selecteer **Webhook** voor **Eindpunttype**.

1.  Kies **Een eindpunt selecteren**. Plak in het nieuwe contextvenster de webhook-URL van de stap [Een webhook maken](#create-a-webhook) in het veld **Eindpunt van abonnees.**

1.  Selecteer **Selectie bevestigen** in het contextvenster.

1.  Selecteer **Maken**.

    ![Gebeurtenisabonnement maken](media/image8.png)

## <a name="test-and-verify"></a>Testen en verifiëren

Controleer of uw abonnement op eventgrid correct is geconfigureerd. In deze test wordt ervan uitgegaan dat u zich hebt geabonneerd op de melding 'Geheime nieuwe versie gemaakt' in het [abonnement Een gebeurtenisraster maken](#create-an-event-grid-subscription)en dat u over de benodigde machtigingen beschikt om een nieuwe versie van een geheim in een sleutelkluis te maken.

![Test config van Event Grid-abonnement](media/image9.png)

![Deelvenster Maken-een-geheim](media/image10.png)

1.  Ga naar uw sleutelkluis op de Azure-portal.

1.  Maak een nieuw geheim. Stel de vervaldatum in op de volgende dag voor testdoeleinden.

1.  Selecteer op het tabblad **Gebeurtenissen** in uw sleutelkluis het abonnement op gebeurtenisraster dat u hebt gemaakt.

1.  Controleer **onder Statistieken**of een gebeurtenis is vastgelegd. Er worden twee evenementen verwacht: SecretNewVersion en SecretNearExpiry. Deze gebeurtenissen valideren dat Gebeurtenisraster de statuswijziging van het geheim in uw sleutelkluis heeft vastgelegd.

    ![Deelvenster Statistieken: controleer op vastgelegde gebeurtenissen](media/image11.png)

1.  Ga naar uw Automatiseringsaccount.

1.  Selecteer het tabblad **Runbooks** en selecteer vervolgens de runbook die u hebt gemaakt.

1.  Selecteer het tabblad **Webhooks** en controleer of de tijdstempel 'laatst geactiveerd' binnen 60 seconden is na het maken van het nieuwe geheim. Dit resultaat bevestigt dat Event Grid een post naar de webhook heeft gemaakt met de gebeurtenisgegevens van de statuswijziging in uw sleutelkluis en dat de webhook is geactiveerd.

    ![Tabblad Webhooks, Laatst geactiveerde tijdstempel](media/image12.png)

1. Ga terug naar uw runbook en selecteer het tabblad **Overzicht.**

1. Kijk naar de **lijst recente vacatures.** U moet zien dat een taak is gemaakt en dat de status is voltooid. Dit bevestigt dat de webhook het runbook heeft geactiveerd om het script uit te voeren.

    ![Webhook Recente vacatures lijst](media/image13.png)

1. Selecteer de recente taak en bekijk het berichtverzoek dat is verzonden van Gebeurtenisraster naar de webhook. Bestudeer de JSON en zorg ervoor dat de parameters voor uw sleutelkluis en gebeurtenistype correct zijn. Als de parameter 'gebeurtenistype' in het JSON-object overeenkomt met de gebeurtenis die zich heeft voorgedaan in de sleutelkluis (in dit voorbeeld Microsoft.KeyVault.SecretNearExpiry), is de test geslaagd.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="you-cant-create-an-event-subscription"></a>U geen evenementabonnement maken

Registreer Event Grid en de belangrijkste vaultprovider opnieuw in uw Azure-abonnementsbronproviders. Zie [Azure-bronproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd! Als u al deze stappen correct hebt gevolgd, bent u nu klaar om programmatisch te reageren op statuswijzigingen van geheimen die zijn opgeslagen in uw sleutelkluis.

Als u een op polling gebaseerd systeem hebt gebruikt om te zoeken naar statuswijzigingen van geheimen in uw sleutelkluizen, u nu deze meldingsfunctie gebruiken. U het testscript in uw runbook ook vervangen door code om uw geheimen programmatisch te vernieuwen wanneer ze op het punt staan te verlopen.

Meer informatie:


- Overzicht: [Key Vault bewaken met Azure Event Grid (voorbeeld)](event-grid-overview.md)
- How to: [E-mail ontvangen wanneer een sleutelkluis geheim verandert](event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenisschema voor Azure Key Vault (voorbeeld)](../event-grid/event-schema-key-vault.md)
- [Overzicht van Azure Key Vault](key-vault-overview.md)
- [Overzicht van Azure Event Grid](../event-grid/overview.md)
- [Overzicht van Azure Automation](../automation/index.yml)
