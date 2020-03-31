---
title: Azure Spring Cloud CI/CD met GitHub-acties
description: Ci/cd-werkstroom opbouwen voor Azure Spring Cloud met GitHub-acties
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 559c894a2212466761de820de7486ae203337802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538461"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD met GitHub-acties

GitHub Actions ondersteunen een workflow voor de levenscyclus van geautomatiseerde softwareontwikkeling. Met GitHub Actions for Azure Spring Cloud u werkstromen maken in uw repository om te bouwen, testen, verpakken, vrijgeven en implementeren naar Azure. 

## <a name="prerequisites"></a>Vereisten
Dit voorbeeld vereist de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="set-up-github-repository-and-authenticate"></a>GitHub-repository instellen en verifiëren
U hebt een Azure-serviceprincipereferentie nodig om Azure-inlogactie te autoriseren. Voer de volgende opdrachten uit op uw lokale machine om een Azure-referentie op te halen:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Als u toegang wilt krijgen tot een specifieke resourcegroep, u het bereik verkleinen:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
De opdracht moet een JSON-object uitvoeren:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

In dit voorbeeld wordt het voorbeeld [van Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) op GitHub gebruikt.  Fork the sample, open gitHub repository pagina, en klik op **Instellingen** tabblad. Open **Geheimen** menu, en klik op **Een nieuw geheim toevoegen:**

 ![Nieuw geheim toevoegen](./media/github-actions/actions1.png)

Stel de geheime `AZURE_CREDENTIALS` naam in en de waarde ervan in op de JSON-tekenreeks die u hebt gevonden onder het kopje *Uw GitHub-opslagplaats instellen en verifiëren*.

 ![Geheime gegevens instellen](./media/github-actions/actions2.png)

U de Azure-inloggegevens ook ophalen uit Key Vault in GitHub-acties, zoals uitgelegd in [Azure Spring verifiëren met Key Vault in GitHub-acties.](./spring-cloud-github-actions-key-vault.md)

## <a name="provision-service-instance"></a>Instantie van de voorzieningsservice
Als u uw Azure Spring Cloud-serviceinstantie wilt inrichten, voert u de volgende opdrachten uit met de Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>De werkstroom bouwen
De werkstroom wordt gedefinieerd met de volgende opties.

### <a name="prepare-for-deployment-with-azure-cli"></a>Voorbereiden op implementatie met Azure CLI
De `az spring-cloud app create` opdracht is momenteel niet idempotent.  We raden deze werkstroom aan voor bestaande Azure Spring Cloud-apps en -exemplaren.

Gebruik de volgende Azure CLI-opdrachten ter voorbereiding:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Rechtstreeks implementeren met Azure CLI
Maak `.github/workflow/main.yml` het bestand in de opslagplaats:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Implementeren met Azure CLI-actie
De `run` opdracht AZ gebruikt de nieuwste versie van Azure CLI. Als er wijzigingen zijn, u ook een specifieke versie `action`van Azure CLI gebruiken met azure/CLI. 

> [!Note] 
> Deze opdracht wordt uitgevoerd in `env` een nieuwe container, dus werkt niet en toegang tot cross-actionbestanden kan extra beperkingen hebben.

Maak het .github/workflow/main.yml-bestand in de repository:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Implementeren met Maven-plug-in
Een andere optie is om de [Maven Plugin](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) te gebruiken voor het implementeren van de Jar en het bijwerken van app-instellingen. De `mvn azure-spring-cloud:deploy` opdracht is idempotent en maakt automatisch Apps indien nodig. U hoeft niet van tevoren overeenkomstige apps te maken.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>De werkstroom uitvoeren
**GitHub-acties** moeten automatisch worden ingeschakeld `.github/workflow/main.yml` nadat u naar GitHub bent geduwd. De actie wordt geactiveerd wanneer je een nieuwe commit pusht. Als u dit bestand in de browser maakt, moet uw actie al zijn uitgevoerd.

Als u wilt controleren of de actie is ingeschakeld, klikt u op het tabblad **Acties** op de pagina GitHub-opslagplaats:

 ![Actie controleren ingeschakeld](./media/github-actions/actions3.png)

Als uw actie bijvoorbeeld fout wordt uitgevoerd als u de Azure-referentie niet hebt ingesteld, u de controles opnieuw uitvoeren nadat de fout is opgelost. Klik op de pagina GitHub-opslagplaats op **Acties,** selecteer de specifieke werkstroomtaak en klik vervolgens op de knop **Controles opnieuw uitvoeren** om controles opnieuw uit te voeren:

 ![Controles opnieuw uitvoeren](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Volgende stappen
* [Key Vault voor Spring Cloud GitHub-acties](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory-serviceprincipals](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [GitHub-acties voor Azure](https://github.com/Azure/actions/)
