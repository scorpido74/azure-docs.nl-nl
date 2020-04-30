---
title: Zelf-hostende Gateway implementeren naar Kubernetes | Microsoft Docs
description: Meer informatie over het implementeren van een zelf-hostend gateway onderdeel van Azure API Management naar Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 7802614540bd5e553fbf1d7a0884ffa2f7433c37
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205103"
---
# <a name="deploy-self-hosted-gateway-to-kubernetes"></a>Zelf-hostende Gateway implementeren op Kubernetes

In dit artikel worden de stappen beschreven voor het implementeren van een zelf-hostend gateway onderdeel van Azure API Management naar een Kubernetes-cluster.

## <a name="prerequisites"></a>Vereisten

- Voer de volgende Snelstartgids uit: [een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
- Maak een Kubernetes-cluster.
> [!TIP]
> [Clusters met één knoop punt](https://kubernetes.io/docs/setup/#learning-environment) werken goed voor ontwikkelings-en evaluatie doeleinden. Gebruik [Kubernetes Certified](https://kubernetes.io/partners/#conformance) multi-node clusters on-premises of in de Cloud voor productie werkbelastingen.
- [Richt een zelf-hostende gateway bron in uw API Management-exemplaar in](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Implementeren naar Kubernetes

1. Selecteer **gateways** onder **implementatie en infra structuur**.
2. Selecteer de zelf-hostende gateway resource die u wilt implementeren.
3. Selecteer **implementatie**.
4. Houd er rekening mee dat een toegangs token in het tekstvak **token** automatisch is gegenereerd voor u met de standaard waarden voor de **verlopende** en **geheime sleutel** . Indien nodig kunt u de gewenste waarden in een of beide besturings elementen kiezen om een nieuw token te genereren.
5. Selecteer het tabblad **Kubernetes** onder **implementatie scripts**.
6. Klik op de **<gateway naam>. yml-** bestand koppeling en down load het yaml-bestand.
7. Selecteer pictogram **kopiëren** in de rechter benedenhoek van het tekstvak **implementeren** om de `kubectl` opdrachten op het klem bord op te slaan.
8. Plak opdrachten in het venster Terminal (of Command). Met de eerste opdracht maakt u een Kubernetes-geheim met een toegangs token dat u in stap 4 hebt gegenereerd. Met de tweede opdracht wordt het configuratie bestand dat in stap 6 is gedownload, toegepast op het Kubernetes-cluster en wordt verwacht dat het bestand aanwezig is in de huidige map.
9. Voer de opdrachten uit om de benodigde Kubernetes-objecten in de [standaard naam ruimte](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) te maken en start zelf-hostende gateway pod ('s) van de [container installatie kopie](https://aka.ms/apim/sputnik/dhub) die is gedownload van de micro soft-container Registry.
10. Voer de opdracht uit die wordt weer gegeven onderstaande om te controleren of de implementatie is geslaagd. Houd er rekening mee dat het enige tijd kan duren voordat alle objecten zijn gemaakt en dat pod (s) moeten worden geïnitialiseerd.
```console
kubectl get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
<gateway-name>   1/1     1            1           18s
```
11. Voer de opdracht uit die wordt weer gegeven onderstaande om te controleren of de service is gemaakt. Houd er rekening mee dat uw service-Ip's en-poorten verschillend zijn.
```console
kubectl get services
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
<gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
```
12. Ga terug naar de Azure Portal en selecteer **overzicht**.
13. **Status** met een groen vinkje, gevolgd door het aantal in het yaml-bestand gevonden replica's, wordt bevestigd dat geïmplementeerde zelf-hostende gateways met succes communiceren met de API Management-service en een regel ' heartbeat ' hebben.

![Gateway status](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Opdracht <code>kubectl logs deployment/<gateway-name></code> uitvoeren om logboeken van een wille keurig geselecteerd pod weer te geven als er meer dan één.
> Voer <code>kubectl logs -h</code> uit voor een volledige set opdracht Opties, bijvoorbeeld voor het weer geven van Logboeken voor een specifieke Pod of container.

## <a name="production-deployment-considerations"></a>Overwegingen voor productie-implementatie

### <a name="access-token"></a>Toegangs token
Zonder een geldige zelf-hostende gateway voor het toegangs token is geen toegang tot en downloaden van de configuratie van het eind punt van de configuratie gegevens van de bijbehorende API Management service. Het toegangs token kan Maxi maal 30 dagen geldig zijn. Het moet opnieuw worden gegenereerd en het cluster is met een nieuw token hand matig of via Automation geconfigureerd voordat het verloopt. Bij het automatiseren van token vernieuwing gebruikt u deze beheer API- [bewerking](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken) om een nieuw token te genereren. Volg deze [koppeling](https://kubernetes.io/docs/concepts/configuration/secret) voor meer informatie over het beheren van Kubernetes-geheimen.

### <a name="namespace"></a>Naamruimte
Kubernetes- [naam ruimten](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) helpen bij het delen van één cluster over meerdere teams, projecten of toepassingen. Naam ruimten bieden een bereik voor resources en namen en kunnen worden gekoppeld aan een resource quota en toegangs beheer beleid.
Opdrachten die in de Azure Portal zelf-hostende gateway resources maken in de **standaard** naam ruimte die automatisch wordt gemaakt, bestaan in elk cluster en kunnen niet worden verwijderd.
Overweeg [het maken en implementeren](https://kubernetesbyexample.com/ns/) van zelf-hostende gateway in een afzonderlijke naam ruimte in de productie omgeving.

### <a name="number-of-replicas"></a>Aantal replica's
Mini maal aantal replica's dat geschikt is voor productie is twee.

Een zelf-hostende gateway wordt standaard geïmplementeerd met een **RollingUpdate** -implementatie [strategie](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy). Bekijk de standaard waarden en overweeg expliciet de velden [**maxUnavailable**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) en [**maxSurge**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) in te stellen, met name bij het gebruik van een hoog aantal replica's.

### <a name="container-resources"></a>Container bronnen
Standaard worden in het YAML-bestand in de Azure Portal geen container resource aanvragen vermeld.

Het is niet mogelijk om op betrouw bare wijze te voors pellen en de hoeveelheid CPU-en geheugen bronnen per container en het aantal benodigde replica's voor de ondersteuning van een specifieke werk belasting te raden, bijvoorbeeld:

- Specifieke hardware waarop het cluster wordt uitgevoerd
- Aanwezigheid en type virtualisatie
- Aantal en de frequentie van gelijktijdige client verbindingen
- Aanvraag frequentie
- Type en aantal geconfigureerde beleids regels
- Payload-grootte en als payloads worden gebufferd of gestreamd
- Latentie van back-end-service

U wordt aangeraden om een resource aanvraag in te stellen op twee kernen en 2 GiB als uitgangs punt, een belasting test uit te voeren en omhoog/omlaag te schalen of neer te zetten op basis van de resultaten.

### <a name="container-image-tag"></a>Tag container installatie kopie
Het YAML-bestand in de Azure Portal maakt gebruik van de **meest** recente code die altijd verwijst naar de meest recente versie van de installatie kopie van de zelf-hostende gateway container.

Overweeg het gebruik van een specifieke versie code in de productie om onbedoelde upgrade naar een nieuwere versie te voor komen.

Volg deze [koppeling](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list) om de volledige lijst met beschik bare labels weer te geven.

### <a name="dns-policy"></a>DNS-beleid
DNS-naam omzetting speelt een cruciale rol in de mogelijkheid van een zelf-hostende gateway om verbinding te maken met afhankelijkheden in Azure-en Dispatch API-aanroepen naar back-end-services.

Het YAML-bestand in de Azure Portal past het standaard [**ClusterFirst**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) -beleid toe, waardoor naam omzettings aanvragen die niet worden opgelost door de cluster-DNS, worden doorgestuurd naar de upstream-DNS-server die is overgenomen van het knoop punt.

Volg deze [koppeling](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service) voor meer informatie over naam omzetting in Kubernetes en overweeg om [DNS-beleid](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) of D[NS-configuratie](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) zo nodig aan te passen voor uw installatie.

### <a name="configuration-backup"></a>Configuratie back-up
Volg deze [koppeling](self-hosted-gateway-overview.md#connectivity-to-azure) voor meer informatie over zelf-hostende gateway gedrag in de aanwezigheid van een tijdelijke Azure-verbindings onderbreking.
Configureer het lokale opslag volume voor de zelf-hostende gateway container, zodat er een back-up kan worden gemaakt van de meest recente gedownloade configuratie. als de verbinding is verbroken, kunt u deze gebruiken na het opnieuw opstarten. Het pad naar het volume koppelt moet zijn <code>/apim/config</code>. Bekijk [hier](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml)een voor beeld.
Volg deze [koppeling](https://kubernetes.io/docs/concepts/storage/volumes/)voor meer informatie over opslag in Kubernetes.

### <a name="local-logs-and-metrics"></a>Lokale logboeken en metrische gegevens
Zelf-hostende Gateway verzendt telemetrie naar [Azure monitor](api-management-howto-use-azure-monitor.md) en [Azure-toepassing inzichten](api-management-howto-app-insights.md) per configuratie-instellingen in de bijbehorende API Management service.
Wanneer de [verbinding met Azure](self-hosted-gateway-overview.md#connectivity-to-azure) tijdelijk is verbroken, wordt de stroom van telemetrie naar Azure onderbroken en worden de gegevens voor de duur van de onderbreking verloren gegaan.
Overweeg om [lokale bewaking](how-to-configure-local-metrics-logs.md) in te stellen om te zorgen voor de mogelijkheid om het API-verkeer te observeren en te voor komen dat telemetrie verloren gaat tijdens Azure-connectiviteit.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de zelf-hostende Gateway vindt u in [Azure API Management zelf-hostende gateway-overzicht](self-hosted-gateway-overview.md)