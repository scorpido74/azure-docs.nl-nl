---
title: Bevoegde containers uitvoeren in een Azure Red Hat open Shift-cluster | Microsoft Docs
description: Voer geprivilegieerde containers uit om de beveiliging en naleving te controleren.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: Aro, open Shift, aquasec, twistlock, Red Hat
ms.openlocfilehash: 4241296a991283f14fbb294fdc059ecde58d6d75
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069660"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Bevoegde containers uitvoeren in een Azure Red Hat open Shift-cluster

U kunt geen wille keurige, geprivilegieerde containers uitvoeren op Azure Red Hat open Shift-clusters.
Twee beveiligings-en compatibiliteits oplossingen mogen worden uitgevoerd op ARO-clusters.
In dit document worden de verschillen beschreven van de algemene open Shift-implementatie documentatie van de leveranciers van het beveiligings product.


Lees deze instructies door voordat u de instructies van de leverancier volgt.
Sectie titels in productspecifieke stappen hieronder verwijzen rechtstreeks naar sectie titels in de documentatie van de leveranciers.

## <a name="before-you-begin"></a>Voordat u begint

De documentatie van de meeste beveiligings producten veronderstelt dat u cluster beheerders bevoegdheden hebt.
Klant beheerders hebben niet alle bevoegdheden in azure Red Hat open SHIFT. De vereiste machtigingen voor het wijzigen van de cluster bronnen zijn beperkt.

Controleer eerst of de gebruiker is aangemeld bij het cluster als klant beheerder door `oc get scc`uit te voeren. Alle gebruikers die lid zijn van de groep klant beheerders, hebben machtigingen voor het weer geven van de beveiligings context beperkingen (SCCs) op het cluster.

Controleer vervolgens of de `oc` binaire versie `3.11.154`is.
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Productspecifieke stappen voor de veiligheid van het licht blauw
De basis instructies die u kunt wijzigen, vindt u in de [documentatie over de beveiligings implementatie](https://docs.aquasec.com/docs/openshift-red-hat)van het licht blauw. De stappen die hier worden uitgevoerd, worden in combi natie met de onderhanden implementatie documentatie voor het licht.

De eerste stap is om aantekeningen te maken op de vereiste SCCs die wordt bijgewerkt. Met deze annotaties wordt voor komen dat de synchronisatie van het cluster pod de wijzigingen in deze SSCs herstelt.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Stap 1: vereisten voorbereiden
Vergeet niet om u aan te melden bij het cluster als een ARO-klant beheerder in plaats van de rol cluster beheerder.

Maak het project en het service account.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Wijs, in plaats van de rol cluster lezer toe te wijzen, de rol klant-beheerder-cluster toe aan de zeeblauw-account met de volgende opdracht.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Volg de resterende instructies in stap 1.  In deze instructies wordt beschreven hoe u het geheim instelt voor het onderhanden register.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Stap 2: de blauwe server, data base en gateway implementeren
Volg de stappen in de onderhouds documentatie voor het installeren van de YAML.

Wijzig de gegeven `aqua-console.yaml`.  Verwijder de bovenste twee objecten met het label `kind: ClusterRole` en `kind: ClusterRoleBinding`.  Deze resources worden niet gemaakt, omdat de beheerder van de klant op dit moment geen toestemming heeft om `ClusterRole` en `ClusterRoleBinding` objecten te wijzigen.

De tweede wijziging is het `kind: Route` gedeelte van de `aqua-console.yaml`. Vervang de volgende YAML voor het object `kind: Route` in het `aqua-console.yaml` bestand.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Volg de resterende instructies.

### <a name="step-3-login-to-the-aqua-server"></a>Stap 3: aanmelden bij de zeeblauwe-server
Deze sectie is op geen enkele manier gewijzigd.  Volg de documentatie bij het licht blauw.

Gebruik de volgende opdracht om het adres van de zeeblauw-console op te halen.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Stap 4: blauwe afdwingers implementeren
Stel de volgende velden in wanneer u afdwingers implementeert:

| Veld          | Waarde         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | licht blauw  |
| Project        | licht blauw: beveiliging |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Productspecifieke stappen voor prisma Cloud/Twistlock

De basis instructies die u kunt wijzigen, zijn te vinden in de [prisma-documentatie over Cloud implementatie](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Begin met het maken van een nieuw open Shift-project
```
oc new-project twistlock
```

U kunt de documentatie volgen tot de sectie ' console installeren ', het prisma-Cloud container register gebruiken in plaats van een intern bestand te maken.

### <a name="install-console"></a>Console installeren

Tijdens `oc create -f twistlock_console.yaml` in stap 2 krijgt u een fout melding bij het maken van de naam ruimte.
U kunt deze gewoon negeren, de naam ruimte is eerder gemaakt met de opdracht `oc new-project`.

### <a name="create-an-external-route-to-console"></a>Een externe route naar de console maken

U kunt de documentatie volgen of de onderstaande instructies als u de voor keur geeft aan de opdracht OC.
Kopieer de volgende route definitie naar een bestand met de naam twistlock_route. yaml op uw computer
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
Voer vervolgens de volgende handelingen uit:
```
oc create -f twistlock_route.yaml
```

U kunt de URL die is toegewezen aan de Twistlock-console ophalen met de volgende opdracht: `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Console configureren

Volg de Twistlock-documentatie.

### <a name="install-defender"></a>Defender installeren

Tijdens `oc create -f defender.yaml` in stap 2 krijgt u fouten bij het maken van de cluster functie en de binding van de cluster functie.
U kunt deze negeren.

Verdedigen worden alleen geïmplementeerd op reken knooppunten. U hoeft deze niet te beperken met een knooppunt kiezer.
