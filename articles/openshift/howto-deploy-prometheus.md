---
title: Een zelfstandig Prometheus-exemplaar implementeren in een Azure Red Hat OpenShift-cluster | Microsoft Documenten
description: Maak een Prometheus-exemplaar in een Azure Red Hat OpenShift-cluster om de statistieken van uw toepassing te controleren.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, metrics, red hat
ms.openlocfilehash: f81a993caa31578e689fb3a90108f3cf0ca81fc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69875125"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Een zelfstandig Prometheus-exemplaar implementeren in een Azure Red Hat OpenShift-cluster

In dit artikel wordt beschreven hoe u een zelfstandige Prometheus-instantie configureert die servicedetectie gebruikt in een Azure Red Hat OpenShift-cluster.

> [!NOTE]
> Toegang tot Azure Red Hat OpenShift is niet vereist.

Doelinstelling:

- Eén project (prometheus-project), dat Prometheus en Alertmanager bevat.
- Twee projecten (app-project1 en app-project2), die de te controleren toepassingen bevatten.

Je bereidt lokaal wat Prometheus config-bestanden voor. Maak een nieuwe map om ze op te slaan. Config-bestanden worden in het cluster opgeslagen als geheimen, voor het geval er later geheime tokens aan het cluster worden toegevoegd.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Aanmelden bij het cluster met het gereedschap OC

1. Open een webbrowser en ga naar de webconsolehttps://openshiftvan uw cluster ( .* random-id*. *regio*.azmosa.io).
2. Meld u aan met uw Azure-referenties.
3. Selecteer uw gebruikersnaam in de rechterbovenhoek en selecteer **Vervolgens Aanmeldingsopdracht kopiëren**.
4. Plak uw gebruikersnaam in de terminal die u zult gebruiken.

> [!NOTE]
> Voer de `oc whoami -c` opdracht uit om te zien of u bent aangemeld bij het juiste cluster.

## <a name="prepare-the-projects"></a>Bereid de projecten voor

Voer de volgende opdrachten uit om de projecten te maken:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> U de `-n` `--namespace` parameter of parameter gebruiken of `oc project` een actief project selecteren door de opdracht uit te voeren.

## <a name="prepare-the-prometheus-configuration-file"></a>Het configuratiebestand van Prometheus voorbereiden
Maak een prometheus.yml-bestand door de volgende inhoud in te voeren:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Maak een geheim genaamd Prom door de volgende configuratie in te voeren:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Het prometheus.yml-bestand is een basisconfiguratiebestand van Prometheus. Het stelt de intervallen in en configureert automatische detectie in drie projecten (prometheus-project, app-project1, app-project2). In het vorige configuratiebestand worden de automatisch gedetecteerde eindpunten over HTTP geschraapt zonder verificatie.

Voor meer informatie over het schrapen van eindpunten, zie [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Het config-bestand Alertmanager voorbereiden
Maak een alertmanager.yml-bestand door de volgende inhoud in te voeren:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Maak een geheim genaamd Prom-Alerts door de volgende configuratie in te voeren:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml is het configuratiebestand Alert Manager.

> [!NOTE]
> Voer de `oc get secret -n prometheus-project` opdracht uit om de twee vorige stappen te verifiëren.

## <a name="start-prometheus-and-alertmanager"></a>Start Prometheus en Alertmanager
Ga naar [openshift/origin repository](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) en download de [prometheus-standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) template. Pas de sjabloon toe op prometheus-project door de volgende configuratie in te voeren:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Het prometheus-standalone.yaml-bestand is een OpenShift-sjabloon. Het zal een Prometheus instantie met oauth-proxy voor het en een Alertmanager instantie, ook beveiligd met oauth-proxy. In deze sjabloon is oauth-proxy zo geconfigureerd dat elke gebruiker die de naamruimte `-openshift-sar` van prometheus-project kan 'krijgen' (zie de vlag).

> [!NOTE]
> Voer de `oc get statefulset -n prometheus-project` opdracht uit om te controleren of de prom StatefulSet dezelfde gewenste en huidige getalreplica's heeft. Voer de `oc get all -n prometheus-project` opdracht uit om alle resources in het project te controleren.

## <a name="add-permissions-to-allow-service-discovery"></a>Machtigingen toevoegen om servicedetectie toe te staan

Maak een prometheus-sdrole.yml-bestand door de volgende inhoud in te voeren:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Voer de volgende opdrachten uit om de sjabloon toe te passen op alle projecten waarvan u servicedetectie wilt toestaan:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Voer de `oc get role` opdrachten en `oc get rolebinding` opdrachten uit om te controleren of Rol- en Rolbinding correct zijn gemaakt.

## <a name="optional-deploy-example-application"></a>Optioneel: voorbeeldtoepassing implementeren

Alles werkt, maar er zijn geen statistieken bronnen. Ga naar de URLhttps://prom-prometheus-project.appsvan Prometheus ( .* random-id*. *regio*.azmosa.io/). U het vinden met behulp van volgende opdracht:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Vergeet niet om het https:// voorvoegsel toe te voegen aan het begin van de hostnaam.

Op de pagina **Status > Service Discovery** worden 0/0 actieve doelen weergegeven.

Voer de volgende opdrachten uit om een voorbeeldtoepassing te implementeren, waarmee basis-Python-statistieken worden weergegeven onder het eindpunt /metrics:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
De nieuwe toepassingen moeten binnen 30 seconden na de implementatie worden weergegeven als geldige doelen op de pagina Servicedetectie.

Selecteer > **Statusdoelen** **Status**voor meer informatie .

> [!NOTE]
> Voor elk met succes geschraapt doel voegt Prometheus een gegevenspunt toe in de up metric. Selecteer **Prometheus** in de linkerbovenhoek, voer de expressie **in** en selecteer **Uitvoeren**.

## <a name="next-steps"></a>Volgende stappen

U aangepaste Prometheus-instrumentatie toevoegen aan uw toepassingen. De Prometheus-clientbibliotheek, die de voorbereiding van Prometheus-statistieken vereenvoudigt, is klaar voor verschillende programmeertalen.

Zie de volgende GitHub-bibliotheken voor meer informatie:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [OK](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
