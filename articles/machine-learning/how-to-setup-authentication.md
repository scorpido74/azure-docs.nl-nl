---
title: Verificatie instellen
titleSuffix: Azure Machine Learning
description: Meer informatie over het instellen en configureren van verificatie voor verschillende resources en werkstromen in Azure Machine Learning. Er zijn meerdere manieren om verificatie binnen de service te configureren en te gebruiken, variërend van eenvoudige ui-gebaseerde auth voor ontwikkelings- of testdoeleinden tot volledige Azure Active Directory-serviceprincipal-verificatie.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283535"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Verificatie instellen voor Azure Machine Learning-resources en -werkstromen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u verificatie instelt en configureert voor verschillende bronnen en werkstromen in Azure Machine Learning. Er zijn meerdere manieren om de service te verifiëren, variërend van eenvoudige ui-gebaseerde auth voor ontwikkelings- of testdoeleinden tot volledige Azure Active Directory-servicehoofdverificatie. In dit artikel worden ook de verschillen in de manier waarop webserviceverificatie werkt en hoe u zich verifiëren naar de Azure Machine Learning REST API.

Deze how-to laat u zien hoe u de volgende taken uitvoert:

* Interactieve UI-verificatie gebruiken voor testen/ontwikkelen
* Serviceprincipal-verificatie instellen
* Authenticeren naar uw werkruimte
* OAuth2.0-tokens voor het type Azure Machine Learning REST-API
* Webserviceverificatie begrijpen

Zie het [conceptartikel](concept-enterprise-security.md) voor een algemeen overzicht van beveiliging en verificatie binnen Azure Machine Learning.

## <a name="prerequisites"></a>Vereisten

* Maak een [Azure Machine Learning-werkruimte](how-to-manage-workspace.md).
* [Configureer uw ontwikkelomgeving](how-to-configure-environment.md) om de Azure Machine Learning SDK te installeren of gebruik een [Azure Machine Learning Notebook VM](concept-azure-machine-learning-architecture.md#compute-instance) met de SDK die al is geïnstalleerd.

## <a name="interactive-authentication"></a>Interactieve verificatie

De meeste voorbeelden in de documentatie voor deze service gebruiken interactieve verificatie in Jupyter-notitieblokken als een eenvoudige methode voor testen en demonstratie. Dit is een lichtgewicht manier om te testen wat je bouwt. Er zijn twee functieoproepen die u automatisch vragen met een verificatiestroom op basis van de gebruikersinterface.

Als `from_config()` u de functie aanroept, wordt de prompt weergegeven.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

De `from_config()` functie zoekt naar een JSON-bestand met de verbindingsgegevens van uw werkruimte. U de verbindingsgegevens ook expliciet `Workspace` opgeven met behulp van de constructor, die ook wordt gevraagd om interactieve verificatie. Beide gesprekken zijn gelijkwaardig.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Als u toegang hebt tot meerdere tenants, moet u mogelijk de klasse importeren en expliciet definiëren welke tenant u target. Bellen naar de `InteractiveLoginAuthentication` constructeur voor zal ook vragen u om in te loggen vergelijkbaar met de bovenstaande oproepen.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Hoewel het handig is voor het testen en leren, zal interactieve verificatie u niet helpen bij het bouwen van geautomatiseerde of headless workflows. Het instellen van serviceprincipal-authenticatie is de beste aanpak voor geautomatiseerde processen die de SDK gebruiken.

## <a name="set-up-service-principal-authentication"></a>Serviceprincipal-verificatie instellen

Dit proces is nodig voor het inschakelen van verificatie die is losgekoppeld van een specifieke gebruikersaanmelding, waarmee u verifiëren naar de Azure Machine Learning Python SDK in geautomatiseerde werkstromen. Serviceprincipal-verificatie stelt u ook in staat om [te verifiëren naar de REST API.](#azure-machine-learning-rest-api-auth)

Als u servicehoofdverificatie wilt instellen, maakt u eerst een app-registratie in Azure Active Directory en verleent u vervolgens toegang tot uw app-functieop basis van uw ML-werkruimte. De eenvoudigste manier om deze installatie te voltooien, is via de [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in de Azure-portal. Nadat u zich hebt inlogt op de portal, klikt u op het `>_` pictogram rechtsboven op de pagina bij uw naam om de shell te openen.

Als u de cloudshell nog niet eerder hebt gebruikt in uw Azure-account, moet u een opslagaccountbron maken voor het opslaan van bestanden die zijn geschreven. Over het algemeen zal deze opslagrekening een verwaarloosbare maandelijkse kosten met zich meebrengen. Installeer bovendien de machine learning-extensie als u deze nog niet eerder hebt gebruikt met de volgende opdracht.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> U moet een beheerder van het abonnement zijn om de volgende stappen uit te voeren.

Voer vervolgens de volgende opdracht uit om de serviceprincipal te maken. Geef het een naam, in dit geval **ml-auth**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

De uitvoer zal een JSON vergelijkbaar met de volgende. Let op `clientId`de `clientSecret`, `tenantId` en velden, zoals je ze nodig hebt voor andere stappen in dit artikel.

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

Voer vervolgens de volgende opdracht uit om de details van `clientId` de serviceprincipal die u `--id` zojuist hebt gemaakt, met behulp van de waarde van bovenaf als invoer voor de parameter.

```azurecli-interactive
az ad sp show --id your-client-id
```

Het volgende is een vereenvoudigd voorbeeld van de JSON-uitvoer van de opdracht. Let op `objectId` het veld, want je hebt de waarde ervan nodig voor de volgende stap.

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

Gebruik vervolgens de volgende opdracht om uw serviceprincipaltoegang toe te wijzen aan uw machine learning-werkruimte. U hebt de naam van uw werkruimte `-w` en `-g` de naam van de resourcegroep nodig voor respectievelijk de parameters en parameters. Gebruik `--user` voor de `objectId` parameter de waarde van de vorige stap. Met `--role` de parameter u de toegangsrol voor de serviceprincipal instellen en in het algemeen gebruikt u **eigenaar** of **bijdrager.** Beide hebben schrijftoegang tot bestaande bronnen zoals compute clusters en datastores, maar alleen **de eigenaar** kan deze resources inrichten. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Deze aanroep levert geen uitvoer op, maar u hebt nu serviceprincipal-verificatie ingesteld voor uw werkruimte.

## <a name="authenticate-to-your-workspace"></a>Verifiëren voor uw werkruimte

Nu u serviceprincipal auth hebt ingeschakeld, u zich verifiëren naar uw werkruimte in de SDK zonder fysiek in te loggen als gebruiker. Gebruik `ServicePrincipalAuthentication` de klasseconstructor en gebruik de waarden die u uit de vorige stappen hebt gekregen als parameters. De `tenant_id` parameterkaarten `tenantId` aan `service_principal_id` van `clientId`hierboven, `service_principal_password` kaarten `clientSecret`aan, en kaarten aan .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

De `sp` variabele bevat nu een verificatieobject dat u rechtstreeks in de SDK gebruikt. In het algemeen is het een goed idee om de id's / geheimen hierboven gebruikt in de omgeving variabelen zoals weergegeven in de volgende code op te slaan.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Voor geautomatiseerde werkstromen die in Python worden uitgevoerd en de SDK voornamelijk gebruiken, u dit object in de meeste gevallen gebruiken voor uw verificatie. De volgende code wordt geverifieerd naar uw werkruimte met behulp van het auth-object dat u zojuist hebt gemaakt.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST API auth

De serviceprincipal die in de bovenstaande stappen is gemaakt, kan ook worden gebruikt om te verifiëren naar de Azure Machine Learning [REST API.](https://docs.microsoft.com/rest/api/azureml/) U gebruikt de [subsidiestroom voor](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)azure Active Directory-clientreferenties , waarmee service-to-service-oproepen voor headless-verificatie in geautomatiseerde werkstromen mogelijk zijn. De voorbeelden worden geïmplementeerd met de [ADAL-bibliotheek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) in zowel Python als Node.js, maar u ook een open-sourcebibliotheek gebruiken die OpenID Connect 1.0 ondersteunt. 

> [!NOTE]
> MSAL.js is een nieuwere bibliotheek dan ADAL, maar u geen service-to-service-verificatie doen met behulp van clientreferenties met MSAL.js, omdat het in de eerste plaats een client-side bibliotheek is die is bedoeld voor interactieve/UI-verificatie die is gekoppeld aan een specifieke gebruiker. We raden u aan ADAL te gebruiken zoals hieronder wordt weergegeven om geautomatiseerde workflows te bouwen met de REST API.

### <a name="nodejs"></a>Node.js

Gebruik de volgende stappen om een auth-token te genereren met Node.js. In uw omgeving, lopen `npm install adal-node`. Gebruik vervolgens `tenantId`uw `clientId`, `clientSecret` en van de serviceprincipal die u in de bovenstaande stappen hebt gemaakt als waarden voor de overeenkomende variabelen in het volgende script.

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

De `tokenResponse` variabele is een object dat de token en bijbehorende metagegevens bevat, zoals de vervaldatum. Tokens zijn 1 uur geldig en kunnen worden vernieuwd door hetzelfde gesprek opnieuw uit te voeren om een nieuw token op te halen. Het volgende is een voorbeeldreactie.

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

Gebruik `accessToken` de eigenschap om het auth-token op te halen. Zie de [REST API-documentatie](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) voor voorbeelden over het gebruik van het token om API-aanroepen te maken.

### <a name="python"></a>Python 

Gebruik de volgende stappen om een auth-token te genereren met Python. In uw omgeving, lopen `pip install adal`. Gebruik vervolgens `tenantId`uw `clientId`, `clientSecret` en van de serviceprincipal die u in de bovenstaande stappen hebt gemaakt als waarden voor de juiste variabelen in het volgende script.

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

De `token_response` variabele is een woordenboek met de token en bijbehorende metagegevens, zoals de vervaldatum. Tokens zijn 1 uur geldig en kunnen worden vernieuwd door hetzelfde gesprek opnieuw uit te voeren om een nieuw token op te halen. Het volgende is een voorbeeldreactie.

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

Gebruik `token_response["accessToken"]` om het auth-token op te halen. Zie de [REST API-documentatie](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) voor voorbeelden over het gebruik van het token om API-aanroepen te maken.

## <a name="web-service-authentication"></a>Webserviceverificatie

Webservices in Azure Machine Learning gebruiken een ander verificatiepatroon dan hierboven beschreven. De eenvoudigste manier om te verifiëren voor geïmplementeerde webservices is het gebruik van **key-based authenticatie**, die statische verificatiesleutels voor het type drager genereert die niet hoeven te worden vernieuwd. Als u alleen hoeft te verifiëren voor een geïmplementeerde webservice, hoeft u geen serviceprincipeverificatie in te stellen zoals hierboven wordt weergegeven.

Webservices die zijn geïmplementeerd op Azure Kubernetes Service hebben standaard basisauth *ingeschakeld.* Azure Container Instances geïmplementeerde services hebben standaard basisgebaseerde auth *uitgeschakeld,* maar u deze inschakelen door in te stellen `auth_enabled=True`bij het maken van de ACI-webservice. Het volgende is een voorbeeld van het maken van een ACI-implementatieconfiguratie met op sleutels gebaseerde auth ingeschakeld.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Vervolgens u de aangepaste ACI-configuratie gebruiken in implementatie met behulp van de `Model` klasse.

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

Gebruik . `aci_service.get_keys()` Als u een sleutel `regen_key()` wilt regenereren, gebruikt u de functie en geeft u primaire of **secundaire** gegevens **door.**

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Webservices ondersteunen ook tokengebaseerde verificatie, maar alleen voor Azure Kubernetes Service-implementaties. Zie de [how-to](how-to-consume-web-service.md) over het consumeren van webservices voor meer informatie over authenticeren.

### <a name="token-based-web-service-authentication"></a>Tokengebaseerde webserviceverificatie

Wanneer u tokenverificatie inschakelt voor een webservice, moeten gebruikers een Azure Machine Learning JSON-webtoken aan de webservice presenteren om toegang te krijgen tot een webservice. Het token verloopt na een bepaald tijdsbestek en moet worden vernieuwd om te kunnen blijven bellen.

* Tokenverificatie is **standaard uitgeschakeld** wanneer u implementeert naar Azure Kubernetes Service.
* Tokenverificatie **wordt niet ondersteund** wanneer u implementeert naar Azure Container Instances.

Als u tokenverificatie `token_auth_enabled` wilt beheren, gebruikt u de parameter wanneer u een implementatie maakt of bijwerkt.

Als tokenverificatie is ingeschakeld, kunt `get_token` u de methode gebruiken om een JSON-webtoken (JWT) en de vervaldatum van dat token op te halen:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Je moet na de `refresh_by` tijd van het token een nieuw token aanvragen. Als u tokens buiten de Python SDK moet vernieuwen, is een optie om de REST `service.get_token()` API te gebruiken met service-principal-verificatie om periodiek te bellen, zoals eerder besproken.
>
> We raden u ten zeerste aan om uw Azure Machine Learning-werkruimte te maken in dezelfde regio als uw Azure Kubernetes Service-cluster. 
>
> Als u wilt verifiëren met een token, belt de webservice naar de regio waarin uw Azure Machine Learning-werkruimte is gemaakt. Als de regio van uw werkruimte niet beschikbaar is, u geen token ophalen voor uw webservice, zelfs niet als uw cluster zich in een andere regio bevindt dan uw werkruimte. Het resultaat is dat Azure AD-verificatie niet beschikbaar is totdat het gebied van uw werkruimte weer beschikbaar is. 
>
> Hoe groter de afstand tussen de regio van uw cluster en de regio van uw werkruimte, hoe langer het duurt om een token op te halen.

## <a name="next-steps"></a>Volgende stappen

* [Een afbeeldingsclassificatiemodel trainen en implementeren](tutorial-train-models-with-aml.md).
* [Gebruik een Azure Machine Learning-model dat is geïmplementeerd als webservice.](how-to-consume-web-service.md)
