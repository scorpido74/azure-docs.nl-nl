---
title: Algemene problemen
description: Meer informatie over het oplossen van veelvoorkomende problemen bij het implementeren, uitvoeren of beheren van Azure Container Instances
ms.topic: article
ms.date: 09/25/2019
ms.custom: mvc
ms.openlocfilehash: 07cdbfb27aaf9076e726ebda861ed24996e10135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533382"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Veelvoorkomende problemen in Azure Container Instances oplossen

In dit artikel ziet u hoe u veelvoorkomende problemen oplost voor het beheren of implementeren van containers naar Azure Container Instances. Zie ook [veelgestelde vragen.](container-instances-faq.md)

Als u extra ondersteuning nodig hebt, raadpleegt u de beschikbare **Help + ondersteuningsopties** in de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="issues-during-container-group-deployment"></a>Problemen tijdens de implementatie van containergroep
### <a name="naming-conventions"></a>Naamconventies

Bij het definiëren van uw containerspecificatie vereisen bepaalde parameters naleving van naamgevingsbeperkingen. Hieronder vindt u een tabel met specifieke vereisten voor eigenschappen van containergroepen. Zie [Naamgevingsconventies][azure-name-restrictions] in het Azure Architecture Center voor meer informatie over Azure-naamgevingsconventies.

| Bereik | Lengte | Hoofdlettergebruik | Geldige tekens | Voorgesteld patroon | Voorbeeld |
| --- | --- | --- | --- | --- | --- |
| Naam containergroep | 1-64 |Niet hoofdlettergevoelig |Alfanumeriek en koppelteken overal behalve het eerste of laatste teken |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Containernaam | 1-64 |Niet hoofdlettergevoelig |Alfanumeriek en koppelteken overal behalve het eerste of laatste teken |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Containerpoorten | Tussen 1 en 65535 |Geheel getal |Geheel getal tussen 1 en 65535 |`<port-number>` |`443` |
| DNS-naamlabel | 5-63 |Niet hoofdlettergevoelig |Alfanumeriek en koppelteken overal behalve het eerste of laatste teken |`<name>` |`frontend-site1` |
| Omgevingsvariabele | 1-63 |Niet hoofdlettergevoelig |Alfanumerieke en onderstrepen (_) overal behalve het eerste of laatste teken |`<name>` |`MY_VARIABLE` |
| Volumenaam | 5-63 |Niet hoofdlettergevoelig |Kleine letters en cijfers en koppeltekens overal behalve het eerste of laatste teken. Kan niet twee opeenvolgende koppeltekens bevatten. |`<name>` |`batch-output-volume` |

### <a name="os-version-of-image-not-supported"></a>OS-versie van afbeelding niet ondersteund

Als u een afbeelding opgeeft die Azure `OsVersionNotSupported` Container Instances niet ondersteunt, wordt een fout geretourneerd. De fout is vergelijkbaar `{0}` met volgende, waar is de naam van de afbeelding die u hebt geprobeerd te implementeren:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Deze fout wordt het vaakst aangetroffen bij het implementeren van Windows-afbeeldingen die zijn gebaseerd op semi-jaarlijkse kanaalrelease 1709 of 1803, die niet worden ondersteund. Zie Veelgestelde vragen voor ondersteunde [Windows-afbeeldingen](container-instances-faq.md#what-windows-base-os-images-are-supported)in Azure Container Instances.

### <a name="unable-to-pull-image"></a>Kan afbeelding niet trekken

Als Azure Container Instances in eerste instantie niet in staat is om uw afbeelding op te vragen, wordt deze gedurende een bepaalde periode opnieuw geprobeerd. Als de bewerking voor het trekken van afbeeldingen blijft mislukken, mislukt ACI uiteindelijk de implementatie en ziet u mogelijk een `Failed to pull image` fout.

Als u dit probleem wilt oplossen, verwijdert u de containerinstantie en probeert u uw implementatie opnieuw. Controleer of de afbeelding in het register bestaat en dat u de naam van de afbeelding correct hebt getypt.

Als de afbeelding niet kan worden getrokken, worden gebeurtenissen zoals de volgende weergegeven in de uitvoer van [de AZ-container:][az-container-show]

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Fout resource niet beschikbaar

Als gevolg van de wisselende regionale resourcebelasting in Azure, ontvangt u mogelijk de volgende fout wanneer u een containerinstantie probeert te implementeren:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Deze fout geeft aan dat als gevolg van zware belasting in het gebied waarin u probeert te implementeren, de resources die zijn opgegeven voor uw container op dat moment niet kunnen worden toegewezen. Gebruik een of meer van de volgende beperkende stappen om het probleem op te lossen.

* Controleren of de instellingen voor containerimplementatie binnen de parameters vallen die zijn gedefinieerd in [de beschikbaarheid van regio's voor Azure Container Instances](container-instances-region-availability.md)
* Lagere CPU- en geheugeninstellingen voor de container opgeven
* Implementeren naar een andere Azure-regio
* Op een later tijdstip implementeren

## <a name="issues-during-container-group-runtime"></a>Problemen tijdens runtime containergroep
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Container wordt voortdurend afgesloten en opnieuw opgestart (geen langlopend proces)

Containergroepen worden standaard gestart met een [herstartbeleid](container-instances-restart-policy.md) van **Altijd,** zodat containers in de containergroep altijd opnieuw worden gestart nadat ze zijn voltooid. Mogelijk moet u dit wijzigen in **OnFailure** of **Nooit** als u van plan bent op taakgebaseerde containers uit te voeren. Als u **OnFailure** opgeeft en nog steeds continu opnieuw wordt opgestart, is er mogelijk een probleem met de toepassing of het script dat in uw container is uitgevoerd.

Bij het uitvoeren van containergroepen zonder langlopende processen u herhaalde uitgangen en opnieuw opstarten zien met afbeeldingen zoals Ubuntu of Alpine. Verbinding maken via [EXEC](container-instances-exec.md) werkt niet omdat de container geen proces heeft om hem in leven te houden. Als u dit probleem wilt oplossen, neemt u een opdracht start als de volgende op met de implementatie van uw containergroep om de container draaiende te houden.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

De API voor containerinstanties `restartCount` en de Azure-portal bevat een eigenschap. Als u het aantal opnieuw opstartenvoor een container wilt controleren, u de opdracht [AZ-containershow gebruiken][az-container-show] in de Azure CLI. In het volgende voorbeeld uitvoer (die is afgekapt voor beknoptheid), u de `restartCount` eigenschap aan het einde van de uitvoer te zien.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> De meeste containerafbeeldingen voor Linux-distributies stellen een shell in, zoals bash, als de standaardopdracht. Aangezien een shell op zichzelf geen langlopende service is, worden deze containers onmiddellijk afgesloten en vallen ze in een herstartlus wanneer deze is geconfigureerd met het **standaardbeleid Altijd** opnieuw opstarten.

### <a name="container-takes-a-long-time-to-start"></a>Container duurt lang om te beginnen

De drie belangrijkste factoren die bijdragen aan de opstarttijd van containers in Azure Container Instances zijn:

* [Afbeeldingsgrootte](#image-size)
* [Afbeeldingslocatie](#image-location)
* [Afbeeldingen in cache](#cached-images)

Windows-afbeeldingen hebben [aanvullende overwegingen.](#cached-images)

#### <a name="image-size"></a>Afbeeldingsgrootte

Als het lang duurt voordat uw container is gestart, maar uiteindelijk slaagt, moet u eerst kijken naar de grootte van uw containerafbeelding. Omdat Azure Container Instances uw containerafbeelding op aanvraag optrekt, is de opstarttijd die u ziet direct gerelateerd aan de grootte ervan.

U de grootte van uw `docker images` containerafbeelding bekijken met de opdracht docker-CLI:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

De sleutel om de afbeeldingsformaten klein te houden, is ervoor te zorgen dat uw uiteindelijke afbeelding niets bevat dat niet vereist is tijdens runtime. Een manier om dit te doen is met [multi-stage builds][docker-multi-stage-builds]. Multi-stage builds maken het gemakkelijk om ervoor te zorgen dat de uiteindelijke afbeelding alleen de artefacten bevat die u nodig hebt voor uw toepassing, en niet een van de extra inhoud die nodig was tijdens het bouwen.

#### <a name="image-location"></a>Afbeeldingslocatie

Een andere manier om de impact van de afbeeldingstoename op de opstarttijd van uw container te verminderen, is door de containerafbeelding te hosten in [Azure Container Registry](/azure/container-registry/) in dezelfde regio waar u containerexemplaren wilt implementeren. Dit verkort het netwerkpad dat de containerafbeelding moet afleggen, waardoor de downloadtijd aanzienlijk wordt verkort.

#### <a name="cached-images"></a>Afbeeldingen in cache

Azure Container Instances maakt gebruik van een caching-mechanisme om de opstarttijd `servercore:1809`van containers te versnellen voor afbeeldingen die zijn gebouwd op algemene [Windows-basisafbeeldingen](container-instances-faq.md#what-windows-base-os-images-are-supported), waaronder `nanoserver:1809`, `servercore:ltsc2019`en . Veelgebruikte Linux-afbeeldingen `ubuntu:1604` `alpine:3.6` zoals en worden ook in de cache opgeslagen. Gebruik de API Voor een up-to-date lijst met afbeeldingen en tags in de cache gebruikt u de API [Voor afbeeldingen in de cache.][list-cached-images]

> [!NOTE]
> Het gebruik van op Windows Server 2019 gebaseerde afbeeldingen in Azure Container Instances is in preview.Use of Windows Server 2019-based images in Azure Container Instances is in preview.

#### <a name="windows-containers-slow-network-readiness"></a>Windows-containers vertragen netwerkgereedheid

Bij de eerste creatie hebben Windows-containers mogelijk geen inkomende of uitgaande connectiviteit voor maximaal 30 seconden (of langer, in zeldzame gevallen). Als uw containertoepassing een internetverbinding nodig heeft, voegt u vertraging toe en probeert u de logica opnieuw om 30 seconden toe te staan om de internetverbinding tot stand te brengen. Na de eerste installatie moet containernetwerken op de juiste manier worden hervat.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Kan geen verbinding maken met de onderliggende Docker API of bevoegde containers uitvoeren

Azure Container Instances geeft geen directe toegang tot de onderliggende infrastructuur die containergroepen host. Dit omvat toegang tot de Docker-API die wordt uitgevoerd op de host van de container en bevoorrechte containers uitvoert. Als u de interactie met Docker nodig hebt, raadpleegt u de [REST-referentiedocumentatie](https://aka.ms/aci/rest) om te zien wat de ACI-API ondersteunt. Als er iets ontbreekt, dient u een verzoek in op de [ACI feedbackforums.](https://aka.ms/aci/feedback)

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>Ip-adres van containergroep is mogelijk niet toegankelijk vanwege niet-overeenkomende poorten

Azure Container Instances ondersteunt nog geen poorttoewijzing zoals bij reguliere dockerconfiguratie. Als u merkt dat het IP-adres van een containergroep niet toegankelijk is wanneer u denkt dat dit zou moeten zijn, moet u ervoor zorgen dat u uw containerafbeelding hebt geconfigureerd om naar dezelfde poorten te luisteren die u in uw containergroep met de `ports` eigenschap blootstelt.

Als u wilt bevestigen dat Azure Container Instances kunnen luisteren op de poort die `aci-helloworld` u in uw containerafbeelding hebt geconfigureerd, test u een implementatie van de afbeelding die de poort blootlegt. Voer de `aci-helloworld` app ook zo uit dat deze op de poort luistert. `aci-helloworld`accepteert een optionele `PORT` omgevingsvariabele om de standaardpoort 80 te overschrijven waarop deze luistert. Als u bijvoorbeeld poort 9000 wilt testen, stelt u de [omgevingsvariabele](container-instances-environment-variables.md) in wanneer u de containergroep maakt:

1. Stel de containergroep in om poort 9000 bloot te leggen en geef het poortnummer door als de waarde van de omgevingsvariabele. Het voorbeeld is opgemaakt voor de Bash-shell. Als u de voorkeur geeft aan een andere shell, zoals PowerShell of Opdrachtprompt, moet u variabele toewijzing dienovereenkomstig aanpassen.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Zoek het IP-adres van de containergroep in de opdrachtuitvoer van `az container create`. Kijk voor de waarde van **ip**. 
1. Nadat de container is ingericht, bladert u naar het IP-adres en `192.0.2.0:9000`de poort van de container-app in uw browser, bijvoorbeeld: . 

    U ziet de 'Welkom bij Azure Container Instances!' bericht dat wordt weergegeven door de web-app.
1. Wanneer u klaar bent met de container, verwijdert u deze met de `az container delete` opdracht:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [ophalen van containerlogboeken en gebeurtenissen](container-instances-get-logs.md) om uw containers te debuggen.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
