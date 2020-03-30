---
title: Geprivilegieerde containers uitvoeren in een Azure Red Hat OpenShift-cluster | Microsoft Documenten
description: Voer geprivilegieerde containers uit om de beveiliging en naleving te controleren.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, openshift, aquasec, twistlock, rode hoed
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271365"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Bevoorrechte containers uitvoeren in een Azure Red Hat OpenShift-cluster

U willekeurige bevoorrechte containers niet uitvoeren op Azure Red Hat OpenShift-clusters.
Twee beveiligingsmonitoring- en complianceoplossingen mogen worden uitgevoerd op ARO-clusters.
In dit document worden de verschillen beschreven met de algemene OpenShift-implementatiedocumentatie van de leveranciers van beveiligingsproducten.


Lees deze instructies door voordat u de instructies van de leverancier opvolgt.
Sectietitels in productspecifieke stappen hieronder verwijzen rechtstreeks naar sectietitels in de documentatie van de leveranciers.

## <a name="before-you-begin"></a>Voordat u begint

De documentatie van de meeste beveiligingsproducten gaat ervan uit dat u clusterbeheerdersrechten hebt.
Klantbeheerders hebben niet alle bevoegdheden in Azure Red Hat OpenShift. Machtigingen die nodig zijn om clusterbrede bronnen te wijzigen, zijn beperkt.

Zorg er eerst voor dat de gebruiker is aangemeld `oc get scc`bij het cluster als klantbeheerder door . Alle gebruikers die lid zijn van de klantenbeheergroep hebben machtigingen om de Beveiligingscontextbeperkingen (SCS) op het cluster weer te geven.

Zorg er vervolgens `oc` voor `3.11.154`dat de binaire versie .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Productspecifieke stappen voor Aqua Security
De basisinstructies die zullen worden gewijzigd, zijn te vinden in de [aqua security-implementatiedocumentatie.](https://docs.aquasec.com/docs/openshift-red-hat) De stappen hier worden uitgevoerd in combinatie met de Aqua-implementatiedocumentatie.

De eerste stap is het annoteren van de vereiste SCC's die worden bijgewerkt. Deze annotaties voorkomen dat de Sync Pod van het cluster wijzigingen in deze SSCs terugdraait.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Stap 1: Voorwaarden voorbereiden
Vergeet niet om in te loggen op het cluster als een ARO-klantbeheerder in plaats van de cluster-admin rol.

Maak het project en het serviceaccount.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

In plaats van de clusterlezerrol toe te wijzen, wijst u de rol klant-admin-cluster toe aan het aqua-account met de volgende opdracht.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Ga verder met het volgen van de resterende instructies in stap 1.  Deze instructies beschrijven het opzetten van het geheim voor het Aqua-register.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Stap 2: De Aqua Server, Database en Gateway implementeren
Volg de stappen in de Aqua-documentatie voor het installeren van de aqua-console.yaml.

Wijzig de `aqua-console.yaml`opgegeven .  Verwijder de bovenste twee `kind: ClusterRole` objecten `kind: ClusterRoleBinding`met het label en .  Deze bronnen worden niet gemaakt omdat de klantbeheerder op dit `ClusterRole` moment `ClusterRoleBinding` geen toestemming heeft om te wijzigen en objecten.

De tweede wijziging zal `kind: Route` worden `aqua-console.yaml`aan het gedeelte van de . Vervang de volgende yaml voor het `kind: Route` object in het `aqua-console.yaml` bestand.
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

Volg de overige instructies.

### <a name="step-3-login-to-the-aqua-server"></a>Stap 3: Inloggen op de Aqua Server
Deze sectie wordt op geen enkele manier gewijzigd.  Volg de Aqua documentatie.

Gebruik de volgende opdracht om het Aqua Console-adres op te halen.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Stap 4: Zet Aqua Enforcers in
Stel de volgende velden in bij het inzetten van handhavers:

| Veld          | Waarde         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | aqua-account  |
| Project        | aqua-beveiliging |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Productspecifieke stappen voor Prisma Cloud / Twistlock

De basisinstructies die we gaan wijzigen, zijn te vinden in de [implementatiedocumentatie van Prisma Cloud](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Begin met het `twistcli` installeren van de tool zoals beschreven in de secties 'Prisma Cloud installeren' en 'Download de Prisma Cloud-software'.

Een nieuw OpenShift-project maken
```
oc new-project twistlock
```

Sla de optionele sectie 'Push the Prisma Cloud images to a private registry' over. Het zal niet werken op Azure Red Hat Openshift. Gebruik in plaats daarvan het online register.

U de officiële documentatie volgen tijdens het toepassen van de onderstaande correcties.
Begin met de sectie Console installeren.

### <a name="install-console"></a>Console installeren

Tijdens `oc create -f twistlock_console.yaml` stap 2 krijg je een foutmelding bij het maken van de naamruimte.
U het veilig negeren, de naamruimte `oc new-project` is eerder gemaakt met de opdracht.

Gebruik `azure-disk` voor opslagtype.

### <a name="create-an-external-route-to-console"></a>Een externe route naar Console maken

U de documentatie volgen, of de instructies hieronder als u de oc-opdracht verkiest.
De volgende routedefinitie kopiëren naar een bestand met de naam twistlock_route.yaml op uw computer
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
vervolgens uitvoeren:
```
oc create -f twistlock_route.yaml
```

Met deze opdracht u de URL toegewezen krijgen aan de Twistlock-console:`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Console configureren

Volg de Twistlock documentatie.

### <a name="install-defender"></a>Defender installeren

Tijdens `oc create -f defender.yaml` stap 2 krijg je fouten bij het maken van de clusterrol- en clusterrolbinding.
Je ze negeren.

Verdedigers worden alleen geïmplementeerd op compute nodes. U hoeft ze niet te beperken met een knooppuntkiezer.
