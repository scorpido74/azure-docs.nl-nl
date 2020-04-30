---
title: Een veilige webtoepassing ontwikkelen | Microsoft Docs
description: Deze eenvoudige voor beeld-app implementeert aanbevolen beveiligings procedures voor het verbeteren van uw toepassing en de beveiligings postuur van uw organisatie bij het ontwikkelen op Azure.
keywords: nb
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 730e478622da8cd90af1c559e4d0c6fd04151cca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686797"
---
# <a name="develop-a-secure-web-app"></a>Een beveiligde web-app ontwikkelen

Dit voor beeld is een eenvoudige python-app die een webpagina weergeeft met koppelingen naar beveiligings bronnen voor het ontwikkelen van apps in Azure. De app implementeert aanbevolen beveiligings procedures waarmee u uw toepassing en de beveiligings postuur van uw organisatie kunt verbeteren wanneer u apps op Azure ontwikkelt.

Volg de stappen in dit artikel sequentieel om te controleren of de toepassings onderdelen correct zijn geconfigureerd. De data base, Azure App Service, Azure Key Vault instantie en Azure-toepassing gateway-exemplaar, zijn afhankelijk van elkaar.

De implementatie scripts instellen de infra structuur. Nadat u de implementatie scripts hebt uitgevoerd, moet u een aantal hand matige configuratie in het Azure Portal uitvoeren om de onderdelen en services samen te koppelen.

De voor beeld-app is gericht op beginners ontwikkelende toepassingen op Azure die beveiligings maatregelen willen implementeren in hun toepassingen.

Bij het ontwikkelen en implementeren van deze app leert u het volgende:

- Maak een Azure Key Vault-exemplaar, sla het op en haal er geheimen van op.
- Azure Database for PostgreSQL implementeren, beveiligde wacht woorden instellen en toegang verlenen.
- Voer een alpine Linux-container uit op Azure Web Apps voor Linux en schakel beheerde identiteiten in voor Azure-resources.
- Een Azure-toepassing gateway-exemplaar maken en configureren met een firewall die gebruikmaakt van de [Rule OWASP Top 10](https://coreruleset.org/).
- Versleuteling van gegevens in door Voer en op rest inschakelen met behulp van Azure-Services.

Nadat u deze app hebt ontwikkeld en geïmplementeerd, hebt u de volgende voor beeld-web-app ingesteld, samen met de configuratie-en beveiligings maatregelen die worden beschreven.

![Voor beeld-web-app](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architectuur

De app is een typische toepassing met n-tier met drie lagen. De front-end, de back-end en de data base-laag met de bewakings-en geheim beheer onderdelen die zijn geïntegreerd, worden hier weer gegeven:

![App-architectuur](./media/secure-web-app/architecture.png)

De architectuur bestaat uit de volgende onderdelen:

- [Azure-toepassing gateway](../../application-gateway/index.yml). Biedt de gateway en Firewall voor onze toepassings architectuur.
- [Azure web apps op Linux](../../app-service/containers/app-service-linux-intro.md). Biedt de container runtime voor het uitvoeren van de python-app in een Linux-omgeving.
- [Azure Key Vault](../../key-vault/index.yml). De geheimen van onze app worden opgeslagen en versleuteld en het maken van toegangs beleid wordt beheerd.
- [Azure database for PostgreSQL](https://azure.microsoft.com/services/postgresql/). De gegevens van onze app veilig opslaan.
- [Azure Security Center](../../security-center/index.yml) en [Azure-toepassing Insights](../../azure-monitor/app/app-insights-overview.md). Voorziet in bewaking en waarschuwingen over de werking van de app.

## <a name="threat-model"></a>Bedreigings model

Bedreigings modellering is het proces van het identificeren van mogelijke beveiligings Risico's voor uw bedrijf en toepassing en zorgt ervoor dat er een goed risico op de juiste wijze wordt uitgevoerd.

In dit voor beeld wordt de [Microsoft Threat Modeling tool](threat-modeling-tool.md) gebruikt voor het implementeren van bedreigings modellen voor de beveiligde voor beeld-app. Door de componenten en de gegevens stromen te ontwikkelen, kunt u problemen en bedreigingen vroegtijdig in het ontwikkelings proces identificeren. Dit bespaart tijd en geld later.

Dit is het bedreigings model voor de voor beeld-app:

![Bedreigings model](./media/secure-web-app/threat-model.png)

Enkele voor beelden van bedreigingen en mogelijke beveiligings problemen die het hulp programma voor het maken van dreigingen worden gegenereerd, worden weer gegeven in de volgende scherm afbeelding. Het bedreigings model biedt een overzicht van de beschik bare kwets baarheid en vraagt de ontwikkel aars om te zien hoe ze de problemen kunnen oplossen.

![Bedreigings model uitvoer](./media/secure-web-app/threat-model-output.png)

SQL-injectie in de voor gaande bedreigings model uitvoer wordt bijvoorbeeld verholpen door het opschonen van gebruikers invoer en het gebruik van opgeslagen functies in Azure Database for PostgreSQL. Deze oplossing voor komt een wille keurige uitvoering van query's tijdens het lezen en schrijven van gegevens.

Ontwikkel aars verbeteren de algehele beveiliging van het systeem door elk van de bedreigingen in de uitvoer van het bedreigings model te verminderen.

## <a name="deployment"></a>Implementatie

Met de volgende opties kunt u Linux uitvoeren op Azure App Service:

- Kies een container in de lijst met vooraf ontwikkelde micro soft-containers in azure die zijn gemaakt met ondersteunende technologieën (python, Ruby, PHP, Java, node. js, .NET core).
- Gebruik een aangepaste, gebouwde container. Selecteer uw eigen container registers als bron van de installatie kopie en bouw op de vele beschik bare technologieën die ondersteuning bieden voor HTTP.

In dit voor beeld voert u het implementatie script uit dat de webapp implementeert op App Service en hoe u de resources maakt.

De app kan de verschillende implementatie modellen gebruiken die hieronder worden weer gegeven:

![Diagram van de implementatie gegevensstroom](./media/secure-web-app/deployment.png)

Er zijn veel manieren om apps te implementeren op Azure, waaronder:

- Azure Resource Manager-sjablonen
- PowerShell
- Azure CLI
- Azure Portal
- Azure DevOps

Deze toepassing gebruikt:

- [Docker](https://docs.docker.com/) om de container installatie kopieën te maken en te bouwen.
- [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) voor implementatie.
- [Docker hub](https://hub.docker.com/) als container register.

## <a name="security-considerations"></a>Beveiligingsoverwegingen

### <a name="network"></a>Netwerk

De voor beeld-app maakt gebruik van end-to-end TLS/SSL-versleuteling voor in-transit gegevens die naar en van het netwerk stromen. De gateway is geconfigureerd met een zelfondertekend certificaat.
> [!IMPORTANT]
> In deze demonstratie wordt een zelfondertekend certificaat gebruikt. In een productie omgeving moet u certificaten verkrijgen van een geverifieerde certificerings instantie (CA).

De toepassings Firewall inspecteert ook het binnenkomende verkeer en de beheerders van waarschuwingen wanneer er schadelijke verkeer wordt gedetecteerd in het netwerk verkeer.
Application Gateway verkleint de kans op bedreigingen voor DDoS en SQL-injecties die zijn gedetecteerd in het bedreigings model.

### <a name="identity"></a>Identiteit

Om u aan te melden bij de portal, gebruikt de voor beeld-app Multi-Factor Authentication voor Azure Active Directory (Azure AD)-beheerders die toegang hebben gekregen tot de resources.
De voor beeld-app gebruikt beheerde identiteiten om machtigingen te krijgen om geheimen van Azure Key Vault te lezen en op te halen, zodat de app niet nodig is om de referenties en tokens voor het lezen van de geheimen te controleren. Azure AD maakt automatisch de service-principals die de app moet lezen en wijzigt de geheimen wanneer beheerde identiteiten worden gebruikt.

Beheerde identiteiten voor Azure-resources en MFA maken het voor aanvallers moeilijker om bevoegdheden te krijgen en hun bevoegdheden in het systeem te escaleren. Deze dreiging is gewijsd in het bedreigings model.
De app maakt gebruik van OAuth, waarmee gebruikers die zijn geregistreerd in de OAuth-toepassing zich kunnen aanmelden bij de app.

### <a name="storage"></a>Storage

Gegevens in de PostgreSQL-Data Base worden automatisch op rest versleuteld door Azure Database for PostgreSQL. De data base autoriseert de App Service IP-adressen, zodat alleen de geïmplementeerde App Service web-app toegang kan krijgen tot de database bronnen met de juiste verificatie referenties.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking

De app implementeert logboek registratie met behulp van Application Insights voor het bijhouden van metrische gegevens, logboeken en uitzonde ringen die optreden. Deze logboek registratie biedt voldoende app-meta gegevens om ontwikkel aars en operationele team leden te informeren over de status van de app. Het biedt ook voldoende gegevens voor Backtrack in het geval van beveiligings incidenten.

## <a name="cost-considerations"></a>Kostenoverwegingen

Als u nog geen Azure-account hebt, kunt u een gratis versie maken. Ga naar de [pagina gratis account](https://azure.microsoft.com/free/) om aan de slag te gaan. Zie wat u kunt doen met een gratis Azure-account en ontdek welke producten gedurende 12 maanden gratis zijn.

Als u de resources in de voor beeld-app wilt implementeren met behulp van de beveiligings functies, moet u betalen voor een aantal Premium-functies. Wanneer de app wordt geschaald en de gratis lagen en tests die door Azure worden aangeboden, moeten worden bijgewerkt om te voldoen aan de toepassings vereisten, kunnen uw kosten toenemen. Gebruik de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) van Azure om uw kosten te schatten.

## <a name="deploy-the-solution"></a>De oplossing implementeren

### <a name="prerequisites"></a>Vereisten

Als u de toepassing wilt uitvoeren, moet u deze hulpprogram ma's installeren:

- Een code-editor om de toepassings code te wijzigen en weer te geven. [Visual Studio code](https://code.visualstudio.com/) is een open source-optie.
- [Azure cli](/cli/azure/install-azure-cli) op uw ontwikkel computer.
- [Git](https://git-scm.com/) op uw systeem. Git wordt gebruikt om de bron code lokaal te klonen.
- [JQ](https://stedolan.github.io/jq/), een UNIX-hulp programma voor het uitvoeren van QUERY'S in JSON op een gebruiks vriendelijke manier.

U hebt een Azure-abonnement nodig om de resources van de voor beeld-app te implementeren. Als u geen Azure-abonnement hebt, kunt u [een gratis account maken](https://azure.microsoft.com/free/) om de voor beeld-app te testen.

Na de installatie van deze hulpprogram ma's bent u klaar om de app te implementeren in Azure.

### <a name="environment-setup"></a>Configuratie van de omgeving

Voer de implementatie scripts uit om de omgeving en het abonnement in te stellen:

1. Gebruik deze git-opdracht om de opslag plaats van de bron code te klonen:

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. Als u de map wilt verplaatsen, gebruikt u deze opdracht:

   ```shell
   cd tutorial-project/scripts
   ```

3. Er zijn bestanden in de map scripts die specifiek zijn voor het platform dat u gebruikt (Windows of Linux). Als de Azure CLI al is geïnstalleerd, meldt u zich bij de opdracht prompt aan bij het Azure-account door de volgende Azure CLI-opdracht uit te voeren:

   ```azurecli-interactive
   az login
   ```

De browser wordt geopend, Meld u aan met uw referenties. Nadat u zich hebt aangemeld, kunt u beginnen met het implementeren van de resources vanaf de opdracht prompt.

De implementatie scripts `deploy-powershell.ps1` en `deploy-bash.sh` bevatten code waarmee de volledige toepassing wordt geïmplementeerd.
De oplossing implementeren:

1. Als u het `deploy-powershell.ps1` bestand in Power shell uitvoert, typt `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` u de naam van de regio en de resource groep vervangen door de juiste Azure-regio's en een naam voor de resource groep
2. Als u zich in Linux bevindt `deploy-bash.sh` `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`, voert u het bestand uit door te typen.`chmod +x deploy-bash.sh`

In de volgende voor beelden worden de fragmenten van de belangrijkste onderdelen gedemonstreerd. U kunt de voor beelden afzonderlijk of met de rest van de onderdelen implementeren door de Deploy-bestanden uit te voeren.

### <a name="implementation-guidance"></a>Implementatie richtlijnen

Het implementatie script is een script dat kan worden onderverdeeld in vier fasen. Elke fase implementeert en configureert een Azure-resource in het [architectuur diagram](#architecture).

De vier fasen zijn:

- Azure Key Vault implementeren.
- Azure Database for PostgreSQL implementeren.
- Implementeer Azure Web Apps op Linux.
- Implementeer Application Gateway met Web Application Firewall.

Elke fase bouwt voort op het bovenstaande door gebruik te maken van de configuratie van de eerder geïmplementeerde resources.

Als u de implementaties tappen wilt volt ooien, moet u ervoor zorgen dat u de hulpprogram ma's hebt geïnstalleerd die worden vermeld onder [vereisten](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Azure Key Vault implementeren

In deze sectie maakt en implementeert u een Azure Key Vault-exemplaar dat wordt gebruikt voor het opslaan van geheimen en certificaten.

Nadat u de implementatie hebt voltooid, hebt u een Azure Key Vault-exemplaar geïmplementeerd in Azure.

Azure Key Vault implementeren met behulp van Azure CLI:

1. Declareer de variabelen voor Azure Key Vault.
2. Registreer de Azure Key Vault provider.
3. Maak de resource groep voor het exemplaar.
4. Maak het Azure Key Vault-exemplaar in de resource groep die u in stap 3 hebt gemaakt.

   ```powershell-interactive

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```

Het is een best practice voor het gebruik van beheerde identiteiten voor Azure-resources in apps die gebruikmaken van Key Vault om toegang te krijgen tot bronnen. Uw beveiligings postuur neemt toe wanneer toegangs sleutels voor Key Vault niet worden opgeslagen in code of in configuratie.

#### <a name="deploy-azure-database-for-postgresql"></a>Azure Database for PostgreSQL implementeren

Azure Database for PostgreSQL op de volgende manier werkt, maakt u eerst de database server en maakt u vervolgens de data base waarop u het schema en de gegevens wilt opslaan.

Nadat u de implementatie hebt voltooid, hebt u een PostgreSQL-server en-data base die wordt uitgevoerd op Azure.

Azure Database for PostgreSQL implementeren met behulp van Azure CLI:

1. Open een Terminal met Azure CLI en de installatie van uw Azure-abonnement.
2. Genereer een veilige combi natie van gebruikers naam en wacht woord die wordt gebruikt voor toegang tot de data base. (Deze moeten worden opgeslagen in Azure Key Vault voor apps die deze gebruiken.)
3. Maak het PostgreSQL-Server exemplaar.
4. Maak een Data Base op het Server exemplaar dat u in stap 3 hebt gemaakt.
5. Voer PostgreSQL-scripts uit op het PostgreSQL-exemplaar.

De onderstaande code is afhankelijk van de PGUSERNAME-en PGPASSWORD-geheimen die zijn opgeslagen in azure-sleutel kluis van de bovenstaande stap voor het implementeren van de sleutel kluis.

   ```powershell-interactive
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

Nadat u de Data Base hebt geïmplementeerd, moet u de referenties en connection string opslaan in Azure Key Vault.
In de map scripts bevindt zich `functions.sql` een bestand met de PL/pgSQL-code die opgeslagen functies maakt wanneer u deze uitvoert. Het uitvoeren van dit bestand parameterizes de invoer om SQL-injectie te beperken.

PostgreSQL is gebundeld met een `psql` hulp programma dat wordt gebruikt om verbinding te maken met de data base. Als u `functions.sql`wilt uitvoeren, moet u verbinding maken met het Azure database for PostgreSQL exemplaar van uw lokale computer en het van daaruit uitvoeren. De installatie van het hulp programma psql is opgenomen in de standaard installatie voor PostgreSQL op elk besturings systeem.
Zie de [psql-documentatie](https://www.postgresql.org/docs/9.3/app-psql.html)voor meer informatie.

Azure Cloud Shell ook het `psql` hulp programma bevat. U kunt Cloud Shell rechtstreeks vanuit de Azure Portal gebruiken door het pictogram Cloud Shell te selecteren.

Als u externe toegang tot het PostgreSQL-exemplaar wilt inschakelen, moet u het IP-adres in PostgreSQL autoriseren.
U schakelt deze toegang in door naar het tabblad **verbindings beveiliging** te gaan en **client-IP toevoegen**te selecteren en de nieuwe instellingen op te slaan.

![IP-adres van client autoriseren](./media/secure-web-app/add-client-ip-postgres.png)

Als u Cloud Shell gebruikt in plaats van het lokale psql-hulp programma, selecteert u **toegang tot Azure-Services toestaan** en wijzigt u de waarde in **ON** om uw Cloud shell toegang toe te staan.

Maak vervolgens verbinding met het exemplaar door de onderstaande opdracht psql uit te voeren met connection string para meters van het tabblad **verbindings reeksen** van het postgresql-exemplaar op de Azure Portal.
Vervang de lege accolades door para meters van de Blade verbindingsteken reeks van de data base en het wacht woord met het wacht woord van Azure Key Vault.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Voer het volgende PL/pgSQL-script uit nadat u hebt gecontroleerd of u verbinding hebt met de data base. Het script maakt de opgeslagen functies die worden gebruikt om gegevens in de data base in te voegen.

```shell
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;

CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```

Zie [TLS-connectiviteit configureren in azure database for PostgreSQL](/azure/postgresql/concepts-ssl-connection-security)voor meer informatie over het instellen van TLS en verificatie van de certificerings instantie (CA) voor postgresql.

Een basis certificaat is opgenomen in de container. De stappen voor het verkrijgen van het certificaat zijn:

1. Down load het certificaat bestand van de [certificerings instantie](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Down load en installeer openssl op uw computer](/azure/postgresql/concepts-ssl-connection-security).
3. Uw certificaat bestand decoderen:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Meer informatie over het configureren van TLS-beveiliging voor PostgreSQL hier [configureren TLS-verbindings beveiliging](/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Azure Web Apps implementeren in Linux

U kunt eenvoudig Linux-Services maken boven op Azure App Service, omdat Azure een reeks vooraf ontwikkelde containers en installatie kopieën biedt voor veelgebruikte talen als Python, Ruby, C# en Java. Azure biedt ook ondersteuning voor aangepaste containers, waardoor vrijwel alle programmeer talen kunnen worden uitgevoerd op het Azure App Service platform.

De app die wordt geïmplementeerd, is een eenvoudige python-app die wordt uitgevoerd op de nieuwste Ubuntu Linux-distributie. Het maakt verbinding met de Azure Key Vault-en PostgreSQL-instanties die in de voor gaande secties zijn gemaakt, respectievelijk referentie beheer en gegevens opslag.

Het volgende docker-bestand bevindt zich in de hoofdmap van de app:

```dockerfile
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

De Dockerfile hierboven wordt gebruikt voor het bouwen van de container die wordt gehost op de `mcr.microsoft.com/samples/basic-linux-app`Azure container Registry op.

De onderstaande code:

1. Declareert de variabelen en namen voor het App Service exemplaar.
2. Hiermee maakt u de resource groep voor het App Service plan.
3. Voorziet in een Azure Web Apps-exemplaar voor Linux-containers.
4. Hiermee schakelt u de logboek registratie in voor de web-app-container.
5. Hiermee stelt u enkele app-configuraties in de app-instellingen van de container in.

   ```powershell-interactive
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }
   ```

Met dit script maakt u een toegewezen identiteit voor het App Service-exemplaar dat kan worden gebruikt met MSI om te communiceren met Azure Key Vault zonder geheimen voor vaste code ring in code of configuratie.

Ga naar het Azure Key Vault-exemplaar in de portal om de toegewezen identiteit te autoriseren op het tabblad toegangs beleid. Selecteer **nieuw toegangs beleid toevoegen**. Zoek onder **Principal selecteren**naar de toepassings naam die lijkt op de naam van het app service exemplaar dat is gemaakt.
Een service-principal die aan de toepassing is gekoppeld, moet zichtbaar zijn. Selecteer de pagina en sla het toegangs beleid op, zoals wordt weer gegeven in de volgende scherm afbeelding.

Omdat de toepassing alleen sleutels hoeft op te halen, selecteert u de machtiging **Get** in de geheimen opties, waardoor toegang is toegestaan terwijl de verleende bevoegdheden worden verminderd.

![Toegangs beleid Key Vault](./media/secure-web-app/kv-access-policy.png)

*Een Key Vault toegangs beleid maken*

Sla het toegangs beleid op en sla de nieuwe wijziging op het tabblad **toegangs beleid** op om de beleids regels bij te werken.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Application Gateway implementeren met Web Application Firewall ingeschakeld

In web apps is het niet raadzaam om services rechtstreeks aan de buiten wereld op internet beschikbaar te stellen.
Taak verdeling en firewall regels bieden meer beveiliging en controle over het binnenkomende verkeer en helpen u het te beheren.

Een Application Gateway-exemplaar implementeren:

1. Maak de resource groep om de toepassings gateway te maken.
2. Richt een virtueel netwerk in om aan de gateway te koppelen.
3. Maak een subnet voor de gateway in het virtuele netwerk.
4. Richt een openbaar IP-adres in.
5. Richt de toepassings gateway in.
6. Schakel Web Application Firewall in op de gateway.

   ```powershell-interactive
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

Het voor gaande script:

1. Hiermee maakt u een nieuw zelfondertekend certificaat in Azure.
2. Hiermee wordt het zelfondertekende certificaat gedownload als een base64-gecodeerd bestand.
3. Hiermee genereert u een wacht woord voor het zelfondertekende certificaat.
4. Exporteert het certificaat als een PFX-bestand dat is ondertekend met het wacht woord.
5. Hiermee wordt het wacht woord van het certificaat opgeslagen in Azure Key Vault.

In deze sectie wordt de toepassings gateway geïmplementeerd:

```powershell-interactive
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

Nadat u de implementatie hebt voltooid, hebt u een toepassings gateway met Web Application Firewall ingeschakeld.

Het gateway-exemplaar geeft poort 443 voor HTTPS weer. Deze configuratie zorgt ervoor dat onze app alleen toegankelijk is via poort 443 via HTTPS.

Het blok keren van ongebruikte poorten is een beveiligings best practice.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Netwerk beveiligings groepen toevoegen aan het App Service-exemplaar

App Service-exemplaren kunnen worden geïntegreerd met virtuele netwerken. Met deze integratie kunnen ze worden geconfigureerd met beleids regels voor netwerk beveiligings groepen die het binnenkomende en uitgaande verkeer van de app beheren.

1. Als u deze functie wilt inschakelen, selecteert u op de Blade Azure-app service-exemplaar onder **instellingen**de optie **netwerken**. Selecteer in het rechterdeel venster onder **VNet-integratie**de optie **Klik hier om te configureren**.

   ![Nieuwe integratie van virtueel netwerk](./media/secure-web-app/app-vnet-menu.png)

    *Nieuwe integratie van virtuele netwerken voor App Service*

1. Selecteer op de volgende pagina **VNET toevoegen (preview)**.

1. Selecteer in het volgende menu het virtuele netwerk dat u hebt gemaakt in de implementatie die `hello-vnet`begint met. U kunt een nieuw subnet maken of een bestaande selecteren.
   In dit geval maakt u een nieuw subnet. Stel het **adres bereik** in op **10.0.3.0/24** en noem het subnet **app-subnet**.

   ![Configuratie van virtuele netwerk App Service](./media/secure-web-app/app-vnet-config.png)

    *Configuratie van het virtuele netwerk voor App Service*

Nu u de integratie van het virtuele netwerk hebt ingeschakeld, kunt u netwerk beveiligings groepen toevoegen aan de app.

1. Zoek in het zoekvak naar **netwerk beveiligings groepen**. Selecteer **netwerk beveiligings groepen** in de resultaten.

    ![Zoeken naar netwerk beveiligings groepen](./media/secure-web-app/nsg-search-menu.png)

    *Zoeken naar netwerk beveiligings groepen*

2. Selecteer in het volgende menu de optie **toevoegen**. Voer de **naam** in van de NSG en de **resource groep** waarin deze zich bevindt. Deze NSG wordt toegepast op het subnet van de toepassings gateway.

    ![Een NSG maken](./media/secure-web-app/nsg-create-new.png)

    *Een NSG maken*

3. Nadat de NSG is gemaakt, selecteert u deze. Selecteer in de Blade onder **instellingen**de optie **regels voor inkomende beveiliging**. Configureer deze instellingen om verbindingen toe te staan in de toepassings gateway via poort 443.

   ![De NSG configureren](./media/secure-web-app/nsg-gateway-config.png)

   *De NSG configureren*

4. Voeg in de uitgaande regels voor de gateway NSG een regel toe waarmee uitgaande verbindingen met het App Service-exemplaar worden toegestaan door een regel te maken die de `AppService`servicetag van de service bedoelt:

   ![Uitgaande regels voor de NSG toevoegen](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Uitgaande regels voor de NSG toevoegen*

    Voeg nog een regel voor uitgaande verbindingen toe zodat de gateway uitgaande regels kan verzenden naar een virtueel netwerk.

   ![Een andere uitgaande regel toevoegen](./media/secure-web-app/nsg-outbound-vnet.png)

    *Een andere uitgaande regel toevoegen*

5. Selecteer **koppelen**op de Blade subnetten van de NSG, selecteer het virtuele netwerk dat u in de implementatie hebt gemaakt en selecteer het gateway-subnet met de naam **gw-subnet**. De NSG wordt toegepast op het subnet.

6. Maak een andere NSG, zoals in de vorige stap, de tijd voor de App Service-instantie. Geef het een naam. Voeg de regel voor binnenkomende verbindingen toe voor poort 443, zoals u dat voor de Application Gateway NSG hebt gedaan.

   Als er een App Service-exemplaar is geïmplementeerd op een App Service Environment exemplaar, wat niet het geval is voor deze app, kunt u regels voor binnenkomend verkeer toevoegen om Azure Service Health tests toe te staan door poorten 454-455 te openen in de binnenkomende beveiligings groepen van uw App Service NSG. Hier is de configuratie:

   ![Regels voor Azure Service Health tests toevoegen](./media/secure-web-app/nsg-create-healthprobes.png)

    *Regels voor Azure Service Health tests toevoegen (alleen App Service Environment)*

7. Maak in de regels voor uitgaande beveiliging een nieuwe regel voor uitgaande beveiliging waarmee het App Service exemplaar kan communiceren met de PostgreSQL-data base. Configureer deze als volgt:

   ![Regel voor het toestaan van uitgaande PostgreSQL-verbindingen](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Een regel toevoegen voor het toestaan van uitgaande PostgreSQL-verbindingen*

Als u de kwets baarheid wilt beperken, wijzigt u de App Service netwerk instellingen zodanig dat alleen de toepassings gateway toegang heeft tot de toepassing.
Hiertoe gaat u naar het tabblad App Service netwerk, selecteert u het tabblad **IP-beperkingen** en maakt u een regel voor toestaan waarmee alleen het IP-adres van de toepassings gateway rechtstreeks toegang heeft tot de service.

U kunt het IP-adres van de gateway ophalen van de pagina overzicht. Geef op het tabblad **IP-adres CIDR** het IP-adres op in de `<GATEWAY_IP_ADDRESS>/32`volgende indeling:.

![Alleen de gateway toestaan](./media/secure-web-app/app-allow-gw-only.png)

*Alleen het gateway-IP-adres toegang geven tot de App Service*

#### <a name="implement-azure-active-directory-oauth"></a>Azure Active Directory OAuth implementeren

De Azure-documenten die worden gedistribueerd op de pagina voor beeld-web-app, zijn resources in onze app die mogelijk bescherming nodig hebben. U kunt Azure Active Directory (Azure AD) gebruiken om verificatie voor web-, desktop-en Mobile-apps te implementeren met behulp van verschillende verificatie stromen.
De app maakt gebruik **van aanmelden bij micro soft**, waarmee de app profielen kan lezen van gebruikers die zijn toegevoegd aan de lijst met één Tenant van Azure AD-gebruikers.

Configureer in de Azure Portal de app zodanig dat de vereiste referenties worden gebruikt:

1. Selecteer **Azure Active Directory**of zoek ernaar door middel van het zoekvak.

2. Selecteer **nieuwe registratie**:

   ![Een registratie maken](./media/secure-web-app/ad-auth-create.png)

   *Een Azure AD-App-registratie maken*

3. Voer op de volgende pagina de naam van de app in. Onder **ondersteunde account typen**selecteert u **alleen accounts in deze organisatie Directory**.
    Onder **omleidings-URI**voert u het basis domein in waarop de app wordt uitgevoerd plus een met het token eindpunt. Bijvoorbeeld: *GATEWAY_HASH*. cloudapp.net/token.

   ![Azure AD-App-registratie configureren](./media/secure-web-app/ad-auth-type.png)

   *Azure AD-App-registratie configureren*

4. Er wordt een scherm weer gegeven waarin de geregistreerde app en de bijbehorende informatie worden weer gegeven. U moet deze gegevens toevoegen aan het Azure Key Vault-exemplaar.
   1. Kopieer de client-ID en sla deze op in Key Vault als `CLIENTID`.
   2. Kopieer de omleidings-URI die u in de vorige stap hebt ingevoerd `REDIRECTURI`en sla deze op als.
   3. Kopieer de naam van de standaardmap van Azure AD, met de indelings *naam*. microsoftonline.com, en sla deze op `TENANT`in Key Vault als.
   4. Ga naar het tabblad **certificaten & geheimen** van de Azure AD-app die u eerder hebt gemaakt en selecteer **Nieuw client geheim**, zoals wordt weer gegeven in de volgende scherm afbeelding. Stel een verval datum in en kopieer de gegenereerde waarde en sla deze op Key Vault als `CLIENTSECRET`.

      ![Azure AD-autorisatie geheim](./media/secure-web-app/ad-auth-secrets.png)

      *Azure AD-autorisatie geheim*

   5. Genereer een veilige wille keurige geheime sleutel met behulp van een opdracht regel/online hulp programma. Sla het op in Key Vault `FLASKSECRETKEY`als. Het toepassings raamwerk gebruikt deze sleutel om sessies te maken.
        Zie [fles-sessies](http://flask.pocoo.org/docs/1.0/quickstart/#sessions)voor meer informatie over het genereren van een geheime sleutel.

5. Nadat u de aanmelding hebt geconfigureerd, moet u gebruikers toevoegen aan de Azure AD-koppeling zodat ze zich kunnen aanmelden bij de bron. Als u deze wilt toevoegen, gaat u naar het tabblad **gebruikers** in azure AD, selecteert u **alle gebruikers**en selecteert u vervolgens **nieuwe gebruiker** of **nieuwe gast gebruiker**. Voor het testen kunt u een gast gebruiker toevoegen en de gebruiker uitnodigen voor de Directory. U kunt ook een nieuwe gebruiker toevoegen als het domein waarop de app wordt uitgevoerd, is gevalideerd. In dit voor beeld kunnen alleen gebruikers die zijn geregistreerd in de Azure AD-Tenant, worden geregistreerd voor toegang. Raadpleeg de documentatie voor meer informatie over de toegang tot multi tenants.

   ![Gebruikers toevoegen aan het standaard domein](./media/secure-web-app/ad-auth-add-user.png)

   *Gebruikers toevoegen aan het standaard Azure Active Directory domein*

Nadat u de Azure AD-configuratie en geheimen aan Key Vault hebt toegevoegd, kunnen gebruikers worden geverifieerd in de app met behulp van Azure OAuth-verificatie.
In de app-code wordt dit verwerkt door de Azure Active Directory Authentication Library (ADAL).

Nadat de geheimen zich in Key Vault bevinden en de toepassing toegang heeft tot de geheimen en de data base, kan de toepassings service worden bereikt via de toepassings-URL van\/de gateway (https:/GATEWAY_HASH. cloudapp. net), die u vanaf de Blade kunt ophalen.

Als u zich bij Azure AD aanmeldt, krijgt u een fout bericht ' gebruiker is niet geregistreerd in de directory waarin u zich probeert aan te melden ', moet u de gebruiker toevoegen. Als u de gebruiker wilt toevoegen, gaat u naar het tabblad **gebruikers** van Azure AD en voegt u de gebruiker hand matig toe door hun gegevens in te voeren of door de gebruiker uit te nodigen door hun e-mail adres als gast gebruiker in te voeren op Azure AD in de Blade **gasten uitnodigen** .

#### <a name="deploy-application-insights"></a>Application Insights implementeren
Nu de app is geïmplementeerd en werkt, moet u fouten in de app afhandelen, samen met de gegevens verzameling logboek registratie en tracering.
De gegevens verzameling logboek registratie en tracering biedt een overzicht van de controle gebeurtenissen die in de app plaatsvinden.

Application Insights is een service waarmee logboeken worden verzameld die kunnen worden gegenereerd door gebruikers of het systeem.

Een Application Insights-exemplaar maken:

1. Zoek naar **Application Insights** met behulp van het zoekvak in de Azure Portal.
2. Selecteer **Application Insights**. Geef de details op die hier worden weer gegeven om een exemplaar te maken.

   ![Een Application Insights-exemplaar maken](./media/secure-web-app/app-insights-data.png)

Nadat de implementatie is voltooid, hebt u een Application Insights-exemplaar.

Nadat u het Application Insights-exemplaar hebt gemaakt, moet u de app op de hoogte stellen van de instrumentatie sleutel waarmee logboeken naar de Cloud kunnen worden verzonden. U doet dit door de Application Insights sleutel op te halen en deze te gebruiken in de toepassings bibliotheken die Azure biedt voor Application Insights. Het best practice is om sleutels en geheimen op te slaan in Azure Key Vault om ze te beveiligen.

Nadat u het Application Insights-exemplaar hebt gemaakt, moet u de app op de hoogte stellen van de instrumentatie sleutel waarmee logboeken naar de Cloud kunnen worden verzonden, voor de basis voorbeeld-app.
Stel in Key Vault een `APPINSIGHTSKEY` geheim in en stel de waarde ervan in als de instrumentatie sleutel. Hiermee kan de app logboeken en metrische gegevens naar Application Insights verzenden.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Multi-Factor Authentication implementeren voor Azure Active Directory

Beheerders moeten ervoor zorgen dat de abonnements accounts in de portal zijn beveiligd. Het abonnement is kwetsbaar voor aanvallen omdat het de resources beheert die u hebt gemaakt. Als u het abonnement wilt beveiligen, schakelt u Multi-Factor Authentication in op het tabblad **Azure Active Directory** van het abonnement.

Azure AD werkt op basis van beleids regels die worden toegepast op een gebruiker of groepen gebruikers die aan een bepaald criterium voldoen.
Er wordt door Azure een standaard beleid gemaakt om aan te geven dat beheerders twee ledige verificatie nodig hebben om zich aan te melden bij de portal.
Nadat u dit beleid hebt ingeschakeld, wordt u mogelijk gevraagd om u af te melden en weer aan te melden bij de Azure Portal.

MFA inschakelen voor beheerders aanmeldingen:

1. Ga naar het tabblad **Azure Active Directory** in het Azure Portal
2. Onder de categorie beveiliging selecteert u voorwaardelijke toegang. U ziet dit scherm:

   ![Voorwaardelijke toegang-beleids regels](./media/secure-web-app/ad-mfa-conditional-add.png)

Als u geen nieuw beleid kunt maken:

1. Ga naar het tabblad **MFA** .
2. Selecteer de koppeling **gratis proef versie** van Azure AD Premium om u te abonneren op de gratis proef versie.

   ![Azure AD Premium gratis proef versie](./media/secure-web-app/ad-trial-premium.png)

Ga terug naar het scherm voorwaardelijke toegang.

1. Selecteer het tabblad Nieuw beleid.
2. Voer de naam van het beleid in.
3. Selecteer de gebruikers of groepen waarvoor u MFA wilt inschakelen.
4. Selecteer onder **besturings elementen voor toegang**het tabblad **Grant** en selecteer vervolgens **multi-factor Authentication vereisen** (en andere instellingen indien gewenst).

   ![MFA vereisen](./media/secure-web-app/ad-mfa-conditional-add.png)

U kunt het beleid inschakelen door het selectie vakje boven aan het scherm te selecteren of op het tabblad **voorwaardelijke toegang** . Wanneer het beleid is ingeschakeld, hebben gebruikers MFA nodig om zich aan te melden bij de portal.

Er is een basislijn beleid waarvoor MFA is vereist voor alle Azure-beheerders. U kunt deze direct inschakelen in de portal. Als u dit beleid inschakelt, wordt de huidige sessie mogelijk ongeldig en wordt u afgedwongen om u opnieuw aan te melden.

Als het basislijn beleid niet is ingeschakeld:

1. Selecteer **MFA vereisen voor beheerders**.
2. Selecteer **beleid direct gebruiken**.

   ![Selecteer beleid direct gebruiken](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Azure Sentinel gebruiken om apps en resources te bewaken

Naarmate een toepassing groeit, is het lastig om alle beveiligings signalen en metrische gegevens die van resources zijn ontvangen samen te voegen en ze nuttig te maken op een op actie gerichte manier.

Azure Sentinel is ontworpen voor het verzamelen van gegevens, het detecteren van mogelijke soorten bedreigingen en het geven van inzicht in beveiligings incidenten.
Hoewel er wordt gewacht op hand matige interventie, kan Azure Sentinel afhankelijk zijn van vooraf geschreven playbooks om waarschuwingen en incident beheer processen te starten.

De voor beeld-app bestaat uit verschillende bronnen die door Azure Sentinel kunnen worden bewaakt.
Als u Azure Sentinel wilt instellen, moet u eerst een Log Analytics-werk ruimte maken waarin alle verzamelde gegevens van de verschillende bronnen worden opgeslagen.

Deze werk ruimte maken:

1. Zoek in het zoekvak in het Azure Portal naar **log Analytics**. Selecteer **log Analytics-werk ruimten**.

   ![Zoeken naar Log Analytics-werk ruimten](./media/secure-web-app/sentinel-log-analytics.png)

    *Zoeken naar Log Analytics-werk ruimten*

2. Selecteer op de volgende pagina **toevoegen** en geef vervolgens een naam, resource groep en locatie op voor de werk ruimte.
   ![Een Log Analytics-werkruimte maken](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Een Log Analytics-werkruimte maken*

3. Gebruik het zoekvak om te zoeken naar **Azure Sentinel**.

   ![Zoeken naar Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *Zoeken naar Azure Sentinel*

4. Selecteer **toevoegen** en selecteer vervolgens de log Analytics werk ruimte die u eerder hebt gemaakt.

   ![Een Log Analytics-werk ruimte toevoegen](./media/secure-web-app/sentinel-workspace-add.png)

    *Een Log Analytics-werk ruimte toevoegen*

5. Op de **Azure Sentinel-data connectors-** pagina onder **configuratie**selecteert u **gegevens connectors**. U ziet een matrix met Azure-Services die u kunt koppelen aan het Log Analytics Storage-exemplaar voor analyse in azure Sentinel.

   ![Log Analytics gegevens connectors](./media/secure-web-app/sentinel-connectors.png)

    *Een gegevens connector toevoegen aan Azure Sentinel*

   Als u bijvoorbeeld verbinding wilt maken met de toepassings gateway, voert u de volgende stappen uit:

   1. Open de Blade Azure-toepassing gateway-exemplaar.
   2. Selecteer **Diagnostische instellingen** onder **Controle**.
   3. Selecteer **Diagnostische instelling toevoegen**.

      ![Application Gateway diagnostische gegevens toevoegen](./media/secure-web-app/sentinel-gateway-connector.png)

      *Application Gateway diagnostische gegevens toevoegen*

   4. Selecteer op de pagina **Diagnostische instellingen** de log Analytics werk ruimte die u hebt gemaakt en selecteer vervolgens alle metrische gegevens die u wilt verzamelen en verzenden naar Azure Sentinel. Selecteer **Opslaan**.

        ![Instellingen voor de Azure Sentinel connector](./media/secure-web-app/sentinel-connector-settings.png)

        *Instellingen voor de Azure Sentinel connector*

  De metrische gegevens van de resource bevinden zich in azure Sentinel, waar u query's kunt uitvoeren en onderzoeken.

   Voeg dezelfde metrische gegevens toe aan de diagnostische instellingen voor Azure Key Vault, het open bare IP-adres, de Azure Database for PostgreSQL en alle services die Diagnostische logboeken in uw account ondersteunen.

Nadat u de metrieken hebt ingesteld, heeft Azure Sentinel gegevens die moeten worden geanalyseerd.

## <a name="evaluate-and-verify"></a>Evalueren en verifiëren

Nadat u de architectuur hebt ontwikkeld en geïmplementeerd, moet u ervoor zorgen dat de code en de geïmplementeerde services voldoen aan de beveiligings normen. Dit zijn enkele stappen die u kunt nemen om de software te controleren:

- Analyse van statische code
- Scannen van beveiligings problemen
- Beveiligings problemen in toepassings afhankelijkheden zoeken en oplossen

Dit zijn de basis bouwstenen voor aanbevolen procedures voor veilige ontwikkeling.

### <a name="static-code-analysis"></a>Analyse van statische code

Voor de voor beeld-app heeft verificatie met statische analyse hulpprogramma's betrekking op het vinden van beveiligings problemen in de app-code met behulp van technieken als Taint-controle en gegevens stroom analyse. Met de hulpprogram ma's voor statische analyse van python hebt u meer zekerheid over de beveiliging van uw app.

**Linten**

PyFlakes, een python-lint bibliotheek, helpt u bij het verwijderen van dode code en ongebruikte functies uit apps, zoals hier wordt weer gegeven:

![PyFlakes](./media/secure-web-app/pyflakes.png)

Het maken van linten biedt hints en mogelijke wijzigingen die uw code kunnen opschonen en minder fout gevoelig tijdens runtime.

**PyLint**

PyLint heeft de hoogste waarde voor dit project gegeven. Het programma voert code-standaard controles, fout controle en herstructureer tips uit om ervoor te zorgen dat de code die op de server wordt uitgevoerd, veilig is. Door PyLint te gebruiken om uw code bij te werken, kunt u fouten elimineren en de PyLint-classificatie verbeteren, zoals in de volgende afbeeldingen wordt weer gegeven.

![Vóór PyLint](./media/secure-web-app/before-pylint.png)

*Vóór PyLint*

Nadat u een aantal van de code fouten hebt opgelost die zijn gevonden door de hulpprogram ma's, weet u zeker dat de code niet gevoelig is voor fouten. Het oplossen van de fouten vermindert de beveiligings Risico's die zich kunnen voordoen wanneer de code wordt geïmplementeerd in productie omgevingen.

![Na pylint](./media/secure-web-app/after-pylint.png)

*Na PyLint*

### <a name="vulnerability-scanning"></a>Scannen van beveiligings problemen

Het ZAP-hulp programma [van OWASP](https://www.zaproxy.org/) is een open-source beveiligings scanner voor webtoepassingen die u kunt gebruiken om de voor beeld-app te controleren op beveiligings problemen. Als het hulp programma wordt uitgevoerd op de voor beeld-app, worden er mogelijk fouten en aanvals vectoren gemeld.

![ZAP-hulp programma](./media/secure-web-app/zap-tool.png)

*ZAP-hulp programma*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Beveiligings problemen in app-afhankelijkheden detecteren en oplossen

U kunt [de afhankelijkheids controle van OWASP](https://owasp.org/www-project-dependency-check/)gebruiken om toepassings afhankelijkheden te vinden en op te lossen.

Beveiliging is een soort gelijke toepassing die afhankelijkheden controleert. U kunt het vinden op [github](https://github.com/pyupio/safety). Veiligheids scans voor beveiligings problemen gevonden in bekende beveiligings databases.

![Schadelijk](./media/secure-web-app/pysafety.png)

*Schadelijk*

## <a name="next-steps"></a>Volgende stappen

De volgende artikelen kunnen u helpen bij het ontwerpen, ontwikkelen en implementeren van beveiligde toepassingen.

- [Ontwerpen](secure-design.md)
- [Ontwikkelen](secure-develop.md)
- [Implementeren](secure-deploy.md)
