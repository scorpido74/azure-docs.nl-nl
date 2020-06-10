---
title: Verificatie instellen
titleSuffix: Azure Machine Learning
description: Meer informatie over het instellen en configureren van verificatie voor verschillende resources en werk stromen in Azure Machine Learning. Er zijn meerdere manieren om verificatie in de service te configureren en te gebruiken, variërend van eenvoudige op gebruikers interface gebaseerde verificatie voor ontwikkelings-of test doeleinden tot volledige Azure Active Directory Service-Principal-verificatie.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 12/17/2019
ms.custom: has-adal-ref
ms.openlocfilehash: e6fd2ba9210aa8f133ed08e850e4ded978682988
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629242"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Verificatie instellen voor Azure Machine Learning resources en werk stromen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u verificatie kunt instellen en configureren voor verschillende resources en werk stromen in Azure Machine Learning. Er zijn meerdere manieren om te verifiëren bij de service, variërend van eenvoudige op gebruikers interface gebaseerde auth voor ontwikkelings-of test doeleinden tot volledige Azure Active Directory Service-Principal-verificatie. In dit artikel wordt ook uitgelegd wat de verschillen zijn in de manier waarop verificatie van webservices werkt, en hoe u zich kunt verifiëren bij de Azure Machine Learning REST API.

In deze procedure ziet u hoe u de volgende taken kunt uitvoeren:

* Interactieve gebruikers interface-verificatie gebruiken voor testen/ontwikkeling
* Service-Principal-verificatie instellen
* Verifiëren bij uw werk ruimte
* OAuth 2.0 Bearer-tokens ophalen voor Azure Machine Learning REST API
* Verificatie van webservices

Zie het [concept artikel](concept-enterprise-security.md) voor een algemeen overzicht van beveiliging en verificatie in azure machine learning.

## <a name="prerequisites"></a>Vereisten

* Maak een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).
* [Configureer uw ontwikkel omgeving](how-to-configure-environment.md) om de Azure machine learning SDK te installeren, of gebruik een [Azure machine learning-notebook-VM](concept-azure-machine-learning-architecture.md#compute-instance) met de SDK die al is geïnstalleerd.

## <a name="interactive-authentication"></a>Interactieve verificatie

De meeste voor beelden in de documentatie voor deze service gebruiken interactieve verificatie in Jupyter-notebooks als eenvoudige methode voor testen en demonstratie. Dit is een licht gewichtve manier om te testen wat u ontwikkelt. Er zijn twee functie aanroepen waarmee automatisch een verificatie stroom op basis van een gebruikers interface wordt gevraagd.

Bij het aanroepen van de `from_config()` functie wordt de prompt weer gegeven.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

De `from_config()` functie zoekt naar een JSON-bestand met de verbindings gegevens van de werk ruimte. U kunt de verbindings gegevens ook expliciet opgeven met behulp van de `Workspace` constructor, die ook wordt gevraagd om interactieve verificatie. Beide aanroepen zijn gelijk.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Als u toegang hebt tot meerdere tenants, moet u de klasse wellicht importeren en expliciet definiëren welke Tenant u wilt richten. Als u de constructor aanroept voor `InteractiveLoginAuthentication` , wordt u gevraagd om u aan te melden, vergelijkbaar met de bovenstaande oproepen.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Interactieve verificatie is handig voor testen en leren, maar biedt geen ondersteuning voor het bouwen van geautomatiseerde of headless werk stromen. Het instellen van Service-Principal-verificatie is de beste benadering voor geautomatiseerde processen die gebruikmaken van de SDK.

## <a name="set-up-service-principal-authentication"></a>Service-Principal-verificatie instellen

Dit proces is nodig voor het inschakelen van verificatie die is losgekoppeld van een specifieke gebruikers aanmelding, waarmee u zich kunt verifiëren bij de Azure Machine Learning python SDK in geautomatiseerde werk stromen. Met Service-Principal-verificatie kunt u [zich ook verifiëren bij de rest API](#azure-machine-learning-rest-api-auth).

> [!TIP]
> Service-principals moeten toegang hebben tot uw werk ruimte via [Azure op rollen gebaseerd toegangs beheer (RBAC)](../role-based-access-control/overview.md).
>
> Met de ingebouwde rollen van **eigenaar** of **Inzender** voor uw werk ruimte kan de Service-Principal alle activiteiten uitvoeren, zoals het trainen van een model, het implementeren van een model, enzovoort. Zie [toegang tot een Azure machine learning-werk ruimte beheren](how-to-assign-roles.md)voor meer informatie over het gebruik van rollen.

Als u Service-Principal-verificatie wilt instellen, maakt u eerst een app-registratie in Azure Active Directory en wijst u vervolgens een rol toe aan uw app. De eenvoudigste manier om deze installatie te volt ooien, is via de [Azure Cloud shell](https://azure.microsoft.com/features/cloud-shell/) in de Azure Portal. Nadat u zich hebt aangemeld bij de portal, klikt u op het `>_` pictogram in de rechter bovenhoek van de pagina bij uw naam om de shell te openen.

Als u de Cloud Shell nog niet eerder in uw Azure-account hebt gebruikt, moet u een opslag account resource maken voor het opslaan van bestanden die zijn geschreven. In het algemeen heeft dit opslag account een te verwaarlozen maandelijkse kosten. Installeer de machine learning-extensie ook als u deze nog niet eerder hebt gebruikt met de volgende opdracht.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> U moet een beheerder zijn van het abonnement om de volgende stappen uit te voeren.

Voer vervolgens de volgende opdracht uit om de service-principal te maken. Geef deze een naam, in dit geval **ml-auth**.

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

Voer vervolgens de volgende opdracht uit om de details op te halen van de service-principal die u zojuist hebt gemaakt, met behulp `clientId` van de bovenstaande waarde als invoer voor de `--id` para meter.

```azurecli-interactive
az ad sp show --id your-client-id
```

Hier volgt een vereenvoudigd voor beeld van de JSON-uitvoer van de opdracht. Noteer het `objectId` veld, omdat u de waarde voor de volgende stap nodig hebt.

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

Gebruik vervolgens de volgende opdracht om uw Service-Principal toegang toe te wijzen aan uw machine learning-werk ruimte. U hebt de naam van uw werk ruimte en de naam van de resource groep nodig voor `-w` `-g` respectievelijk de para meters en. Voor de `--user` para meter gebruikt u de `objectId` waarde uit de vorige stap. Met de `--role` para meter kunt u de Access-rol voor de service-principal instellen en in het algemeen gebruikt u **eigenaar** of **Inzender**. Beide hebben schrijf toegang tot bestaande resources, zoals reken clusters en gegevens bronnen, maar alleen de **eigenaar** kan deze resources inrichten.

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Deze aanroep produceert geen uitvoer, maar u hebt nu Service-Principal-verificatie ingesteld voor uw werk ruimte.

## <a name="authenticate-to-your-workspace"></a>Verifiëren bij uw werk ruimte

Nu u Service-Principal-verificatie hebt ingeschakeld, kunt u zich bij uw werk ruimte in de SDK aanmelden zonder dat u zich fysiek als gebruiker aanmeldt. Gebruik de `ServicePrincipalAuthentication` klasse-constructor en gebruik de waarden uit de vorige stappen als de para meters. De `tenant_id` para meter wordt toegewezen aan `tenantId` van boven, wordt `service_principal_id` toegewezen aan `clientId` en wordt `service_principal_password` toegewezen aan `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

De `sp` variabele bevat nu een verificatie object dat u rechtstreeks in de SDK gebruikt. Over het algemeen is het een goed idee om de id's/geheimen die hierboven worden gebruikt, op te slaan in omgevings variabelen, zoals in de volgende code wordt weer gegeven.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Voor automatische werk stromen die worden uitgevoerd in Python en de SDK voornamelijk gebruiken, kunt u dit object in de meeste gevallen gebruiken voor uw verificatie. De volgende code verifieert uw werk ruimte met behulp van het auth-object dat u zojuist hebt gemaakt.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST API auth

De service-principal die u in de bovenstaande stappen hebt gemaakt, kan ook worden gebruikt voor de verificatie van de Azure Machine Learning [rest API](https://docs.microsoft.com/rest/api/azureml/). U gebruikt de Azure Active Directory [toekennings stroom voor client referenties](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow), waarmee service-to-service-aanroepen kunnen worden uitgevoerd voor headless authenticatie in automatische werk stromen. De voor beelden worden geïmplementeerd met de [ADAL-bibliotheek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) in Python en node. js, maar u kunt ook een open-source bibliotheek gebruiken die openid connect Connect 1,0 ondersteunt.

> [!NOTE]
> MSAL. js is een nieuwe bibliotheek dan ADAL, maar u kunt geen service-to-service-verificatie uitvoeren met behulp van client referenties met MSAL. js, omdat het hoofd zakelijk een bibliotheek aan de client zijde is die is bedoeld voor interactieve/UI-verificatie die is gekoppeld aan een specifieke gebruiker. We raden u aan ADAL te gebruiken zoals hieronder wordt weer gegeven om automatische werk stromen te bouwen met de REST API.

### <a name="nodejs"></a>Node.js

Gebruik de volgende stappen om een verificatie token te genereren met behulp van node. js. Voer uit in uw omgeving `npm install adal-node` . Gebruik vervolgens uw `tenantId` , `clientId` , en `clientSecret` van de service-principal die u in de bovenstaande stappen hebt gemaakt als waarden voor de overeenkomende variabelen in het volgende script.

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

De variabele `tokenResponse` is een object dat het token en de gekoppelde meta gegevens bevat, zoals de verloop tijd. Tokens zijn geldig gedurende 1 uur en kunnen worden vernieuwd door dezelfde aanroep opnieuw uit te voeren om een nieuw token op te halen. Hier volgt een voor beeld van een antwoord.

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

### <a name="python"></a>Python

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

De variabele `token_response` is een woorden lijst die het token en de gekoppelde meta gegevens bevat, zoals de verval tijd. Tokens zijn geldig gedurende 1 uur en kunnen worden vernieuwd door dezelfde aanroep opnieuw uit te voeren om een nieuw token op te halen. Hier volgt een voor beeld van een antwoord.

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

Web-Services in Azure Machine Learning gebruiken een ander verificatie patroon dan hierboven is beschreven. De eenvoudigste manier om te verifiëren bij geïmplementeerde webservices is door gebruik te maken **van verificatie op basis van een sleutel**, waarmee statisch Bearer-type verificatie sleutels worden gegenereerd die niet moeten worden vernieuwd. Als u alleen bij een geïmplementeerde webservice wilt verifiëren, hoeft u geen Service Principle-verificatie in te stellen, zoals hierboven wordt weer gegeven.

Op de web-services die zijn geïmplementeerd op de Azure Kubernetes-service, is standaard op sleutel gebaseerde verificatie *ingeschakeld* . Azure Container Instances geïmplementeerde Services hebben op sleutel gebaseerde verificatie standaard *uitgeschakeld* , maar u kunt deze inschakelen door in te stellen `auth_enabled=True` bij het maken van de ACI-webservice. Hier volgt een voor beeld van het maken van een ACI-implementatie configuratie met ingeschakelde authenticatie op basis van een sleutel.

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

Web-Services bieden ook ondersteuning voor verificatie op basis van tokens, maar alleen voor implementaties van Azure Kubernetes-service. Bekijk de [instructies](how-to-consume-web-service.md) voor het gebruik van webservices voor aanvullende informatie over verificatie.

### <a name="token-based-web-service-authentication"></a>Verificatie op basis van tokens-based web-service

Wanneer u token verificatie inschakelt voor een webservice, moeten gebruikers een Azure Machine Learning JSON Web Token aan de webservice aanbieden om deze te openen. Het token verloopt na een opgegeven tijds kader en moet worden vernieuwd om aanroepen voort te zetten.

* Token verificatie is **standaard uitgeschakeld** wanneer u implementeert in azure Kubernetes service.
* Verificatie van tokens **wordt niet ondersteund** wanneer u implementeert in azure container instances.

Als u de verificatie van tokens wilt beheren, gebruikt `token_auth_enabled` u de para meter bij het maken of bijwerken van een implementatie.

Als token verificatie is ingeschakeld, kunt u de `get_token` methode gebruiken om een JSON Web token (JWT) op te halen en de verval tijd van dat token:

> [!TIP]
> Als u een Service-Principal gebruikt om het token op te halen en de minimale vereiste toegang wilt hebben om een token op te halen, wijst u deze toe aan de rol van **lezer** voor de werk ruimte.

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> U moet een nieuw token aanvragen na de tijd van de token `refresh_by` . Als u tokens wilt vernieuwen buiten de python-SDK, kunt u het beste de REST API met Service-Principal-verificatie gebruiken om de oproep periodiek `service.get_token()` uit te voeren, zoals eerder is besproken.
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
