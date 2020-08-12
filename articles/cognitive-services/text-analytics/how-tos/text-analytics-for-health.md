---
title: Text Analytics voor de status gebruiken
titleSuffix: Azure Cognitive Services
description: Meer informatie over het extra heren en labelen van medische gegevens uit ongestructureerde klinische tekst met Text Analytics voor de status.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: 71cbf03a36dd95eb66c3dcbaffbf4b63d889f507
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121575"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Procedure: Text Analytics gebruiken voor de status (preview)

> [!NOTE]
> De Text Analytics voor de status container is onlangs bijgewerkt. Bekijk [wat er nieuw is](../whats-new.md) voor meer informatie over recente wijzigingen. Vergeet niet om de meest recente container te halen voor het gebruik van de updates die worden vermeld.

> [!IMPORTANT] 
> Text Analytics status is een preview-functie die IS ingesteld op ' AS IS ' en ' WITH ALL FAULTs '. Daarom **moet Text Analytics voor status (preview) niet worden geïmplementeerd of geïmplementeerd in productie gebruik.** Text Analytics de status niet is bedoeld of beschikbaar gesteld voor gebruik als medisch apparaat, klinisch ondersteunings programma of andere technologie, bedoeld om te worden gebruikt in de diagnose, het verkrijgen, beperken, behandelen of voor komen van ziekten of andere voor waarden en er wordt geen licentie of recht verleend door micro soft om deze mogelijkheid voor dergelijke doel einden te gebruiken. Deze mogelijkheid is niet ontworpen of bedoeld om te worden geïmplementeerd of gedistribueerd als een plaatsvervanger voor professioneel medisch advies of advies, diagnose, behandeling of het klinisch arrest van een ziekte medewerker, en mag niet als zodanig worden gebruikt. De klant is alleen verantwoordelijk voor het gebruik van Text Analytics voor de status. Micro soft garandeert niet dat Text Analytics voor de gezondheid of materialen die in verband met de mogelijkheid worden geleverd, voldoende zijn voor medische doel einden of dat anderszins voldoen aan de gezondheids-en medische vereisten van een persoon. 


Text Analytics status is een container service die relevante medische gegevens ophaalt en uitpakt uit ongestructureerde teksten, zoals dokters notities, samen vattingen van de afvoer, klinische documenten en elektronische status records.  

## <a name="features"></a>Functies

De Text Analytics voor de status container voert momenteel de naam entity Recognition (NER), de extractie van relaties, het afwijzen van entiteiten en het koppelen van koppelingen voor Engelse tekst in uw eigen ontwikkel omgeving uit die voldoet aan uw specifieke vereisten voor beveiliging en gegevens beheer.

#### <a name="named-entity-recognition"></a>[Herkenning van benoemde entiteiten](#tab/ner)

Herkenning met de naam entiteit detecteert woorden en zinsdelen die in ongestructureerde tekst worden genoemd en die kunnen worden gekoppeld aan een of meer semantische typen, zoals diagnose, medicijnen naam, symptoom/ondertekening of leeftijd.

> [!div class="mx-imgBorder"]
> ![Status NER](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[Relatie-extractie](#tab/relation-extraction)

Met relatie-extractie worden betekenis volle verbindingen van concepten aangegeven die worden vermeld in de tekst. Zo wordt een relatie ' tijd van voor waarde ' gevonden door een voorwaarde naam te koppelen aan een tijd. 

> [!div class="mx-imgBorder"]
> ![Status RE](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[Entiteit koppelen](#tab/entity-linking)

Entiteit koppelt disambiguates afzonderlijke entiteiten door benoemde entiteiten die worden vermeld in tekst te koppelen aan concepten die zijn gevonden in een vooraf gedefinieerde data base met concepten. Bijvoorbeeld het Unified medisch taal systeem (UMLS).

> [!div class="mx-imgBorder"]
> ![Health EL](../media/ta-for-health/health-entity-linking.png)

Text Analytics status ondersteunt de koppeling naar de Health-en biomedische woorden lijst in de[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)-kennis bron (Unified medisch language System).

#### <a name="negation-detection"></a>[Detectie van negatie](#tab/negation-detection) 

De betekenis van medische inhoud wordt sterk beïnvloed door para meters zoals negatie, wat kritieke implicatie kan hebben als er een probleem is vastgesteld. Text Analytics voor de status ondersteunt de detectie van negatie voor de verschillende entiteiten die in de tekst worden genoemd. 

> [!div class="mx-imgBorder"]
> ![Status NEG](../media/ta-for-health/health-negation.png)

---

Bekijk de [entiteits categorieën](../named-entity-types.md?tabs=health) die door Text Analytics worden geretourneerd voor een volledige lijst met ondersteunde entiteiten.

## <a name="supported-languages"></a>Ondersteunde talen

Text Analytics alleen voor de status ondersteunt documenten in de Engelse taal.

## <a name="request-access-to-the-container-registry"></a>Toegang aanvragen tot het container register

Vul het [aanvraag formulier voor de Cognitive Services containers](https://aka.ms/cognitivegate) in en verzend het om toegang tot de container aan te vragen. U wordt momenteel niet gefactureerd voor Text Analytics voor het status gebruik. 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>De container installeren

Er zijn meerdere manieren waarop u de container kunt installeren en uitvoeren. 

- Gebruik de [Azure Portal](text-analytics-how-to-install-containers.md?tabs=healthcare) om een Text Analytics resource te maken en gebruik docker om uw container op te halen.
- Gebruik de volgende Power shell-en [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) -scripts voor het automatiseren van de configuratie van de resource-implementatie container.

### <a name="install-the-container-using-azure-web-app-for-containers"></a>De container installeren met behulp van Azure Web App for Containers

Azure [Web App for containers](https://azure.microsoft.com/services/app-service/containers/) is een Azure-resource die is toegewezen aan het uitvoeren van containers in de Cloud. Het biedt out-of-the-box-mogelijkheden, zoals automatisch schalen, ondersteuning van docker-containers en docker-samen stellen, HTTPS-ondersteuning en nog veel meer.

> [!NOTE]
> Met Azure Web App krijgt u automatisch een domein in de vorm van`<appservice_name>.azurewebsites.net`

Voer dit Power shell-script uit met behulp van de Azure CLI om een Web App for Containers te maken, met behulp van uw abonnement en de container installatie kopie via HTTPS. Wacht totdat het script is voltooid (ongeveer 25-30 minuten) voordat u de eerste aanvraag indient.

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>De container installeren met behulp van Azure container instance

U kunt ook een Azure container instance (ACI) gebruiken om de implementatie eenvoudiger te maken. ACI is een bron waarmee u docker-containers op aanvraag kunt uitvoeren in een beheerde, serverloze Azure-omgeving. 

Zie [Azure container instances gebruiken](text-analytics-how-to-use-container-instances.md) voor stappen voor het implementeren van een ACI-bron met behulp van de Azure Portal. U kunt ook het onderstaande Power shell-script gebruiken met behulp van Azure CLI, waarmee u een ACI maakt in uw abonnement met behulp van de container installatie kopie.  Wacht totdat het script is voltooid (ongeveer 25-30 minuten) voordat u de eerste aanvraag indient.  Als gevolg van de limiet van het maximum aantal Cpu's per ACI-resource, selecteert u deze optie niet als u verwacht dat u meer dan 5 grote documenten (ongeveer 5000 tekens per aanvraag verzendt).
Raadpleeg het artikel over [regionale ondersteuning voor ACI](https://docs.microsoft.com/azure/container-instances/container-instances-region-availability) voor informatie over de beschik baarheid. 

> [!NOTE] 
> Azure Container Instances geen HTTPS-ondersteuning voor de ingebouwde domeinen op. Als u HTTPS nodig hebt, moet u deze hand matig configureren, met inbegrip van het maken van een certificaat en het registreren van een domein. U vindt hier instructies om dit te doen met NGINX hieronder.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Beveiligde ACI-connectiviteit

Standaard wordt er geen beveiliging gegeven bij gebruik van ACI met container-API. De reden hiervoor is dat de containers worden uitgevoerd als onderdeel van een pod dat door een netwerk brug wordt beschermd vanaf de buiten wereld. U kunt een container echter wijzigen met een front-facing onderdeel, zodat het container eindpunt privé blijft. De volgende voor beelden gebruiken [NGINX](https://www.nginx.com) als ingangs gateway voor de ondersteuning van HTTPS/SSL en verificatie van client certificaten.

> [!NOTE]
> NGINX is een open-source, snelle HTTP-server en-proxy. Een NGINX-container kan worden gebruikt om een TLS-verbinding voor één container te beëindigen. Meer complexe NGINX ingangs-gebaseerde TLS-afsluit oplossingen zijn ook mogelijk.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>NGINX instellen als een ingangs gateway

NGINX maakt gebruik van [configuratie bestanden](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) om functies tijdens runtime in te scha kelen. Als u TLS-beëindiging voor een andere service wilt inschakelen, moet u een SSL-certificaat opgeven om de TLS-verbinding te beëindigen en `proxy_pass` een adres voor de service op te geven. Hieronder vindt u een voor beeld.


> [!NOTE]
> `ssl_certificate`verwacht een pad dat moet worden opgegeven in het lokale bestands systeem van de NGINX-container. Het opgegeven adres `proxy_pass` moet beschikbaar zijn in het NGINX-netwerk van de container.

Alle bestanden in de container NGINX `_.conf_` worden geladen die zijn gekoppeld onder `/etc/nginx/conf.d/` in het http-configuratiepad.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Voor beeld van docker-opstellen van bestand

In het onderstaande voor beeld ziet u hoe een [docker opstellen](https://docs.docker.com/compose/reference/overview) bestand kan worden gemaakt om de NGINX en Text Analytics voor status containers te implementeren:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Voer de volgende opdracht uit vanaf een console op het hoofd niveau van het bestand om het docker opstellen van het bestand te initiëren:

```bash
docker-compose up
```

Zie de documentatie van NGINX over [NGINX SSL-beëindiging](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)voor meer informatie.


## <a name="example-api-request"></a>Voorbeeld van API-aanvraag
De container bevat op REST gebaseerde eindpunt-API's voor queryvoorspelling.

Gebruik de onderstaande voor beeld-krul aanvraag voor het verzenden van een query naar de container die u hebt geïmplementeerd om de variabele te vervangen `serverURL` door de juiste waarde.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

De volgende JSON is een voor beeld van een JSON-bestand dat is gekoppeld aan de Text Analytics voor de hoofd tekst van de status-API-aanvraag:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>Tekst van API-antwoord

De volgende JSON is een voor beeld van de Text Analytics voor Health API-antwoord tekst:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "ConfidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "ConfidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "ConfidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "ConfidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>Uitvoer van negatie detectie

Wanneer u de detectie van negatie gebruikt, kan één negatie term in sommige gevallen meerdere voor waarden tegelijk aanpakken. De ontkenning van een herkende entiteit wordt weer gegeven in de JSON-uitvoer met de Booleaanse waarde van de `isNegated` vlag:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Uitvoer van relatie-extractie

Uitvoer van relatie-extractie bevat URI-verwijzingen naar de *bron* van de relatie en het *doel*ervan. Entiteiten met een relatie rol van `ENTITY` worden toegewezen aan het `target` veld. Entiteiten met een relatie rol van `ATTRIBUTE` worden toegewezen aan het `source` veld. Afkortings relaties bevatten bidirectionele `source` en `target` velden en worden `bidirectional` ingesteld op `true` . 

```json
"relations": [
  {
      "relationType": "DosageOfMedication",
      "score": 1.0,
      "bidirectional": false,
      "source": "#/documents/2/entities/0",
      "target": "#/documents/2/entities/1",
      "entities": [
          {
              "id": "0",
              "role": "ATTRIBUTE"
          },
          {
              "id": "1",
              "role": "ENTITY"
          }
      ]
  },
...
]
```

## <a name="see-also"></a>Zie ook

* [Overzicht van Text Analytics](../overview.md)
* [Benoemde entiteits Categorieën](../named-entity-types.md)
* [Nieuwe functies](../whats-new.md)
