---
title: Zero-downtime implementatie voor duurzame functies
description: Meer informatie over hoe u de orkestratie voor duurzame functies inschakelen voor implementaties zonder downtime.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: 8e12d58c0077084c181d111b0b017665b74b9157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231259"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Zero-downtime implementatie voor duurzame functies

Het [betrouwbare uitvoeringsmodel](durable-functions-checkpointing-and-replay.md) van duurzame functies vereist dat orkestraties deterministisch zijn, wat een extra uitdaging creëert om rekening mee te houden wanneer u updates implementeert. Wanneer een implementatie wijzigingen in handtekeningen van de activiteitsfunctie of orchestrator-logica bevat, mislukken in-flight orchestration-exemplaren. Deze situatie is vooral een probleem voor gevallen van langlopende orkestraties, die uren of dagen werk kunnen vertegenwoordigen.

Om te voorkomen dat deze fouten zich voordoen, hebt u twee opties: 
- Stel uw implementatie uit totdat alle lopende orchestration-exemplaren zijn voltooid.
- Zorg ervoor dat alle lopende orchestration-exemplaren de bestaande versies van uw functies gebruiken. 

> [!NOTE]
> In dit artikel vindt u richtlijnen voor functies die zich richten op duurzame functies 1.x. Het is niet bijgewerkt om rekening te houden met wijzigingen geïntroduceerd in duurzame functies 2.x. Zie [Versies duurzame functies](durable-functions-versions.md)voor meer informatie over de verschillen tussen extensieversies.

In de volgende grafiek worden de drie belangrijkste strategieën vergeleken om een zero-downtime-implementatie voor duurzame functies te bereiken: 

| Strategie |  Wanneer gebruikt u dit? | Voordelen | Nadelen |
| -------- | ------------ | ---- | ---- |
| [Versiebeheer](#versioning) |  Toepassingen die geen frequente [wijzigingen ondervinden.](durable-functions-versioning.md) | Eenvoudig te implementeren. |  Grotere functie-app grootte in het geheugen en het aantal functies.<br/>Code duplicatie. |
| [Statuscontrole met sleuf](#status-check-with-slot) | Een systeem dat geen langlopende orkestraties heeft die langer dan 24 uur of vaak overlappende orkestraties hebben. | Eenvoudige code basis.<br/>Vereist geen extra functie-appbeheer. | Vereist extra opslagaccount of taakhubbeheer.<br/>Vereist perioden waarin er geen orkestraties worden uitgevoerd. |
| [Toepassingsroutering](#application-routing) | Een systeem dat geen perioden heeft waarin orkestraties niet worden uitgevoerd, zoals die perioden met orkestraties die meer dan 24 uur duren of met vaak overlappende orkestraties. | Hiermee verwerkt u nieuwe versies van systemen met voortdurend lopende orkestraties die baanbrekende wijzigingen hebben. | Vereist een intelligente applicatierouter.<br/>Kan maximaal het aantal functie-apps toegestaan door uw abonnement. De standaardwaarde is 100. |

## <a name="versioning"></a>Versiebeheer

Definieer nieuwe versies van uw functies en laat de oude versies in uw functie-app. Zoals u in het diagram zien, wordt de versie van een functie onderdeel van de naam. Omdat eerdere versies van functies behouden blijven, kunnen in-flight orchestration-exemplaren blijven verwijzen. Ondertussen vragen om nieuwe orchestration-exemplaren om de nieuwste versie, waarnaar uw orchestration-clientfunctie kan verwijzen vanuit een app-instelling.

![Versiestrategie](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

In deze strategie moet elke functie worden gekopieerd en moeten de verwijzingen naar andere functies worden bijgewerkt. U het gemakkelijker maken door een script te schrijven. Hier is een [voorbeeldproject](https://github.com/TsuyoshiUshio/DurableVersioning) met een migratiescript.

>[!NOTE]
>Deze strategie maakt gebruik van implementatiesleuven om downtime tijdens de implementatie te voorkomen. Zie Azure Functions deployment slots voor meer gedetailleerde informatie over het maken en gebruiken van nieuwe [implementatiesleuven.](../functions-deployment-slots.md)

## <a name="status-check-with-slot"></a>Statuscontrole met sleuf

Terwijl de huidige versie van uw functie-app wordt uitgevoerd in uw productiesleuf, implementeert u de nieuwe versie van uw functie-app in uw faseringssleuf. Voordat u uw productie- en faseringsruimten verwisselt, controleert u of er lopende orchestration-exemplaren zijn. Nadat alle orchestration-exemplaren zijn voltooid, u de swap uitvoeren. Deze strategie werkt wanneer u voorspelbare perioden hebt wanneer er geen orkestratie-exemplaren tijdens de vlucht zijn. Dit is de beste aanpak wanneer uw orkestraties niet lang lopen en wanneer uw orkestratie-uitvoeringen elkaar niet vaak overlappen.

### <a name="function-app-configuration"></a>Configuratie van de functie-app

Gebruik de volgende procedure om dit scenario in te stellen.

1. [Voeg implementatiesleuven toe](../functions-deployment-slots.md#add-a-slot) aan uw functie-app voor fasering en productie.

1. Stel voor elke sleuf de [azurewebJobsStorage-toepassingsinstelling in](../functions-app-settings.md#azurewebjobsstorage) op de verbindingstekenreeks van een gedeeld opslagaccount. Deze tekenreeks voor opslagaccountverbinding wordt gebruikt door de runtime van Azure-functies. Dit account wordt gebruikt door de runtime van Azure Functions en beheert de sleutels van de functie.

1. Maak voor elke sleuf bijvoorbeeld een nieuwe `DurableManagementStorage`app-instelling. Stel de waarde in op de verbindingstekenreeks van verschillende opslagaccounts. Deze opslagaccounts worden gebruikt door de extensie Duurzame functies voor [een betrouwbare uitvoering.](durable-functions-checkpointing-and-replay.md) Gebruik een apart opslagaccount voor elke sleuf. Markeer deze instelling niet als instelling voor implementatiesleuf.

1. Geef in de [sectie host.json-bestand](durable-functions-bindings.md#hostjson-settings)van uw `azureStorageConnectionStringName` functie-app de sectie durableTask op als de naam van de app-instelling die u in stap 3 hebt gemaakt.

In het volgende diagram ziet u de beschreven configuratie van implementatiesleuven en opslagaccounts. In dit potentiële scenario voor predeployment wordt versie 2 van een functie-app uitgevoerd in de productiesleuf, terwijl versie 1 in de faseringssleuf blijft staan.

![Implementatiesleuven en opslagaccounts](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host.json voorbeelden

De volgende JSON-fragmenten zijn voorbeelden van de instelling van de verbindingstekenreeks in het *bestand host.json.*

#### <a name="functions-20"></a>Functies 2.0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "DurableManagementStorage"
    }
  }
}
```

#### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>CI/CD-pijplijnconfiguratie

Configureer de CI/CD-pijplijn alleen als de functie-app geen in behandeling zijnde of lopende orchestration-exemplaren heeft. Wanneer u Azure Pipelines gebruikt, u een functie maken die voor deze voorwaarden controleert, zoals in het volgende voorbeeld:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var status = await client.GetStatusAsync(new DateTime(2015,10,10), null, runtimeStatus);
    return (ActionResult) new OkObjectResult(new Status() {HasRunning = (status.Count != 0)});
}
```

Configureer vervolgens de faseringspoort om te wachten tot er geen orkestraties worden uitgevoerd. Zie [Implementatiebesturingselement vrijgeven met behulp van poorten voor](/azure/devops/pipelines/release/approvals/gates?view=azure-devops) meer informatie

![Implementatiepoort](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines controleert uw functie-app op het uitvoeren van orchestration-exemplaren voordat uw implementatie wordt gestart.

![Implementatiepoort (actief)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Nu moet de nieuwe versie van uw functie-app worden geïmplementeerd in de faseringssleuf.

![Faseringssleuf](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Tot slot, swap slots. 

Toepassingsinstellingen die niet zijn gemarkeerd als instellingen voor implementatiesleuf, worden ook verwisseld, zodat de versie 2-app de verwijzing naar opslagaccount A behoudt. Omdat de orchestration-status wordt bijgehouden in het opslagaccount, blijven alle orkestraties die op de versie 2-app worden uitgevoerd, zonder onderbreking in de nieuwe sleuf worden uitgevoerd.

![Implementatiesite](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Als u voor beide sleuven hetzelfde opslagaccount wilt gebruiken, u de namen van uw taakhubs wijzigen. In dit geval moet u de status van uw sleuven en de HubName-instellingen van uw app beheren. Zie [Taakhubs voor](durable-functions-task-hubs.md)meer informatie in Duurzame functies .

## <a name="application-routing"></a>Toepassingsroutering

Deze strategie is het meest complex. Het kan echter worden gebruikt voor functie-apps die geen tijd hebben tussen het uitvoeren van orkestraties.

Voor deze strategie moet u een *toepassingsrouter* maken voor uw duurzame functies. Deze router kan worden geïmplementeerd met duurzame functies. De router heeft de verantwoordelijkheid om:

* Implementeer de functie-app.
* Beheer de versie van duurzame functies. 
* Route orkestratie aanvragen om apps te functioneren.

De eerste keer dat een orchestration-aanvraag wordt ontvangen, voert de router de volgende taken uit:

1. Hiermee maakt u een nieuwe functie-app in Azure.
2. Implementeert de code van uw functie-app naar de nieuwe functie-app in Azure.
3. Stuurt het orchestration-verzoek door naar de nieuwe app.

De router beheert de status van welke versie van de code van uw app wordt geïmplementeerd naar welke functie-app in Azure.

![Toepassingsroutering (eerste keer)](media/durable-functions-zero-downtime-deployment/application-routing.png)

De router stuurt implementatie- en orkestratieaanvragen naar de juiste functie-app op basis van de versie die met de aanvraag wordt verzonden. Het negeert de patchversie.

Wanneer u een nieuwe versie van uw app implementeert zonder een wijziging, u de patchversie verhogen. De router implementeert naar uw bestaande functie-app en verzendt aanvragen voor de oude en nieuwe versies van de code, die worden doorgestuurd naar dezelfde functie-app.

![Toepassingsroutering (geen wijziging)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Wanneer u een nieuwe versie van uw app implementeert met een laatste wijziging, u de hoofd- of secundaire versie verhogen. Vervolgens maakt de toepassingsrouter een nieuwe functie-app in Azure, wordt deze geïmplementeerd en worden aanvragen voor de nieuwe versie van uw app naar deze app overgemaakt. In het volgende diagram blijven lopende orkestraties op de 1.0.1-versie van de app draaien, maar aanvragen voor de 1.1.0-versie worden doorgestuurd naar de nieuwe functie-app.

![Toepassingsroutering (wijziging verbreken)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

De router bewaakt de status van orkestraties op de 1.0.1-versie en verwijdert apps nadat alle orkestraties zijn voltooid. 

### <a name="tracking-store-settings"></a>Winkelinstellingen bijhouden

Elke functie-app moet aparte planningswachtrijen gebruiken, mogelijk in afzonderlijke opslagaccounts. Als u alle instanties van orchestrations in alle versies van uw toepassing wilt opvragen, u instantie- en geschiedenistabellen delen in uw functie-apps. U tabellen delen `trackingStoreConnectionStringName` door `trackingStoreNamePrefix` de instellingen en instellingen in het instellingenbestand [host.json te](durable-functions-bindings.md#host-json) configureren, zodat ze allemaal dezelfde waarden gebruiken.

Zie [Instanties beheren in Duurzame functies in Azure](durable-functions-instance-management.md)voor meer informatie.

![Winkelinstellingen bijhouden](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Duurzame functies voor versies](durable-functions-versioning.md)

