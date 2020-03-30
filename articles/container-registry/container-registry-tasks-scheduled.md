---
title: Zelfstudie - Een ACR-taak plannen
description: Lees in deze zelfstudie hoe u een Azure Container Registry Task uitvoert volgens een gedefinieerd schema door een of meer timertriggers in te stellen
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 3202b5d8c426165d81129f1affa69b3a3d515ce9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402877"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Een ACR-taak uitvoeren volgens een gedefinieerd schema

In deze zelfstudie ziet u hoe u een [ACR-taak](container-registry-tasks-overview.md) volgens een schema uitvoert. Plan een taak door een of meer *timertriggers*in te stellen. Timertriggers kunnen alleen of in combinatie met andere taaktriggers worden gebruikt.

Lees in deze zelfstudie over het plannen van taken en:

> [!div class="checklist"]
> * Een taak maken met een timertrigger
> * Timertriggers beheren

Het plannen van een taak is handig voor scenario's als de volgende:

* Voer een containerbelasting uit voor geplande onderhoudsbewerkingen. Voer bijvoorbeeld een container-app uit om overbodige afbeeldingen uit uw register te verwijderen.
* Voer tijdens de werkdag een reeks tests uit op een productieafbeelding als onderdeel van uw live-site monitoring.

U de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de voorbeelden in dit artikel uit te voeren. Als u het lokaal wilt gebruiken, is versie 2.0.68 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Over het plannen van een taak

* **Trigger met cron expressie** - De timer trigger voor een taak maakt gebruik van een *cron expressie*. De expressie is een tekenreeks met vijf velden die de minuut, het uur, de dag, de maand en de dag van de week opgeven om de taak te activeren. Frequenties tot één keer per minuut worden ondersteund.

  De expressie `"0 12 * * Mon-Fri"` activeert bijvoorbeeld elke weekdag een taak om 12.00 uur UTC. Zie [details](#cron-expressions) later in dit artikel.
* **Meerdere timertriggers** - Het toevoegen van meerdere timers aan een taak is toegestaan, zolang de schema's verschillen.
    * Geef meerdere timertriggers op wanneer u de taak maakt of voeg ze later toe.
    * Optioneel geeft u de triggers een naam voor eenvoudiger beheer of acr-taken geven standaardtriggernamen.
    * Als timerschema's elkaar tegelijk overlappen, activeert ACR Tasks de taak op de geplande tijd voor elke timer.
* **Andere taaktriggers** - In een taak die door een timer wordt geactiveerd, u ook triggers inschakelen op basis van [broncodecommit](container-registry-tutorial-build-task.md) of [basisafbeeldingsupdates.](container-registry-tutorial-base-image-update.md) Net als andere ACR-taken u ook handmatig een geplande taak [activeren.][az-acr-task-run]

## <a name="create-a-task-with-a-timer-trigger"></a>Een taak maken met een timertrigger

Wanneer u een taak maakt met de opdracht [az acr-taak maken,][az-acr-task-create] u optioneel een timertrigger toevoegen. Voeg `--schedule` de parameter toe en geef een cron-expressie door voor de timer.

Als eenvoudig voorbeeld activeert de `hello-world` volgende opdracht het uitvoeren van de afbeelding vanuit Docker Hub elke dag om 21:00 UTC. De taak wordt uitgevoerd zonder broncodecontext.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Voer de opdracht [az acr-taakweerom][az-acr-task-show] te zien dat de timertrigger is geconfigureerd. Standaard is de trigger voor de update van de basisafbeelding ook ingeschakeld.

```azurecli
az acr task show --name mytask --registry registry --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Activeer de taak handmatig met [az acr taak draaien][az-acr-task-run] om ervoor te zorgen dat het goed is ingesteld:

```azurecli
az acr task run --name mytask --registry myregistry
```

Als de container succesvol wordt uitgevoerd, is de uitvoer vergelijkbaar met de volgende:

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

Voer na de geplande tijd de opdracht [lijst-runs van az acr-taken uit][az-acr-task-list-runs] om te controleren of de timer de taak heeft geactiveerd zoals verwacht:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

Wanneer de timer succesvol is, is de uitvoer vergelijkbaar met de volgende:

```output
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Timertriggers beheren

Gebruik de az [acr-taaktimeropdrachten][az-acr-task-timer] om de timertriggers voor een ACR-taak te beheren.

### <a name="add-or-update-a-timer-trigger"></a>Een timertrigger toevoegen of bijwerken

Nadat een taak is gemaakt, voegt u optioneel een timertrigger toe met behulp van de opdracht [taaktimer van AZ ACR.][az-acr-task-timer-add] In het volgende voorbeeld wordt een timertriggernaam *timer2* toegevoegd aan *mijn taak die* eerder is gemaakt. Deze timer activeert de taak elke dag om 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Werk de planning van een bestaande trigger bij of wijzig de status met behulp van de update-update van de [AZ ACR-taaktimer.][az-acr-task-timer-update] Werk bijvoorbeeld de trigger met de naam *timer2* bij om de taak om 11:30 UTC te activeren:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Lijsttimertriggers

De [opdracht op de az acr-taaktimer][az-acr-task-timer-list] geeft de timertriggers weer die zijn ingesteld voor een taak:

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

### <a name="remove-a-timer-trigger"></a>Een timertrigger verwijderen

Gebruik de opdracht [taaktimer van AZ acr om][az-acr-task-timer-remove] een timertrigger uit een taak te verwijderen. In het volgende voorbeeld wordt de *timer2-trigger* uit *mijntaak verwijderd:*

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron-expressies

ACR Tasks gebruikt de [NCronTab-bibliotheek](https://github.com/atifaziz/NCrontab) om cron-expressies te interpreteren. Ondersteunde expressies in ACR-taken hebben vijf vereiste velden, gescheiden door witruimte:

`{minute} {hour} {day} {month} {day-of-week}`

De tijdzone die wordt gebruikt bij de cron-expressies is Coordinated Universal Time (UTC). De uren zijn in 24-uursformaat.

> [!NOTE]
> ACR-taken ondersteunen `{second}` het `{year}` veld niet in cron-expressies. Als u een cron-expressie kopieert die in een ander systeem wordt gebruikt, moet u deze velden verwijderen als deze worden gebruikt.

Elk veld kan een van de volgende typen waarden hebben:

|Type  |Voorbeeld  |Wanneer geactiveerd  |
|---------|---------|---------|
|Een specifieke waarde |<nobr>`"5 * * * *"`</nobr>|elk uur om 5 minuten na het uur|
|Alle waarden`*`( )|<nobr>`"* 5 * * *"`</nobr>|elke minuut van het uur dat begint 5:00 GMT (60 keer per dag)|
|Een bereik`-` (operator)|<nobr>`"0 1-3 * * *"`</nobr>|3 keer per dag, om 1:00, 2:00 en 3:00 GMT|
|Een set waarden`,` (operator)|<nobr>`"20,30,40 * * * *"`</nobr>|3 keer per uur, op 20 minuten, 30 minuten en 40 minuten na het uur|
|Een intervalwaarde`/` (operator)|<nobr>`"*/10 * * * *"`</nobr>|6 keer per uur, op 10 minuten, 20 minuten, enzovoort, voorbij het uur

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Voorbeelden van Cron

|Voorbeeld|Wanneer geactiveerd  |
|---------|---------|
|`"*/5 * * * *"`|eens in de vijf minuten|
|`"0 * * * *"`|eenmaal op de top van elk uur|
|`"0 */2 * * *"`|eens in de twee uur|
|`"0 9-17 * * *"`|eenmaal per uur van 9:00 tot 17:00 UTC|
|`"30 9 * * *"`|om 9:30 UTC elke dag|
|`"30 9 * * 1-5"`|om 9:30 UTC elke weekdag|
|`"30 9 * Jan Mon"`|om 9:30 UTC elke maandag in januari|

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources wilt verwijderen die u in deze zelfstudiereeks hebt gemaakt, inclusief het containerregister of de containerregisters, containerinstantie, sleutelkluis en serviceprincipal, geeft u de volgende opdrachten uit:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Azure Container Registry-taken maakt die automatisch worden geactiveerd door een timer. 

Zie Afbeeldingen automatisch [verwijderen uit een Azure-containerregister](container-registry-auto-purge.md)voor een voorbeeld van het gebruik van een geplande taak om opslagplaatsen in een register op te schonen.

Zie andere artikelen in de [zelfstudiereeks ACR Tasks](container-registry-tutorial-quick-task.md)voor taken die worden geactiveerd door broncodecommits of basisafbeeldingsupdates.



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
