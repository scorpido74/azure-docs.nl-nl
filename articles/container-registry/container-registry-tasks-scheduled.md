---
title: 'Zelf studie: een ACR-taak plannen'
description: In deze zelf studie leert u hoe u een Azure Container Registry taak uitvoert volgens een gedefinieerd schema door een of meer timer triggers in te stellen
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 3202b5d8c426165d81129f1affa69b3a3d515ce9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78402877"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Een ACR-taak uitvoeren volgens een gedefinieerd schema

In deze zelf studie ziet u hoe u een [ACR-taak](container-registry-tasks-overview.md) kunt uitvoeren volgens een schema. Een taak plannen door een of meer *Timer triggers*in te stellen. Timer triggers kunnen alleen worden gebruikt of in combi natie met andere taak triggers.

In deze zelf studie vindt u informatie over het plannen van taken en:

> [!div class="checklist"]
> * Een taak maken met een timer trigger
> * Timer Triggers beheren

Het plannen van een taak is handig voor scenario's zoals de volgende:

* Voer een container werkbelasting uit voor geplande onderhouds bewerkingen. Voer bijvoorbeeld een container-app uit om overbodige installatie kopieën uit het REGI ster te verwijderen.
* Voer tijdens de werkdag een set tests uit op een productie-afbeelding als onderdeel van de bewaking van uw live-site.

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de voor beelden in dit artikel uit te voeren. Als u het lokaal wilt gebruiken, is versie 2.0.68 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.


## <a name="about-scheduling-a-task"></a>Over het plannen van een taak

* **Activeren met cron-expressie** -de timer trigger voor een taak maakt gebruik van een *cron-expressie*. De expressie is een teken reeks met vijf velden die de minuut, het uur, de dag, de maand en de dag van de week opgeven om de taak te activeren. Frequenties van Maxi maal één keer per minuut worden ondersteund.

  Met de expressie wordt bijvoorbeeld `"0 12 * * Mon-Fri"` een taak geactiveerd om 12.00 UTC op elke werkdag. [Meer informatie](#cron-expressions) vindt u verderop in dit artikel.
* **Meerdere timer triggers** : het toevoegen van meerdere tijds duren aan een taak is toegestaan, zolang de planningen verschillen.
    * Geef meerdere timer triggers op wanneer u de taak maakt of voeg ze later toe.
    * Noem eventueel de triggers voor eenvoudiger beheer, of ACR taken bieden standaard namen voor triggers.
    * Als timers overlap pingen per keer worden gepland, wordt de taak door ACR-taken geactiveerd op het geplande tijdstip voor elke timer.
* **Andere taak triggers** : in een taak die door een timer wordt geactiveerd, kunt u ook triggers inschakelen op basis van updates van de [bron code](container-registry-tutorial-build-task.md) of [basis installatie kopie](container-registry-tutorial-base-image-update.md). Net als andere ACR taken kunt u een geplande taak ook [hand matig activeren][az-acr-task-run] .

## <a name="create-a-task-with-a-timer-trigger"></a>Een taak maken met een timer trigger

Wanneer u een taak maakt met de opdracht [AZ ACR Task Create][az-acr-task-create] , kunt u eventueel een timer trigger toevoegen. Voeg de `--schedule` para meter toe en geef een cron-expressie door voor de timer.

Als eenvoudig voor beeld wordt met de volgende opdracht de `hello-world` installatie kopie vanaf docker hub elke dag uitgevoerd om 21:00 UTC. De taak wordt uitgevoerd zonder een bron code context.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Voer de opdracht [AZ ACR Task show][az-acr-task-show] uit om te zien dat de timer trigger is geconfigureerd. De trigger voor het bijwerken van de basis installatie kopie is standaard ook ingeschakeld.

```azurecli
az acr task show --name mytask --registry registry --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Activeer de taak hand matig met [AZ ACR Task run][az-acr-task-run] om er zeker van te zijn dat deze correct is ingesteld:

```azurecli
az acr task run --name mytask --registry myregistry
```

Als de container wordt uitgevoerd, ziet de uitvoer er ongeveer als volgt uit:

```output
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Na de geplande tijd voert u de opdracht [AZ ACR Task List-runs][az-acr-task-list-runs] uit om te controleren of de timer de taak als verwacht heeft geactiveerd:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

Wanneer de timer is geslaagd, is de uitvoer vergelijkbaar met het volgende:

```output
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Timer Triggers beheren

Gebruik de opdracht [AZ ACR Task timer][az-acr-task-timer] om de timer triggers voor een ACR-taak te beheren.

### <a name="add-or-update-a-timer-trigger"></a>Een timer trigger toevoegen of bijwerken

Nadat een taak is gemaakt, voegt u eventueel een timer trigger toe met behulp van de opdracht [AZ ACR taak timer add][az-acr-task-timer-add] . In het volgende voor beeld wordt een timer trigger naam *timer2* toegevoegd aan *mytask* die u eerder hebt gemaakt. Deze timer activeert de taak elke dag om 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Het schema van een bestaande trigger bijwerken of de status ervan wijzigen met behulp van de opdracht [AZ ACR Task timer update][az-acr-task-timer-update] . Werk bijvoorbeeld de trigger met de naam *timer2* bij om de taak te activeren op 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Timer triggers weer geven

De opdracht [AZ ACR Task timer List][az-acr-task-timer-list] bevat de timer triggers die zijn ingesteld voor een taak:

```azurecli
az acr task timer list --name mytask --registry myregistry
```

Voorbeelduitvoer:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Een timer trigger verwijderen

Gebruik de opdracht [AZ ACR taak timer Remove][az-acr-task-timer-remove] om een timer trigger van een taak te verwijderen. In het volgende voor beeld wordt de trigger *timer2* verwijderd uit *mytask*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron-expressies

ACR-taken gebruiken de [NCronTab](https://github.com/atifaziz/NCrontab) -bibliotheek om cron-expressies te interpreteren. Ondersteunde expressies in ACR-taken bevatten vijf verplichte velden, gescheiden door witruimte:

`{minute} {hour} {day} {month} {day-of-week}`

De tijd zone die wordt gebruikt met de cron-expressies is Coordinated Universal Time (UTC). Uren hebben een 24-uurs notatie.

> [!NOTE]
> ACR-taken bieden geen ondersteuning `{second}` `{year}` voor het veld or in cron-expressies. Als u een cron-expressie die wordt gebruikt in een ander systeem kopieert, moet u deze velden verwijderen als ze worden gebruikt.

Elk veld kan een van de volgende typen waarden hebben:

|Type  |Voorbeeld  |Wanneer geactiveerd  |
|---------|---------|---------|
|Een specifieke waarde |<nobr>`"5 * * * *"`</nobr>|elk uur om 5 minuten na het hele uur|
|Alle waarden ( `*` )|<nobr>`"* 5 * * *"`</nobr>|elke minuut van het uur vanaf 5:00 UTC (60 keer per dag)|
|Een bereik ( `-` operator)|<nobr>`"0 1-3 * * *"`</nobr>|3 keer per dag, om 1:00, 2:00 en 3:00 UTC|
|Een reeks waarden ( `,` operator)|<nobr>`"20,30,40 * * * *"`</nobr>|3 keer per uur, om 20 minuten, 30 minuten en 40 minuten na het hele uur|
|Een interval waarde ( `/` operator)|<nobr>`"*/10 * * * *"`</nobr>|6 keer per uur, 10 minuten, 20 minuten, enzovoort, na het hele uur

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron-voor beelden

|Voorbeeld|Wanneer geactiveerd  |
|---------|---------|
|`"*/5 * * * *"`|eenmaal per vijf minuten|
|`"0 * * * *"`|eenmaal aan de bovenkant van elk uur|
|`"0 */2 * * *"`|eenmaal per twee uur|
|`"0 9-17 * * *"`|eenmaal per uur van 9:00 tot 17:00 UTC|
|`"30 9 * * *"`|elke dag om 9:30 UTC|
|`"30 9 * * 1-5"`|om 9:30 UTC elke werkdag|
|`"30 9 * Jan Mon"`|om 9:30 UTC elke maandag in januari|

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources wilt verwijderen die u in deze zelfstudie reeks hebt gemaakt, met inbegrip van het container register of de registers, het container exemplaar, de sleutel kluis en de Service-Principal, geeft u de volgende opdrachten op:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Azure Container Registry-taken maakt die automatisch worden geactiveerd door een timer. 

Zie [automatisch installatie kopieën verwijderen uit een Azure container Registry](container-registry-auto-purge.md)voor een voor beeld van het gebruik van een geplande taak om opslag plaatsen in een REGI ster op te schonen.

Zie voor voor beelden van taken die worden geactiveerd door het door voeren van de bron code of het bijwerken van basis installatie kopieën andere artikelen in de [reeks zelf](container-registry-tutorial-quick-task.md)studies over ACR-taken.



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
