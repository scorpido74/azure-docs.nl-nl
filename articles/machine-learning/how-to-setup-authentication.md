---
title: Verificatie instellen
titleSuffix: Azure Machine Learning
description: Meer informatie over het instellen en configureren van verificatie voor verschillende resources en werk stromen in Azure Machine Learning. Er zijn meerdere manieren om verificatie in de service te configureren en te gebruiken, variërend van eenvoudige op gebruikers interface gebaseerde verificatie voor ontwikkelings-of test doeleinden tot volledige Azure Active Directory Service-Principal-verificatie.
services: machine-learning
author: larryfr
ms.author: larryfr
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-javascript
ms.openlocfilehash: 867babcf7160ce6e589197be0a3186e5a2156238
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423572"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Verificatie instellen voor Azure Machine Learning resources en werk stromen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het verifiëren van uw Azure Machine Learning-werk ruimte en modellen die zijn geïmplementeerd als webservices.

Over het algemeen zijn er twee soorten verificatie die u kunt gebruiken met Azure Machine Learning:

* __Interactief__: u gebruikt uw account in azure Active Directory om ofwel rechtstreeks te verifiëren, of om een token op te halen dat wordt gebruikt voor verificatie. Interactieve verificatie wordt gebruikt tijdens het experimenteren en iteratieve ontwikkeling. Of waar u de toegang tot resources (zoals een webservice) per gebruiker wilt beheren.
* __Service-Principal__: u maakt een Service-Principal-account in azure Active Directory en gebruikt dit om een token te verifiëren of op te halen. Een service-principal wordt gebruikt wanneer u een geautomatiseerd proces nodig hebt om te verifiëren bij de service zonder tussen komst van de gebruiker. Een voor beeld: een script voor continue integratie en implementatie waarmee een model wordt getraind en getest wanneer de trainings code verandert. U kunt ook een Service-Principal gebruiken om een token op te halen voor verificatie bij een webservice, als u niet wilt dat de eind gebruiker van de service wordt geverifieerd. Of waar de verificatie van de eind gebruiker niet rechtstreeks wordt uitgevoerd met Azure Active Directory.

Ongeacht welk verificatie type wordt gebruikt, wordt op rollen gebaseerd toegangs beheer (RBAC) gebruikt om het niveau van toegang dat is toegestaan voor de resources te bereiken. Een account dat wordt gebruikt om het toegangs token voor een geïmplementeerd model op te halen, heeft bijvoorbeeld alleen lees toegang tot de werk ruimte nodig. Zie [toegang tot Azure machine learning werk ruimte beheren](how-to-assign-roles.md)voor meer informatie over RBAC.

## <a name="prerequisites"></a>Vereisten

* Maak een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).
* [Configureer uw ontwikkel omgeving](how-to-configure-environment.md) om de Azure machine learning SDK te installeren, of gebruik een [Azure machine learning-notebook-VM](concept-azure-machine-learning-architecture.md#compute-instance) met de SDK die al is geïnstalleerd.

## <a name="interactive-authentication"></a>Interactieve verificatie

De meeste voor beelden in de documentatie en voor beelden gebruiken interactieve verificatie. Wanneer u de SDK gebruikt, zijn er bijvoorbeeld twee functie aanroepen waarmee automatisch een verificatie stroom op basis van een gebruikers interface wordt gevraagd:

* Bij het aanroepen van de `from_config()` functie wordt de prompt weer gegeven.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    De functie `from_config()` zoekt naar een JSON-bestand met de verbindingsgegevens van de werkruimte.

* Met de `Workspace` constructor om abonnements-, resource groep-en werkruimte gegevens op te geven, wordt u ook gevraagd om interactieve verificatie uit te voeren.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Als u toegang hebt tot meerdere tenants, moet u de klasse wellicht importeren en expliciet definiëren welke Tenant u wilt richten. Als u de constructor aanroept voor `InteractiveLoginAuthentication` , wordt u gevraagd om u aan te melden, vergelijkbaar met de bovenstaande oproepen.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

## <a name="service-principal-authentication"></a>Verificatie van service-principal

Als u de verificatie van de Service-Principal (SP) wilt gebruiken, moet u eerst de SP maken en toegang verlenen tot uw werk ruimte. Zoals eerder vermeld, wordt op Azure Role-based Access Control (RBAC) gebruikt om de toegang te beheren. Daarom moet u ook bepalen welke toegang u wilt verlenen aan de SP.

> [!IMPORTANT]
> Als u een Service-Principal gebruikt, geeft u deze de __Mini maal vereiste toegang voor de taak__ waarvoor deze wordt gebruikt. U kunt bijvoorbeeld geen service principal-eigenaar of Inzender toegang verlenen als u voor alle gebruikt voor het lezen van het toegangs token voor een webimplementatie.
>
> De reden voor het verlenen van de minimale toegang is dat een Service-Principal gebruikmaakt van een wacht woord voor verificatie en dat het wacht woord kan worden opgeslagen als onderdeel van een Automation-script. Als het wacht woord wordt gelekt, en de mini maal vereiste toegang voor een bepaalde taken, minimaliseert het schadelijke gebruik van de SP.

De eenvoudigste manier om een SP te maken en toegang te verlenen tot uw werk ruimte is met behulp van de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Als u een Service-Principal wilt maken en toegang tot uw werk ruimte wilt verlenen, gebruikt u de volgende stappen:

> [!NOTE]
> U moet een beheerder zijn van het abonnement om al deze stappen uit te voeren.

1. Verifiëren bij uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

    Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de instructies op de opdracht regel volgen. De instructies moeten bladeren naar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en een autorisatie code invoeren.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    Zie [Aanmelden met Azure cli](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)voor andere verificatie methoden.

1. Installeer de Azure Machine Learning extensie:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. De service-principal maken. In het volgende voor beeld wordt een SP **met de naam ml-auth** gemaakt:

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    De uitvoer is vergelijkbaar met het volgende. Noteer de `clientId` `clientSecret` velden, en, `tenantId` zoals u deze nodig hebt voor andere stappen in dit artikel.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. De Details voor de Service-Principal ophalen met de `clientId` waarde die is geretourneerd in de vorige stap:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    De volgende JSON is een vereenvoudigd voor beeld van de uitvoer van de opdracht. Noteer het `objectId` veld, omdat u de waarde voor de volgende stap nodig hebt.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Toestaan dat de SP toegang heeft tot uw Azure Machine Learning-werk ruimte. U hebt de naam van uw werk ruimte en de naam van de resource groep nodig voor `-w` `-g` respectievelijk de para meters en. Voor de `--user` para meter gebruikt u de `objectId` waarde uit de vorige stap. `--role`Met de para meter kunt u de Access-rol voor de service-principal instellen. In het volgende voor beeld wordt de SP toegewezen aan de rol **eigenaar** . 

    > [!IMPORTANT]
    > Met de functie voor toegang tot eigenaar kan de Service-Principal vrijwel elke bewerking in uw werk ruimte uitvoeren. Het wordt in dit document gebruikt om te demonstreren hoe toegang wordt verleend. in een productie omgeving raadt micro soft aan de service-principal de minimale toegang te geven die nodig is voor het uitvoeren van de rol die u wilt gebruiken. Zie [toegang tot Azure machine learning werk ruimte beheren](how-to-assign-roles.md)voor meer informatie.

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Deze aanroep produceert geen uitvoer bij geslaagde pogingen.

### <a name="use-a-service-principal-from-the-sdk"></a>Een service-principal van de SDK gebruiken

Als u de werk ruimte wilt verifiëren vanuit de SDK met behulp van de Service-Principal, gebruikt u de `ServicePrincipalAuthentication` constructor class. Gebruik de waarden die u hebt gekregen bij het maken van de service provider als de para meters. De `tenant_id` para meter wordt toegewezen aan `tenantId` van boven, wordt `service_principal_id` toegewezen aan `clientId` en wordt `service_principal_password` toegewezen aan `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

De `sp` variabele bevat nu een verificatie object dat u rechtstreeks in de SDK gebruikt. Over het algemeen is het een goed idee om de id's/geheimen die hierboven worden gebruikt, op te slaan in omgevings variabelen, zoals in de volgende code wordt weer gegeven. Als u in omgevings variabelen opslaat, voor komt u dat de gegevens per ongeluk in een GitHub-opslag plaats worden gecontroleerd.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Voor automatische werk stromen die worden uitgevoerd in Python en de SDK voornamelijk gebruiken, kunt u dit object in de meeste gevallen gebruiken voor uw verificatie. De volgende code verifieert uw werk ruimte met behulp van het auth-object dat u hebt gemaakt.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Een service-principal van de Azure CLI gebruiken

U kunt een Service-Principal gebruiken voor Azure CLI-opdrachten. Zie [Aanmelden met een Service-Principal](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#sign-in-using-a-service-principal)voor meer informatie.

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Een Service-Principal gebruiken met de REST API (preview-versie)

De service-principal kan ook worden gebruikt voor de verificatie van de Azure Machine Learning [rest API](https://docs.microsoft.com/rest/api/azureml/) (preview). U gebruikt de Azure Active Directory [toekennings stroom voor client referenties](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow), waarmee service-to-service-aanroepen kunnen worden uitgevoerd voor headless authenticatie in automatische werk stromen. De voor beelden worden geïmplementeerd met de [ADAL-bibliotheek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) in zowel Python als Node.js, maar u kunt ook een open-source bibliotheek gebruiken die openid connect Connect 1,0 ondersteunt.

> [!NOTE]
> MSAL.js is een nieuwe bibliotheek dan ADAL, maar u kunt geen service-to-service-verificatie uitvoeren met behulp van client referenties met MSAL.js, omdat het hoofd zakelijk een bibliotheek aan de client zijde is die is bedoeld voor interactieve/UI-verificatie die is gekoppeld aan een specifieke gebruiker. We raden u aan ADAL te gebruiken zoals hieronder wordt weer gegeven om automatische werk stromen te bouwen met de REST API.

#### <a name="nodejs"></a>Node.js

Gebruik de volgende stappen om een verificatie token te genereren met behulp van Node.js. Voer uit in uw omgeving `npm install adal-node` . Gebruik vervolgens uw `tenantId` , `clientId` , en `clientSecret` van de service-principal die u in de bovenstaande stappen hebt gemaakt als waarden voor de overeenkomende variabelen in het volgende script.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

De variabele `tokenResponse` is een object dat het token en de gekoppelde meta gegevens bevat, zoals de verloop tijd. Tokens zijn geldig gedurende 1 uur en kunnen worden vernieuwd door dezelfde aanroep opnieuw uit te voeren om een nieuw token op te halen. Het volgende code fragment is een voor beeld van een antwoord.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Gebruik de `accessToken` eigenschap om het verificatie token op te halen. Raadpleeg de [rest API-documentatie](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) voor voor beelden over het gebruik van het token voor het maken van API-aanroepen.

#### <a name="python"></a>Python

Gebruik de volgende stappen om een verificatie token te genereren met behulp van python. Voer uit in uw omgeving `pip install adal` . Gebruik vervolgens uw `tenantId` , `clientId` , en `clientSecret` van de service-principal die u in de bovenstaande stappen hebt gemaakt als waarden voor de juiste variabelen in het volgende script.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

De variabele `token_response` is een woorden lijst die het token en de gekoppelde meta gegevens bevat, zoals de verval tijd. Tokens zijn geldig gedurende 1 uur en kunnen worden vernieuwd door dezelfde aanroep opnieuw uit te voeren om een nieuw token op te halen. Het volgende code fragment is een voor beeld van een antwoord.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Gebruiken `token_response["accessToken"]` om het verificatie token op te halen. Raadpleeg de [rest API-documentatie](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) voor voor beelden over het gebruik van het token voor het maken van API-aanroepen.

## <a name="web-service-authentication"></a>Verificatie van de webservice

De model implementaties die zijn gemaakt door Azure Machine Learning bieden twee verificatie methoden:

* **op basis van sleutels**: een statische sleutel wordt gebruikt om te verifiëren bij de webservice.
* **op basis**van een token: er moet een tijdelijk token worden opgehaald uit de werk ruimte en worden gebruikt om de webservice te verifiëren. Dit token verloopt na een bepaalde tijd en moet worden vernieuwd om te blijven werken met de webservice.

    > [!NOTE]
    > Verificatie op basis van tokens is alleen beschikbaar bij het implementeren naar de Azure Kubernetes-service.

### <a name="key-based-web-service-authentication"></a>Verificatie op basis van sleutel-webservice

Op de web-services die zijn geïmplementeerd op Azure Kubernetes service (AKS) is standaard op basis van sleutel verificatie *ingeschakeld* . Op basis van Azure Container Instances (ACI) geïmplementeerde Services hebben op sleutel gebaseerde verificatie standaard *uitgeschakeld* , maar u kunt deze inschakelen door in te stellen `auth_enabled=True` bij het maken van de ACI-webservice. De volgende code is een voor beeld van het maken van een ACI-implementatie configuratie met ingeschakelde authenticatie op basis van een sleutel.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Vervolgens kunt u de aangepaste ACI-configuratie in de implementatie gebruiken met behulp van de- `Model` klasse.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Gebruik om de verificatie sleutels op te halen `aci_service.get_keys()` . Als u een sleutel opnieuw wilt genereren, gebruikt u de `regen_key()` functie en geeft u **primair** of **secundair**door.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Zie [een client maken voor een model dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)voor meer informatie over het verifiëren van een geïmplementeerd model.

### <a name="token-based-web-service-authentication"></a>Verificatie op basis van tokens-based web-service

Wanneer u token verificatie inschakelt voor een webservice, moeten gebruikers een Azure Machine Learning JSON Web Token aan de webservice aanbieden om deze te openen. Het token verloopt na bepaalde tijd, en moet worden vernieuwd om aanroepen te kunnen blijven doen.

* Token verificatie is **standaard uitgeschakeld** wanneer u implementeert in azure Kubernetes service.
* Verificatie van tokens **wordt niet ondersteund** wanneer u implementeert in azure container instances.
* Token verificatie **kan niet tegelijkertijd worden gebruikt als verificatie op basis van een sleutel**.

Als u de verificatie van tokens wilt beheren, gebruikt `token_auth_enabled` u de para meter bij het maken of bijwerken van een implementatie:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Als token verificatie is ingeschakeld, kunt u de `get_token` methode gebruiken om een JSON Web token (JWT) op te halen en de verval tijd van dat token:

> [!TIP]
> Als u een Service-Principal gebruikt om het token op te halen en de minimale vereiste toegang wilt hebben om een token op te halen, wijst u deze toe aan de rol van **lezer** voor de werk ruimte.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> U moet een nieuw token aanvragen nadat de `refresh_by`-tijd van het token is verstreken. Als u tokens wilt vernieuwen buiten de python-SDK, kunt u het beste de REST API met Service-Principal-verificatie gebruiken om de oproep periodiek `service.get_token()` uit te voeren, zoals eerder is besproken.
>
> We raden u ten zeerste aan om uw Azure Machine Learning-werk ruimte te maken in dezelfde regio als uw Azure Kubernetes service-cluster.
>
> Als u wilt verifiëren met een token, wordt er door de webservice een aanroep naar de regio waarin uw Azure Machine Learning-werk ruimte is gemaakt. Als de regio van uw werk ruimte niet beschikbaar is, kunt u geen token voor uw webservice ophalen, zelfs als uw cluster zich in een andere regio bevindt dan uw werk ruimte. Het resultaat is dat Azure AD-verificatie pas beschikbaar is als de regio van de werk ruimte weer beschikbaar is.
>
> Hoe groter de afstand tussen de regio van uw cluster en de regio van uw werk ruimte, hoe langer het duurt om een token op te halen.

## <a name="next-steps"></a>Volgende stappen

* [Geheimen gebruiken in trainingen](how-to-use-secrets-in-runs.md).
* [Train en implementeer een installatie kopie classificatie model](tutorial-train-models-with-aml.md).
* [Een Azure machine learning model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md).
