---
title: (AFGESCHAFT) Concept gebruiken met Azure Container Service en Azure Container Registry
description: Maak een ACS Kubernetes-cluster en een Azure Container Registry om uw eerste toepassing met Draft te maken in Azure.
author: squillace
ms.service: container-service
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 8d688d2918c9100019d033e93e9a3dca9e492de2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271137"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>(AFGESCHAFT) Concept gebruiken met Azure Container Service en Azure Container Registry om een toepassing te bouwen en te implementeren naar Kubernetes

> [!TIP]
> Zie [Concept gebruiken met Azure Kubernetes Service (AKS)](../../aks/kubernetes-draft.md)voor de bijgewerkte versie van dit artikel die Azure Kubernetes Service gebruikt.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Draft](https://aka.ms/draft) is een nieuw open-source hulpprogramma waarmee u eenvoudig containertoepassingen kunt ontwikkelen en ze kunt implementeren in Kubernetes-clusters. U hoeft hiervoor niet veel te weten over Docker en Kubernetes (u hoeft ze zelfs niet eens te installeren!). Als u een hulpprogramma als Draft gebruikt, kunnen u en uw team zich richten op het bouwen van de toepassing met Kubernetes en hoeft er minder aandacht te worden besteed aan de infrastructuur.

U kunt Draft gebruiken met alle Docker-installatiekopieregisters en alle Kubernetes-clusters, ook de lokale. In deze zelfstudie wordt uitgelegd hoe u ACS met Kubernetes en ACR gebruiken om een live maar veilige ontwikkelaarspijplijn in Kubernetes te maken met behulp van Draft en hoe u Azure DNS gebruiken om die ontwikkelaarspijplijn bloot te leggen die anderen op een domein kunnen zien.


## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken
U kunt eenvoudig [een nieuw Azure Container Registry maken](../../container-registry/container-registry-get-started-azure-cli.md). De stappen daarvoor zijn als volgt:

1. Maak een Azure-brongroep om uw ACR-register en het Kubernetes-cluster in ACS te beheren.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Maak een ACR-afbeeldingregister met behulp van `--admin-enabled` [az acr maken](/cli/azure/acr#az-acr-create) en ervoor te zorgen dat de optie is ingesteld op `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Een Azure Container Service maken met Kubernetes

U bent er nu klaar voor om [az acs create](/cli/azure/acs#az-acs-create) te gebruiken voor het maken van een ACS-cluster met Kubernetes als de `--orchestrator-type`-waarde.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
```

> [!NOTE]
> Kubernetes is niet het standaardorchestratortype, dus gebruik de `--orchestrator-type kubernetes`-switch.

Wanneer dit is gelukt, ziet de uitvoer er als volgt uit.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

Wanneer u een cluster hebt, kunt u de referenties importeren met de opdracht [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes). U hebt nu een lokaal configuratiebestand voor uw cluster. Dit bestand hebben Helm en Draft nodig om werk te verrichten.

## <a name="install-and-configure-draft"></a>Draft installeren en configureren


1. Download concept voor https://github.com/Azure/draft/releases uw omgeving op en installeer in uw PATH, zodat de opdracht kan worden gebruikt.
2. Download het roer https://github.com/kubernetes/helm/releases voor uw omgeving en [installeer deze in uw PATH, zodat de opdracht kan worden gebruikt.](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client)
3. Configureer Draft om uw eigen register te gebruiken en om subdomeinen te maken voor elk Helm-diagram dat wordt gemaakt. U hebt het volgende nodig voor het configureren van Draft:
   - de naam van uw Azure Container Registry (in dit voorbeeld is dat `draftacsdemo`)
   - de registersleutel of het wachtwoord van `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.

   Oproep `draft init` en het configuratieproces vraagt u om de bovenstaande waarden; Houd er rekening mee dat de URL-indeling voor `draftacsdemo`de `.azurecr.io`register-URL de registernaam is (in dit voorbeeld) plus . Uw gebruikersnaam is de naam van het register op zijn eigen. De eerste keer dat u het proces uitvoert, ziet het er ongeveer als volgt uit.
   ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
   ```

U kunt nu een toepassing implementeren.


## <a name="build-and-deploy-an-application"></a>Een toepassing bouwen en implementeren

In de Draft-opslagplaats staan [zes eenvoudige voorbeeldtoepassingen](https://github.com/Azure/draft/tree/master/examples). Kloon de repo en laten we gebruik maken van de [Java voorbeeld](https://github.com/Azure/draft/tree/master/examples/example-java). Wijzig in de voorbeeld-/java-map en typ `draft create` om de toepassing te bouwen. De toepassing zou er als volgt moeten uitzien.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

De uitvoer bevat een Dockerfile en een Helm-diagram. Typ `draft up` om te bouwen en implementeren. De output is uitgebreid, maar moet worden als het volgende voorbeeld.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Uw toepassing veilig bekijken

Uw container wordt nu uitgevoerd in ACS. Als u deze `draft connect` wilt bekijken, gebruikt u de opdracht, waarmee een beveiligde verbinding wordt gemaakt met het IP-cluster met een specifieke poort voor uw toepassing, zodat u deze lokaal bekijken. Als dit lukt, zoekt u naar de URL om verbinding te maken met uw app op de eerste regel na de **SUCCES-indicator.**

> [!NOTE]
> Als u een bericht ontvangt dat er geen pods klaar waren, wacht dan even `kubectl get pods -w` en probeert het opnieuw, of u de pods klaar zien maken en vervolgens opnieuw proberen wanneer ze dat doen.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

In het voorgaande voorbeeld `curl -s http://localhost:46143` kunt u typen `Hello World, I'm Java!`om het antwoord te ontvangen. Wanneer u CTRL+ of CMD+C (afhankelijk van uw OS-omgeving) hebt, wordt de beveiligde tunnel afgebroken en u verder rijden.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Uw toepassing delen door een implementatiedomein te configureren met Azure DNS

U hebt de iteratielus voor ontwikkelaars die Concept in de voorgaande stappen maakt, al uitgevoerd. U uw toepassing echter delen via het internet door:
1. Een binnendringen in uw ACS-cluster installeren (om een openbaar IP-adres op te geven waarop de app moet worden weergegeven)
2. Uw aangepaste domein delegeren naar Azure DNS en uw domein toewijzen aan het IP-adres dat ACS aan uw ingevallen controller toewijst

### <a name="use-helm-to-install-the-ingress-controller"></a>Gebruik het roer om de invallende controller te installeren.
Gebruik **het roer** om `stable/traefik`te zoeken naar en te installeren , een invallende controller, om inkomende aanvragen voor uw builds in te schakelen.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Stel nu een controle in voor de `ingress`-controller om de externe IP-waarde vast te leggen bij de implementatie. Dit IP-adres wordt toegewezen aan het implementatiedomein in de volgende sectie.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

In dit geval is het externe IP-adres voor het implementatiedomein `13.64.108.240`. U kunt uw domein nu toewijzen aan dat IP-adres.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Het invallende IP toewijzen aan een aangepast subdomein

Draft maakt een release voor elke Helm-grafiek die er wordt gemaakt en elke toepassing waar u aan werkt. Elk wordt een gegenereerde naam die door **concept** wordt gebruikt als _subdomein_ bovenop het _hoofdimplementatiedomein_ dat u beheert. (In dit voorbeeld `squillace.io` gebruiken we als implementatiedomein.) Als u dit subdomeingedrag wilt inschakelen, `'*.draft'` moet u een A-record maken voor in uw DNS-vermeldingen voor uw implementatiedomein, zodat elk gegenereerd subdomein wordt doorgestuurd naar de insingress-controller van het Kubernetes-cluster. 

Elke eigen domeinprovider wijst op zijn eigen manier DNS-servers toe. Als u uw [domeinnaamservers wilt overdragen aan Azure DNS](../../dns/dns-delegate-domain-azure-dns.md), voert u de volgende stappen uit:

1. Maak een resourcegroep voor uw zone.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Maak een DNS-zone voor uw domein.
   Gebruik de opdracht [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) om de naamservers te verkrijgen voor het delegeren van DNS-controle over uw domein aan Azure.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Voeg de DNS-servers die u ontvangt toe aan de domeinprovider van uw implementatiedomein. Op die manier kunt u Azure DNS gebruiken om uw domein naar wens opnieuw toe te wijzen. De manier waarop u dit doet, verschilt per domein; [delegeren van uw domeinnaamservers aan Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) bevat een aantal van de details die u moet weten. 
4. Zodra uw domein is gedelegeerd aan Azure DNS, maakt u een A-recordsetvermelding voor uw implementatiedomeintoewijzing naar het `ingress` IP-adres van stap 2 van de vorige sectie.
   ```azurecli
   az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
   ```
   De uitvoer ziet er ongeveer zo uit:
   ```json
   {
    "arecords": [
      {
        "ipv4Address": "13.64.108.240"
      }
    ],
    "etag": "<guid>",
    "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
    "metadata": null,
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
   }
   ```
5. Concept opnieuw **installeren**

   1. Het **concept** verwijderen uit `helm delete --purge draft`het cluster door te typen . 
   2. Ontwerp **opnieuw** installeren met `draft-init` dezelfde opdracht, `--ingress-enabled` maar met de optie:
      ```bash
      draft init --ingress-enabled
      ```
      Reageer op de prompts zoals je de eerste keer deed, hierboven. U hebt echter nog een vraag om op te reageren, met behulp van het volledige domeinpad dat u hebt geconfigureerd met de Azure DNS.

6. Voer uw top-level domein in voor binnendringen (bijvoorbeeld draft.example.com): draft.squillace.io
7. Wanneer u `draft up` deze keer belt, u uw `curl` toepassing (of deze) `<appname>.draft.<domain>.<top-level-domain>`zien op de URL van het formulier. In het geval van `http://handy-labradoodle.draft.squillace.io`dit voorbeeld, . 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>Volgende stappen

Nu u beschikt over een ACS Kubernetes-cluster, kunt u onderzoek uitvoeren met [Azure Container Registry](../../container-registry/container-registry-intro.md) om meer en andere implementaties te maken voor dit scenario. U kunt bijvoorbeeld de domein-DNS-recordset draft._basedomain.toplevel_ maken om processen van specifieke ACS-implementaties vanuit een dieper gelegen subdomein te controleren.






