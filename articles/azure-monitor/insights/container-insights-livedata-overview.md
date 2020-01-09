---
title: Live data (preview) weer geven met Azure Monitor voor containers | Microsoft Docs
description: In dit artikel wordt een overzicht gegeven van de real-time weer gave van Kubernetes-logboeken, gebeurtenissen en pod-metrische gegevens zonder gebruik te maken van kubectl in Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 18ae091a32d0256288d27ad1439ffc7be26db5f1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404756"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Kubernetes-logboeken, gebeurtenissen en metrische gegevens over pod in realtime weer geven

Azure Monitor voor containers bevat de functie Live data (preview). Dit is een geavanceerde diagnostische functie waarmee u toegang krijgt tot de AKS-container Logboeken (Kubernetes), gebeurtenissen en pod-metrische gegevens van de Azure-service. Het biedt directe toegang tot `kubectl logs -c`, `kubectl get`-gebeurtenissen en `kubectl top pods`. In een console venster worden de logboeken, gebeurtenissen en metrische gegevens weer gegeven die door de container-Engine zijn gegenereerd voor verdere hulp bij het oplossen van problemen in realtime.

Dit artikel bevat een gedetailleerd overzicht en helpt u inzicht te krijgen in het gebruik van deze functie. 

>[!NOTE]
>AKS-clusters die zijn ingeschakeld als [persoonlijke clusters](https://azure.microsoft.com/updates/aks-private-cluster/) , worden niet ondersteund met deze functie. Deze functie is afhankelijk van het rechtstreeks openen van de Kubernetes-API via een proxy server vanuit uw browser. Door netwerk beveiliging in te scha kelen, wordt dit verkeer geblokkeerd door de Kubernetes-API van deze proxy te blok keren. 

>[!NOTE]
>Deze functie is beschikbaar in alle Azure-regio's, inclusief Azure China. Het is momenteel niet beschikbaar in de Amerikaanse overheid van Azure.

Lees onze [installatie handleiding](container-insights-livedata-setup.md)voor hulp bij het instellen of oplossen van problemen met de functie van live data (preview). Deze functie heeft rechtstreeks toegang tot de Kubernetes-API en aanvullende informatie over het verificatie model vindt u [hier](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

## <a name="live-data-preview-functionality-overview"></a>Overzicht van de functionaliteit van live data (preview)

### <a name="search"></a>Search

![Filter voorbeeld van live data console-deel venster](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

De functie voor Live gegevens (preview) omvat zoek functionaliteit. In het **Zoek** veld kunt u de resultaten filteren door een sleutel woord of-term te typen en alle overeenkomende resultaten zijn gemarkeerd om snelle controle toe te staan. Tijdens het weer geven van gebeurtenissen kunt u de resultaten ook beperken met behulp van de **filter** Pill rechts van de zoek balk. Afhankelijk van de resource die u hebt geselecteerd, wordt in de Pill een Pod, naam ruimte of cluster weer gegeven waaruit u kunt kiezen.  

### <a name="scroll-lock-and-pause"></a>Scroll Lock en PAUSE 

Als u de functie voor automatisch schuiven wilt onderbreken en het gedrag van het deel venster wilt beheren, kunt u hand matig door de nieuwe gegevens lezen door de **Schuif** optie te gebruiken. Als u AutoScroll opnieuw wilt inschakelen, selecteert u de **Schuif** optie opnieuw. U kunt het ophalen van logboek-of gebeurtenis gegevens ook onderbreken door de optie **pause** te selecteren en wanneer u klaar bent om verder te gaan, selecteert u **afspelen**.  

![Live data-console venster Live-weer gave onderbreken](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>We raden u aan om tijdens korte tijd alleen een onderbreking in te stellen of te onderbreken tijdens het oplossen van een probleem. Deze aanvragen kunnen van invloed zijn op de beschik baarheid en beperking van de Kubernetes-API op uw cluster. 

>[!IMPORTANT]
>Er worden geen gegevens permanent opgeslagen tijdens de werking van deze functie. Alle gegevens die tijdens de sessie zijn vastgelegd, worden verwijderd wanneer u de browser sluit of verlaat. Gegevens blijven alleen aanwezig voor visualisatie in het venster over vijf minuten van de functie meet waarden. alle metrische gegevens die ouder zijn dan vijf minuten, worden ook verwijderd. De Live-gegevens (preview)-buffer query's binnen de redelijke limieten voor geheugen gebruik (moet hier specifiekere zijn, wat redelijk is?). 

## <a name="view-logs"></a>Logboeken weergeven

U kunt real-time logboek gegevens weer geven wanneer deze worden gegenereerd door de container Engine vanuit de weer gave **knoop punten**, **controllers**en **containers** . Voer de volgende stappen uit om de logboek gegevens weer te geven.

1. Blader in het Azure Portal naar de cluster resource groep AKS en selecteer uw AKS-resource.

2. Kies op het AKS-cluster dashboard onder **bewaking** aan de linkerkant de optie **inzichten**. 

3. Selecteer het tabblad **knoop punten**, **controllers**of **containers** .

4. Selecteer een object in het prestatie raster en selecteer in het deel venster Eigenschappen aan de rechter kant de optie **Live gegevens weer geven (preview)** . Als het AKS-cluster met behulp van Azure AD is geconfigureerd voor eenmalige aanmelding, wordt u gevraagd om te verifiëren bij het eerste gebruik tijdens die browser sessie. Selecteer uw account en de volledige verificatie met Azure.  

    >[!NOTE]
    >Wanneer u de gegevens in uw Log Analytics-werk ruimte bekijkt door de optie **weer geven in analyse** te selecteren in het deel venster Eigenschappen, worden in de zoek resultaten van het logboek mogelijk **knoop punten**, **daemon-sets**, **replica sets**, **taken**, **cron**, **peul**en **containers** weer gegeven die mogelijk niet meer bestaan. Het zoeken naar Logboeken voor een container die niet beschikbaar is in `kubectl`, zal hier ook mislukken. Bekijk de [weer gave in de analyse](container-insights-log-search.md#search-logs-to-analyze-data) functie voor meer informatie over het weer geven van historische logboeken, gebeurtenissen en metrische gegevens.  

Nadat de verificatie is voltooid, wordt het console venster voor Live gegevens (preview-versie) weer gegeven onder het raster voor prestatie gegevens waarin u logboek gegevens in een doorlopende stroom kunt weer geven. Als de status indicator ophalen een groen vinkje bevat dat helemaal rechts in het deel venster staat, betekent dit dat de gegevens kunnen worden opgehaald en streamen naar uw-console.  

![Deel venster Eigenschappen van knoop punt weergave gegevens optie](./media/container-insights-livedata-overview/node-properties-pane.png)  

De titel van het deel venster toont de naam van de pod waarin de container is gegroepeerd.

## <a name="view-events"></a>Evenementen bekijken

U kunt real-time gebeurtenis gegevens weer geven wanneer deze worden gegenereerd door de container Engine vanuit de weer gave **knoop punten**, **controllers**, **containers**en **implementaties (preview)** wanneer een container, Pod, knoop punt, replicaset, daemonset, taak, CronJob of implementatie is geselecteerd. Voer de volgende stappen uit om gebeurtenissen weer te geven.

1. Blader in het Azure Portal naar de cluster resource groep AKS en selecteer uw AKS-resource.

2. Kies op het AKS-cluster dashboard onder **bewaking** aan de linkerkant de optie **inzichten**. 

3. Selecteer het tabblad **knoop punten**, **controllers**, **containers**of **implementaties (voor beeld)** .

4. Selecteer een object in het prestatie raster en selecteer in het deel venster Eigenschappen aan de rechter kant de optie **Live gegevens weer geven (preview)** . Als het AKS-cluster met behulp van Azure AD is geconfigureerd voor eenmalige aanmelding, wordt u gevraagd om te verifiëren bij het eerste gebruik tijdens die browser sessie. Selecteer uw account en de volledige verificatie met Azure.  

    >[!NOTE]
    >Wanneer u de gegevens in uw Log Analytics-werk ruimte bekijkt door de optie **weer geven in analyse** te selecteren in het deel venster Eigenschappen, worden in de zoek resultaten van het logboek mogelijk **knoop punten**, **daemon-sets**, **replica sets**, **taken**, **cron**, **peul**en **containers** weer gegeven die mogelijk niet meer bestaan. Het zoeken naar Logboeken voor een container die niet beschikbaar is in `kubectl`, zal hier ook mislukken. Bekijk de [weer gave in de analyse](container-insights-log-search.md#search-logs-to-analyze-data) functie voor meer informatie over het weer geven van historische logboeken, gebeurtenissen en metrische gegevens.  

Nadat de verificatie is voltooid, wordt het console venster voor Live gegevens (preview-versie) onder het raster prestatie gegevens weer gegeven. Als de status indicator ophalen een groen vinkje bevat dat helemaal rechts in het deel venster staat, betekent dit dat de gegevens kunnen worden opgehaald en streamen naar uw-console. 
    
Als het object dat u hebt geselecteerd een container is, selecteert u de optie **gebeurtenissen** in het deel venster. Als u een knoop punt, Pod of controller hebt geselecteerd, wordt het weer geven van gebeurtenissen automatisch geselecteerd. 

    ![Controller properties pane view events](./media/container-insights-livedata-overview/controller-properties-live-events.png)  

De titel van het deel venster toont de naam van de pod waarin de container is gegroepeerd.

### <a name="filter-events"></a>Gebeurtenissen filteren 

Tijdens het weer geven van gebeurtenissen kunt u de resultaten ook beperken met behulp van de **filter** Pill rechts van de zoek balk. Afhankelijk van de resource die u hebt geselecteerd, wordt in de Pill een Pod, naam ruimte of cluster weer gegeven waaruit u kunt kiezen.  

## <a name="view-metrics"></a>Metrische gegevens bekijken 

U kunt metrische gegevens in realtime weer geven wanneer deze worden gegenereerd door de container-Engine vanuit de weer gave **knoop punten** of **controllers** als er een **pod** is geselecteerd. Voer de volgende stappen uit om de metrische gegevens weer te geven.

1. Blader in het Azure Portal naar de cluster resource groep AKS en selecteer uw AKS-resource.

2. Kies op het AKS-cluster dashboard onder **bewaking** aan de linkerkant de optie **inzichten**. 

3. Selecteer de **knoop punten** of het tabblad **controllers** .

4. Selecteer een **pod** -object in het prestatie raster en selecteer in het deel venster Eigenschappen aan de rechter kant de optie **Live data weer geven (preview)** . Als het AKS-cluster met behulp van Azure AD is geconfigureerd voor eenmalige aanmelding, wordt u gevraagd om te verifiëren bij het eerste gebruik tijdens die browser sessie. Selecteer uw account en de volledige verificatie met Azure.  

    >[!NOTE]
    >Wanneer u de gegevens in uw Log Analytics-werk ruimte bekijkt door de optie **weer geven in analyse** te selecteren in het deel venster Eigenschappen, worden in de zoek resultaten van het logboek mogelijk **knoop punten**, **daemon-sets**, **replica sets**, **taken**, **cron**, **peul**en **containers** weer gegeven die mogelijk niet meer bestaan. Het zoeken naar Logboeken voor een container die niet beschikbaar is in `kubectl`, zal hier ook mislukken. Bekijk de [weer gave in de analyse](container-insights-log-search.md#search-logs-to-analyze-data) functie voor meer informatie over het weer geven van historische logboeken, gebeurtenissen en metrische gegevens.  

Nadat de verificatie is voltooid, wordt het console venster voor Live gegevens (preview-versie) onder het raster prestatie gegevens weer gegeven. Metrische gegevens worden opgehaald en streamen naar uw-console, zodat deze in de twee grafieken worden gepresenteerd. De titel van het deel venster toont de naam van de pod waarin de container is gegroepeerd.

![Voor beeld van metrische pod-gegevens weer geven](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>Volgende stappen

- Om door te gaan met het leren over het gebruik van Azure Monitor en controleren van andere aspecten van uw AKS-cluster [weergave Azure Kubernetes Service health](container-insights-analyze.md).

- Bekijk de [voor beelden van logboek query's](container-insights-log-search.md#search-logs-to-analyze-data) om vooraf gedefinieerde query's en voor beelden te bekijken om waarschuwingen, visualisaties of verdere analyse van uw clusters te maken.
