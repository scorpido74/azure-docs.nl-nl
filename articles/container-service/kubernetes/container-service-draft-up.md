---
title: KEUR Concept gebruiken met Azure Container Service en Azure Container Registry
description: Maak een ACS Kubernetes-cluster en een Azure Container Registry om uw eerste toepassing met Draft te maken in Azure.
author: squillace
ms.service: container-service
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 039879cb725c20af3c40698947ea9abe189d08ad
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731760"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>KEUR Concept gebruiken met Azure Container Service en Azure Container Registry een toepassing bouwen en implementeren op Kubernetes

> [!TIP]
> Zie voor de bijgewerkte versie van dit artikel die gebruikmaakt van de Azure Kubernetes-service [Draft gebruiken met Azure Kubernetes service (AKS)](../../aks/kubernetes-draft.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Draft](https://aka.ms/draft) is een nieuw open-source hulpprogramma waarmee u eenvoudig containertoepassingen kunt ontwikkelen en ze kunt implementeren in Kubernetes-clusters. U hoeft hiervoor niet veel te weten over Docker en Kubernetes (u hoeft ze zelfs niet eens te installeren!). Als u een hulpprogramma als Draft gebruikt, kunnen u en uw team zich richten op het bouwen van de toepassing met Kubernetes en hoeft er minder aandacht te worden besteed aan de infrastructuur.

U kunt Draft gebruiken met alle Docker-installatiekopieregisters en alle Kubernetes-clusters, ook de lokale. In deze zelf studie leert u hoe u ACS met Kubernetes en ACR kunt gebruiken om een live, maar veilige ontwikkelaars pijplijn in Kubernetes te maken met behulp van concept, en hoe u Azure DNS kunt gebruiken om die ontwikkelaars pijplijn weer te geven zodat anderen deze kunnen zien in een domein.


## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken
U kunt eenvoudig [een nieuw Azure Container Registry maken](../../container-registry/container-registry-get-started-azure-cli.md). De stappen daarvoor zijn als volgt:

1. Maak een Azure-resource groep voor het beheren van uw ACR-REGI ster en het Kubernetes-cluster in ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Maak een ACR-installatie kopie register met [AZ ACR Create](/cli/azure/acr#az-acr-create) en zorg `--admin-enabled` ervoor dat de optie `true`is ingesteld op.
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


1. Down load concepten voor uw omgeving https://github.com/Azure/draft/releases op en installeer deze in uw pad zodat u de opdracht kunt gebruiken.
2. Down load helm voor uw omgeving https://github.com/kubernetes/helm/releases en [Installeer deze in uw pad zodat u de opdracht kunt gebruiken](https://helm.sh/docs/intro/quickstart#install-helm).
3. Configureer Draft om uw eigen register te gebruiken en om subdomeinen te maken voor elk Helm-diagram dat wordt gemaakt. U hebt het volgende nodig voor het configureren van Draft:
   - de naam van uw Azure Container Registry (in dit voorbeeld is dat `draftacsdemo`)
   - de registersleutel of het wachtwoord van `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.

   Bij `draft init` het aanroepen en het configuratie proces wordt u gevraagd om de bovenstaande waarden. Houd er rekening mee dat de URL-indeling voor de register-URL de register naam is `draftacsdemo`(in `.azurecr.io`dit voor beeld) plus. Uw gebruikers naam is zelf de register naam. De eerste keer dat u het proces uitvoert, ziet het er ongeveer als volgt uit.
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

In de Draft-opslagplaats staan [zes eenvoudige voorbeeldtoepassingen](https://github.com/Azure/draft/tree/master/examples). Kloon de opslag plaats en gebruik het Java- [voor beeld](https://github.com/Azure/draft/tree/master/examples/example-java). Wijzig de voor beelden/Java-map en typ `draft create` om de toepassing te bouwen. De toepassing zou er als volgt moeten uitzien.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

De uitvoer bevat een Dockerfile en een Helm-diagram. Typ `draft up` om te bouwen en implementeren. De uitvoer is uitgebreid, maar moet hetzelfde zijn als in het volgende voor beeld.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Uw toepassing veilig weer geven

Uw container wordt nu uitgevoerd in ACS. Als u deze wilt weer geven `draft connect` , gebruikt u de opdracht, waarmee een beveiligde verbinding met het IP-adres van het cluster wordt gemaakt met een specifieke poort voor uw toepassing, zodat u deze lokaal kunt weer geven. Als dit lukt, zoekt u naar de URL om verbinding te maken met uw app op de eerste regel na de **succes** indicator.

> [!NOTE]
> Als u een bericht ontvangt met de mede deling dat er geen peuling is voltooid, wacht u even en probeer het opnieuw, of u kunt `kubectl get pods -w` de peulen die nu klaar zijn met de voor bereiding en het opnieuw proberen.

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

In het vorige voor beeld kunt u typen `curl -s http://localhost:46143` om het antwoord te ontvangen `Hello World, I'm Java!`. Wanneer u CTRL + of CMD + C (afhankelijk van uw besturings systeem omgeving), wordt de beveiligde tunnel gescheurd en kunt u door gaan met herhalen.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Uw toepassing delen door een implementatie domein met Azure DNS te configureren

U hebt de herhalings lus van de ontwikkelaar al uitgevoerd die in de voor gaande stappen is gemaakt. U kunt uw toepassing echter via internet delen door:
1. Een ingang in uw ACS-cluster installeren (om een openbaar IP-adres op te geven waarop de app moet worden weer gegeven)
2. Uw aangepaste domein delegeren naar Azure DNS en uw domein toewijzen aan het IP-adres ACS dat wordt toegewezen aan de ingangs controller

### <a name="use-helm-to-install-the-ingress-controller"></a>Gebruik helm om de ingangs controller te installeren.
Gebruik **helm** om te zoeken naar en `stable/traefik`te installeren, een ingangs controller, om binnenkomende aanvragen voor uw builds in te scha kelen.
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

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Het ingangs-IP-adres toewijzen aan een aangepast subdomein

Draft maakt een release voor elke Helm-grafiek die er wordt gemaakt en elke toepassing waar u aan werkt. Elk hiervan haalt een gegenereerde naam op die wordt gebruikt door **concept** als _subdomein_ boven op het hoofd _domein_ van de implementatie die u beheert. (In dit voor beeld gebruiken `squillace.io` we als het implementatie domein.) Als u dit gedrag van subdomeinen wilt inschakelen, moet u een `'*.draft'` A-record maken voor in uw DNS-vermeldingen voor uw implementatie domein, zodat elk gegenereerd subdomein wordt doorgestuurd naar de ingangs controller van het Kubernetes-cluster. 

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
3. Voeg de DNS-servers die u ontvangt toe aan de domeinprovider van uw implementatiedomein. Op die manier kunt u Azure DNS gebruiken om uw domein naar wens opnieuw toe te wijzen. Hoe u dit doet, is afhankelijk van het domein. [Delegeer uw domein naam servers naar Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) bevat enkele details die u moet weten. 
4. Als uw domein is overgedragen aan Azure DNS, maakt u een recordset-vermelding voor uw implementatie domein toewijzing aan het `ingress` IP-adres uit stap 2 van de vorige sectie.
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
5. **Concept** opnieuw installeren

   1. Verwijder de **concepten** uit het cluster door te `helm delete --purge draft`typen. 
   2. **Concept** opnieuw installeren met behulp `draft-init` van dezelfde opdracht, maar `--ingress-enabled` met de optie:
      ```bash
      draft init --ingress-enabled
      ```
      Reageer op de prompts zoals u de eerste keer hebt gedaan. U hebt echter nog één vraag om te reageren op, met behulp van het volledige pad van het domein dat u hebt geconfigureerd met de Azure DNS.

6. Voer uw domein op het hoogste niveau in voor ingangs (bijvoorbeeld draft.example.com): draft.squillace.io
7. Wanneer u deze `draft up` tijd aanroept, kunt u uw toepassing (of `curl` IT) zien op de URL van het formulier. `<appname>.draft.<domain>.<top-level-domain>` In het geval van dit voor beeld `http://handy-labradoodle.draft.squillace.io`. 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>Volgende stappen

Nu u beschikt over een ACS Kubernetes-cluster, kunt u onderzoek uitvoeren met [Azure Container Registry](../../container-registry/container-registry-intro.md) om meer en andere implementaties te maken voor dit scenario. U kunt bijvoorbeeld de domein-DNS-recordset draft._basedomain.toplevel_ maken om processen van specifieke ACS-implementaties vanuit een dieper gelegen subdomein te controleren.






