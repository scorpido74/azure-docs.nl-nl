---
title: Een veilige webapplicatie ontwikkelen | Microsoft Documenten
description: Deze eenvoudige voorbeeld-app implementeert best practices voor beveiliging die uw toepassing en de beveiligingshouding van uw organisatie verbeteren wanneer u zich ontwikkelt op Azure.
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686797"
---
# <a name="develop-a-secure-web-app"></a>Een beveiligde web-app ontwikkelen

Dit voorbeeld is een eenvoudige Python-app die een webpagina weergeeft met koppelingen naar beveiligingsbronnen voor het ontwikkelen van apps op Azure. De app implementeert best practices voor beveiliging die uw toepassing en de beveiligingshouding van uw organisatie kunnen verbeteren wanneer u apps op Azure ontwikkelt.

Volg de in dit artikel beschreven stappen achtereenvolgens om ervoor te zorgen dat de toepassingsonderdelen correct zijn geconfigureerd. De database, Azure App Service, Azure Key Vault-instantie en azure application gateway-instantie zijn van elkaar afhankelijk.

De implementatiescripts stellen de infrastructuur in. Nadat u de implementatiescripts hebt uitgevoerd, moet u een handmatige configuratie uitvoeren in de Azure-portal om de componenten en services aan elkaar te koppelen.

De voorbeeld-app is gericht op beginners die toepassingen op Azure ontwikkelen die beveiligingsmaatregelen in hun toepassingen willen implementeren.

Bij het ontwikkelen en implementeren van deze app leert u hoe u:

- Maak een Azure Key Vault-exemplaar, sla er geheimen uit op en haal deze op.
- Implementeer Azure Database voor PostgreSQL, stel veilige wachtwoorden in en vernieuw de toegang tot deze database.
- Voer een Alpine Linux-container uit op Azure Web Apps voor Linux en schakel beheerde identiteiten in voor Azure-bronnen.
- Een Azure Application Gateway-instantie maken en configureren met een firewall die gebruikmaakt van [OWASP Top 10 Ruleset](https://coreruleset.org/).
- Schakel versleuteling van gegevens in transit en in rust in met behulp van Azure-services.

Nadat u deze app hebt ontwikkeld en geïmplementeerd, hebt u de volgende voorbeeldweb-app ingesteld, samen met de configuratie- en beveiligingsmaatregelen die worden beschreven.

![Voorbeeld web-app](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architectuur

De app is een typische n-tier applicatie met drie lagen. De front-end, back-end en database laag met monitoring en secret-management componenten geïntegreerd worden hier weergegeven:

![App-architectuur](./media/secure-web-app/architecture.png)

De architectuur bestaat uit deze componenten:

- [Azure Application Gateway](../../application-gateway/index.yml). Biedt de gateway en firewall voor onze applicatiearchitectuur.
- [Azure Web Apps op Linux](../../app-service/containers/app-service-linux-intro.md). Hiermee wordt de containerruntijd mogelijk om de Python-app in een Linux-omgeving uit te voeren.
- [Azure Key Vault](../../key-vault/index.yml). Slaat de geheimen van onze app op en versleutelt en beheert het maken van toegangsbeleid eromheen.
- [Azure-database voor PostgreSQL](https://azure.microsoft.com/services/postgresql/). Slaat de gegevens van onze app veilig op.
- [Azure Security Center](../../security-center/index.yml) en [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Biedt monitoring en waarschuwingen over de werking van onze app.

## <a name="threat-model"></a>Bedreigingsmodel

Bedreigingsmodellering is het proces van het identificeren van potentiële beveiligingsbedreigingen voor uw bedrijf en toepassing en vervolgens ervoor te zorgen dat er een goed mitigatieplan is.

In dit voorbeeld wordt de [Microsoft Threat Modeling Tool](threat-modeling-tool.md) gebruikt om bedreigingsmodellering voor de beveiligde voorbeeld-app te implementeren. Door de componenten en de gegevensstromen in kaart te brengen, u problemen en bedreigingen al vroeg in het ontwikkelingsproces identificeren. Dit bespaart later tijd en geld.

Dit is het bedreigingsmodel voor de voorbeeld-app:

![Bedreigingsmodel](./media/secure-web-app/threat-model.png)

Sommige voorbeeldbedreigingen en potentiële kwetsbaarheden die de hulpprogramma voor bedreigingsmodellering genereert, worden weergegeven in de volgende schermafbeelding. Het dreigingsmodel geeft een overzicht van het blootgestelde aanvalsoppervlak en vraagt de ontwikkelaars na te denken over hoe de problemen kunnen worden beperkt.

![Uitvoer van bedreigingsmodellen](./media/secure-web-app/threat-model-output.png)

Sql-injectie in de vorige uitvoer van het bedreigingsmodel wordt bijvoorbeeld beperkt door gebruikersinvoer te ontsmetten en opgeslagen functies in Azure Database voor PostgreSQL te gebruiken. Deze beperking voorkomt willekeurige uitvoering van query's tijdens het lezen en schrijven van gegevens.

Ontwikkelaars verbeteren de algehele beveiliging van het systeem door het beperken van elk van de bedreigingen in het bedreigingsmodel output.

## <a name="deployment"></a>Implementatie

Met de volgende opties u Linux uitvoeren op Azure App Service:

- Kies een container in de lijst met vooraf gebouwde Microsoft-containers op Azure die zijn gemaakt met ondersteunende technologieën (Python, Ruby, PHP, Java, Node.js, .NET Core).
- Gebruik een op maat gemaakte container. Selecteer uw eigen containerregisters als bron van de afbeelding en bouw voort op de vele beschikbare technologieën die HTTP ondersteunen.

In dit voorbeeld voert u het implementatiescript uit waarmee de webapp wordt geïmplementeerd in de app-service en de bronnen wordt gemaakt.

De app kan de verschillende implementatiemodellen hieronder gebruiken:

![Diagram implementatiegegevensstroom](./media/secure-web-app/deployment.png)

Er zijn veel manieren om apps op Azure te implementeren, waaronder:

- Azure Resource Manager-sjablonen
- PowerShell
- Azure CLI
- Azure Portal
- Azure DevOps

Deze toepassing wordt gebruikt:

- [Docker](https://docs.docker.com/) om de containerafbeeldingen te maken en te bouwen.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) voor implementatie.
- [Docker Hub](https://hub.docker.com/) als containerregister.

## <a name="security-considerations"></a>Beveiligingsoverwegingen

### <a name="network"></a>Netwerk

De voorbeeld-app maakt gebruik van end-to-end TLS/SSL-versleuteling voor binnenkomende gegevens die in en uit het netwerk stromen. De gateway is geconfigureerd met een zelfondertekend certificaat.
> [!IMPORTANT]
> In deze demonstratie wordt een zelfondertekend certificaat gebruikt. In een productieomgeving moet u certificaten verkrijgen van een geverifieerde certificeringsinstantie (CA).

De toepassingsfirewall inspecteert ook binnenkomend verkeer en waarschuwt beheerders wanneer kwaadaardig verkeer wordt gedetecteerd in het netwerkverkeer.
Application Gateway vermindert de mogelijkheid voor DDoS- en SQL-injectiebedreigingen die in het bedreigingsmodel worden ontdekt.

### <a name="identity"></a>Identiteit

Als u zich wilt aanmelden bij de portal, gebruikt de voorbeeld-app Multi-Factor Authentication voor Azure Active Directory-beheerders (Azure AD) die toegang hebben gekregen tot de bronnen.
De voorbeeld-app gebruikt beheerde identiteiten om machtigingen te verkrijgen voor het lezen en ophalen van geheimen uit Azure Key Vault, zodat de app geen speciale referenties en tokens hoeft te gebruiken om de geheimen te lezen. Azure AD maakt automatisch de serviceprincipals die de app moet lezen en wijzigt de geheimen wanneer beheerde identiteiten worden gebruikt.

Beheerde identiteiten voor Azure-resources en MFA maken het moeilijker voor tegenstanders om privileges te verkrijgen en hun bevoegdheden in het systeem te escaleren. Deze dreiging werd opgemerkt in het dreigingsmodel.
De app maakt gebruik van OAuth, waarmee gebruikers die zijn geregistreerd in de OAuth-applicatie zich kunnen aanmelden bij de app.

### <a name="storage"></a>Storage

Gegevens in de PostgreSQL-database worden in rust automatisch versleuteld door Azure Database voor PostgreSQL. De database autoriseert de IP-adressen van de App Service, zodat alleen de geïmplementeerde App Service-webapp toegang heeft tot de databasebronnen met de juiste verificatiereferenties.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking

De app implementeert logboekregistratie door Application Insights te gebruiken om statistieken, logboeken en uitzonderingen die zich voordoen bij te houden. Deze logboekregistratie biedt voldoende metagegevens van apps om ontwikkelaars en teamleden te informeren over de status van de app. Het biedt ook voldoende gegevens om terug te krabbelen in geval van beveiligingsincidenten.

## <a name="cost-considerations"></a>Kostenoverwegingen

Als u nog geen Azure-account hebt, u een gratis account maken. Ga naar de [gratis accountpagina](https://azure.microsoft.com/free/) om aan de slag te gaan, zie wat u doen met een gratis Azure-account en ontdek welke producten 12 maanden gratis zijn.

Als u de resources in de voorbeeld-app wilt implementeren met de beveiligingsfuncties, moet u betalen voor een aantal premiumfuncties. Aangezien de app schaalt en de gratis lagen en proefversies die door Azure worden aangeboden, moeten worden geüpgraded om te voldoen aan de vereisten voor toepassingen, kunnen uw kosten stijgen. Gebruik de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) om uw kosten te schatten.

## <a name="deploy-the-solution"></a>De oplossing implementeren

### <a name="prerequisites"></a>Vereisten

Als u de toepassing operationeel wilt krijgen, moet u de volgende hulpprogramma's installeren:

- Een codeeditor om de toepassingscode te wijzigen en te bekijken. [Visual Studio Code](https://code.visualstudio.com/) is een open source optie.
- [Azure CLI](/cli/azure/install-azure-cli) op uw ontwikkelcomputer.
- [Git](https://git-scm.com/) op uw systeem. Git wordt gebruikt om de broncode lokaal te klonen.
- [jq](https://stedolan.github.io/jq/), een UNIX-tool voor het op een gebruiksvriendelijke manier opvragen van JSON.

U hebt een Azure-abonnement nodig om de bronnen van de voorbeeld-app te implementeren. Als u geen Azure-abonnement hebt, u [een gratis account maken](https://azure.microsoft.com/free/) om de voorbeeld-app te testen.

Nadat u deze hulpprogramma's hebt geïnstalleerd, u de app op Azure implementeren.

### <a name="environment-setup"></a>Configuratie van de omgeving

Voer de implementatiescripts uit om de omgeving en het abonnement in te stellen:

1. Als u de broncoderepository wilt klonen, gebruikt u deze opdracht Git:

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. Als u naar de map wilt gaan, gebruikt u deze opdracht:

   ```shell
   cd tutorial-project/scripts
   ```

3. Er zijn bestanden in de map scripts die specifiek zijn voor het platform dat u gebruikt (Windows of Linux). Als de Azure CLI al is geïnstalleerd, meldt u zich aan bij het Azure-account bij de opdrachtprompt door deze opdracht Azure CLI uit te voeren:

   ```azurecli-interactive
   az login
   ```

De browser wordt geopend en meldt u zich aan met uw referenties. Nadat u zich hebt aanmeldt, u beginnen met het implementeren van de resources van de opdrachtprompt.

De `deploy-powershell.ps1` implementatiescripts `deploy-bash.sh` en bevatten code die de hele toepassing implementeert.
Ga als het gaat om de implementatie van de oplossing:

1. Als u op PowerShell `deploy-powershell.ps1` het `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` bestand uitvoert door de naam van de regio en de resourcegroep te vervangen door geschikte Azure-regio's en een naam voor de resourcegroep
2. Als u op Linux `deploy-bash.sh` het `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`bestand uitvoert door te typen, moet u het bestand mogelijk uitvoerbaar maken door te typen`chmod +x deploy-bash.sh`

In de volgende voorbeelden worden fragmenten van de belangrijkste onderdelen weergegeven. U de voorbeelden afzonderlijk of met de rest van de componenten implementeren door de deploy-bestanden uit te voeren.

### <a name="implementation-guidance"></a>Uitvoeringsrichtsnoeren

Het implementatiescript is een script dat in vier fasen kan worden opgesplitst. Elke fase implementeert en configureert een Azure-bron die zich in het [architectuurdiagram bevindt.](#architecture)

De vier fasen zijn:

- Azure Key Vault implementeren.
- Azure-database implementeren voor PostgreSQL.
- Azure Web Apps implementeren op Linux.
- Application Gateway implementeren met firewall voor webtoepassingen.

Elke fase bouwt voort op de voorgaande fase met behulp van configuratie van de eerder geïmplementeerde resources.

Als u de implementatiestappen wilt voltooien, controleert u of u de hulpprogramma's hebt geïnstalleerd die worden vermeld onder [Vereisten](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Azure Key Vault implementeren

In deze sectie maakt en implementeert u een Azure Key Vault-exemplaar dat wordt gebruikt om geheimen en certificaten op te slaan.

Nadat u de implementatie hebt voltooid, wordt een Azure Key Vault-exemplaar geïmplementeerd op Azure.

Azure Key Vault implementeren met Azure CLI:

1. Declareer de variabelen voor Azure Key Vault.
2. Registreer de Azure Key Vault-provider.
3. Maak de resourcegroep voor de instantie.
4. Maak de instantie Azure Key Vault in de resourcegroep die in stap 3 is gemaakt.

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

Het is een aanbevolen manier om beheerde identiteiten voor Azure-bronnen te gebruiken in apps die Key Vault gebruiken om toegang te krijgen tot bronnen. Uw beveiligingshouding neemt toe wanneer toegangssleutels tot Key Vault niet in code of in configuratie worden opgeslagen.

#### <a name="deploy-azure-database-for-postgresql"></a>Azure-database implementeren voor PostgreSQL

Azure Database voor PostgreSQL werkt op de volgende manier, maak eerst de databaseserver en maak vervolgens de database waarop het schema en de gegevens moeten worden opgeslagen.

Nadat u de implementatie hebt voltooid, hebt u een PostgreSQL-server en -database die op Azure worden uitgevoerd.

Azure Database voor PostgreSQL implementeren met Azure CLI:

1. Open een terminal met Azure CLI en de installatie van uw Azure-abonnement.
2. Genereer een veilige combinatie van gebruikersnaam en wachtwoord die wordt gebruikt om toegang te krijgen tot de database. (Deze moeten worden opgeslagen in Azure Key Vault voor apps die ze gebruiken.)
3. De instantie PostgreSQL-server maken.
4. Maak een database op de serverinstantie die u in stap 3 hebt gemaakt.
5. Voer PostgreSQL-scripts uit op de PostgreSQL-instantie.

De onderstaande code is gebaseerd op de PGUSERNAME- en PGPASSWORD-geheimen die zijn opgeslagen in Azure KeyVault van de bovenstaande implementatie-KeyVault-stap.

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

Nadat u de database hebt geïmplementeerd, moet u de referenties en verbindingstekenreeks opslaan in Azure Key Vault.
In de map scripts is `functions.sql` er een bestand dat de PL/pgSQL-code bevat waarmee opgeslagen functies worden gemaakt wanneer u deze uitvoert. Als u dit bestand uitvoert, worden de ingangen parameteriseert om SQL-injectie te beperken.

PostgreSQL wordt gebundeld met `psql` een tool genaamd die wordt gebruikt om verbinding te maken met de database. Als `functions.sql`u wilt uitvoeren, moet u vanaf uw lokale machine verbinding maken met de Azure Database voor PostgreSQL-instantie en deze vanaf daar uitvoeren. De installatie van het psql-hulpprogramma is opgenomen in de standaardinstallatie voor PostgreSQL op elk besturingssysteem.
Zie de [psql-documentatie voor](https://www.postgresql.org/docs/9.3/app-psql.html)meer informatie.

Azure Cloud Shell `psql` bevat ook de tool. U Cloud Shell rechtstreeks vanuit de Azure-portal gebruiken door het Cloud Shell-pictogram te selecteren.

Als u externe toegang tot de PostgreSQL-instantie wilt inschakelen, moet u het IP-adres in PostgreSQL autoriseren.
U schakelt deze toegang in door naar het tabblad **Verbindingsbeveiliging** te gaan, **client-IP toevoegen**te selecteren en de nieuwe instellingen op te slaan.

![Client-IP autoriseren](./media/secure-web-app/add-client-ip-postgres.png)

Als u Cloud Shell gebruikt in plaats van het lokale psql-hulpprogramma, selecteert u **Toegang tot Azure-services toestaan** en de waarde ervan wijzigen in **AAN** om uw Cloud Shell-toegang toe te staan.

Maak vervolgens verbinding met de instantie door de opdracht onder psql met verbindingstekenreeksparameters uit te voeren vanaf het tabblad **Verbindingstekenreeksen** van de instantie PostgreSQL op de Azure-portal.
Vervang de lege accolades door parameters uit het verbindingstekenreeksblad van de database en het wachtwoord door het wachtwoord van Azure Key Vault.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Voer het volgende PL/pgSQL-script uit nadat u ervoor hebt gezorgd dat u bent verbonden met de database. Het script maakt de opgeslagen functies die worden gebruikt om gegevens in de database in te voegen.

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

Zie [TLS-connectiviteit configureren in Azure Database voor PostgreSQL voor](/azure/postgresql/concepts-ssl-connection-security)meer informatie over het instellen van TLS- en Certificate Authority (CA)-verificatie voor PostgreSQL.

Een rootcertificaat is opgenomen in de container. De stappen die zijn genomen om het certificaat te verkrijgen zijn:

1. Download het certificaatbestand van de [Certificeringsinstantie](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Download en installeer OpenSSL op uw machine.](/azure/postgresql/concepts-ssl-connection-security)
3. Decoderen van uw certificaatbestand:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Lees hier meer over het configureren van TLS-beveiliging voor PostgreSQL, [TLS Connection Security configureren.](/azure/postgresql/concepts-ssl-connection-security)

#### <a name="deploy-azure-web-apps-on-linux"></a>Azure Web Apps implementeren op Linux

U eenvoudig Linux-services bouwen bovenop Azure App Service, omdat Azure een set vooraf gebouwde containers en afbeeldingen biedt voor veelgebruikte talen zoals Python, Ruby, C#en Java. Azure ondersteunt ook aangepaste containers, waardoor vrijwel alle programmeertalen kunnen worden uitgevoerd op het Azure App Service-platform.

De app die wordt geïmplementeerd is een eenvoudige Python-app die draait op de nieuwste Ubuntu Linux-distributie. Het maakt verbinding met de Azure Key Vault- en PostgreSQL-exemplaren die in de vorige secties zijn gemaakt voor respectievelijk referentiebeheer en gegevensopslag.

Het volgende Docker-bestand wordt weergegeven in de hoofdmap van de app:

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

Het Dockerfile hierboven wordt gebruikt om de container te `mcr.microsoft.com/samples/basic-linux-app`bouwen die wordt gehost in het Azure Container Registry op .

De onderstaande code:

1. Hiermee worden de variabelen en namen voor de instantie App-service declareert.
2. Hiermee maakt u de resourcegroep voor het App-serviceplan.
3. Voorziet in een instantie Azure Web Apps op Linux-containers.
4. Hiermee u logboekregistratie voor de web-app-container in.
5. Hiermee stelt u bepaalde app-configuraties in de app-instellingen van de container in.

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

Met dit script wordt een toegewezen identiteit gemaakt voor de app-service-instantie die met MSI kan worden gebruikt om te communiceren met Azure Key Vault zonder harde coderingsgeheimen in code of configuratie.

Ga naar de instantie Azure Key Vault in de portal om de toegewezen identiteit te autoriseren op het tabblad toegangsbeleid. Selecteer **Nieuw toegangsbeleid toevoegen**. Zoek **onder Hoofd selecteren**de toepassingsnaam die vergelijkbaar is met de naam van de instantie App-service die is gemaakt.
Een serviceprincipal die aan de toepassing is gekoppeld, moet zichtbaar zijn. Selecteer deze en sla de pagina toegangsbeleid op, zoals in de volgende schermafbeelding wordt weergegeven.

Omdat de toepassing alleen sleutels hoeft op te halen, selecteert u de machtiging **Ophalen** in de geheimenopties, waarbij toegang wordt toegestaan terwijl de verleende bevoegdheden worden verminderd.

![Key Vault Access Policy](./media/secure-web-app/kv-access-policy.png)

*Een toegangsbeleid voor key vault maken*

Sla het toegangsbeleid op en sla de nieuwe wijziging op op het tabblad **Toegangsbeleid** om het beleid bij te werken.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Application Gateway implementeren met firewall voor webtoepassingen ingeschakeld

In web apps, is het niet aan te raden dat u diensten rechtstreeks bloot aan de buitenwereld op het internet.
Load balancing en firewall regels bieden meer veiligheid en controle over het binnenkomende verkeer en helpen u het te beheren.

Een toepassingsgateway-instantie implementeren:

1. Maak de resourcegroep om de toepassingsgateway te huisvesten.
2. Een virtueel netwerk inrichten om aan de gateway te koppelen.
3. Maak een subnet voor de gateway in het virtuele netwerk.
4. Een openbaar IP-adres opgeven.
5. Inrichten van de toepassingsgateway.
6. Firewall voor webtoepassingen inschakelen op de gateway.

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

Het voorgaande script:

1. Hiermee maakt u een nieuw zelfondertekend certificaat op Azure.
2. Downloadt het zelfondertekende certificaat als een bestand met basis64.Downloadt het zelfondertekende certificaat als een bestand met basis64.
3. Hiermee genereert u een wachtwoord voor het zelfondertekende certificaat.
4. Hiermee exporteert u het certificaat als een PFX-bestand dat is ondertekend met het wachtwoord.
5. Slaat het wachtwoord van het certificaat op in Azure Key Vault.

In deze sectie wordt de toepassingsgateway geïmplementeerd:

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

Nadat u de implementatie hebt voltooid, hebt u een toepassingsgateway met firewall voor webtoepassingen ingeschakeld.

De gateway-instantie stelt poort 443 voor HTTPS bloot. Deze configuratie zorgt ervoor dat onze app alleen toegankelijk is op poort 443 via HTTPS.

Het blokkeren van ongebruikte poorten en het beperken van de blootstelling aan het aanvalsoppervlak is een beveiligingstoepassing.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Netwerkbeveiligingsgroepen toevoegen aan het exemplaar app-service

App Service-exemplaren kunnen worden geïntegreerd met virtuele netwerken. Met deze integratie kunnen ze worden geconfigureerd met netwerkbeveiligingsgroepen die het inkomende en uitgaande verkeer van de app beheren.

1. Als u deze functie wilt inschakelen, selecteert u in het azure app-serviceinstantieblad onder **Instellingen**de optie **Netwerken**. Selecteer in het rechterdeelvenster onder **VNet-integratie**De optie **Klik hier om te configureren**.

   ![Nieuwe virtuele netwerkintegratie](./media/secure-web-app/app-vnet-menu.png)

    *Nieuwe virtuele netwerkintegratie voor App Service*

1. Selecteer **VNET toevoegen (voorbeeld)** op de volgende pagina.

1. Selecteer in het volgende menu het virtuele netwerk `hello-vnet`dat is gemaakt in de implementatie die begint met . U een nieuw subnet maken of een bestaand subnet selecteren.
   Maak in dit geval een nieuw subnet. Stel het **bereik van** het adres in op **10.0.3.0/24** en geef een naam aan het subnet **app-subnet**.

   ![Virtuele netwerkconfiguratie van App Service](./media/secure-web-app/app-vnet-config.png)

    *Virtuele netwerkconfiguratie voor App-service*

Nu u de virtuele netwerkintegratie hebt ingeschakeld, u netwerkbeveiligingsgroepen toevoegen aan onze app.

1. Gebruik het zoekvak, zoek naar **netwerkbeveiligingsgroepen.** Selecteer **Netwerkbeveiligingsgroepen** in de resultaten.

    ![Zoeken naar netwerkbeveiligingsgroepen](./media/secure-web-app/nsg-search-menu.png)

    *Zoeken naar netwerkbeveiligingsgroepen*

2. Selecteer In het volgende menu De optie **Toevoegen**. Voer de **naam** van de NSG en de **resourcegroep** in waarin deze zich moet bevinden. Deze NSG wordt toegepast op het subnet van de applicatiegateway.

    ![Een NSG maken](./media/secure-web-app/nsg-create-new.png)

    *Een NSG maken*

3. Nadat de NSG is gemaakt, selecteert u deze. Selecteer in het blad onder **Instellingen**de **inkomende beveiligingsregels**. Configureer deze instellingen zodat verbindingen in de toepassingsgateway via poort 443 kunnen worden opgenomen.

   ![De NSG configureren](./media/secure-web-app/nsg-gateway-config.png)

   *De NSG configureren*

4. Voeg in de uitgaande regels voor de gateway NSG een regel toe die uitgaande verbindingen met `AppService`de instantie App-service toestaat door een regel te maken die zich richt op de servicetag:

   ![Uitgaande regels voor de NSG toevoegen](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Uitgaande regels voor de NSG toevoegen*

    Voeg nog een uitgaande regel toe om de gateway uitgaande regels naar een virtueel netwerk te laten verzenden.

   ![Een andere uitgaande regel toevoegen](./media/secure-web-app/nsg-outbound-vnet.png)

    *Een andere uitgaande regel toevoegen*

5. Selecteer **Op**het subnet van het NSG Associate, selecteer je het virtuele netwerk dat in de implementatie is gemaakt en selecteer je het gatewaysubnet met de naam **gw-subnet**. De NSG wordt toegepast op het subnet.

6. Maak een andere NSG zoals in de eerdere stap, dit keer voor het app-service-exemplaar. Geef het een naam. Voeg de inkomende regel voor poort 443 toe, net als voor de toepassingsgateway NSG.

   Als u een App Service-instantie hebt geïmplementeerd op een app-serviceomgeving-instantie, wat niet het geval is voor deze app, u binnenkomende regels toevoegen om Azure Service Health-sondes toe te staan door poorten 454-455 te openen voor de binnenkomende beveiligingsgroepen van uw App Service NSG. Hier is de configuratie:

   ![Regels toevoegen voor Azure Service Health-sondes](./media/secure-web-app/nsg-create-healthprobes.png)

    *Regels toevoegen voor Azure Service Health-sondes (alleen App-serviceomgeving)*

7. Maak in de uitgaande beveiligingsregels een nieuwe uitgaande beveiligingsregel waarmee de instantie App Service kan communiceren met de PostgreSQL-database. Configureer het als volgt:

   ![Regel om uitgaande PostgreSQL-verbindingen toe te staan](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Een regel toevoegen om uitgaande PostgreSQL-verbindingen toe te staan*

Als u het aanvalsoppervlak wilt beperken, wijzigt u de netwerkinstellingen van App Service zodat alleen de toepassingsgateway toegang heeft tot de toepassing.
U doet dit door naar het tabblad App Service-netwerk te gaan, het tabblad **IP-beperkingen te** selecteren en een allow-regel te maken waarmee alleen het IP-adres van de toepassingsgateway rechtstreeks toegang heeft tot de service.

U het IP-adres van de gateway ophalen op de overzichtspagina. Voer op het tabblad **CIDR IP-adres** het `<GATEWAY_IP_ADDRESS>/32`IP-adres in deze indeling in: .

![Alleen de gateway toestaan](./media/secure-web-app/app-allow-gw-only.png)

*Alleen het gateway-IP toestaan om toegang te krijgen tot de App-service*

#### <a name="implement-azure-active-directory-oauth"></a>Azure Active Directory OAuth implementeren

De Azure-documenten die op de voorbeeldwebpagina worden gedistribueerd, zijn bronnen in onze app die mogelijk bescherming nodig hebben. U Azure Active Directory (Azure AD) gebruiken om verificatie voor web-, bureaublad- en mobiele apps te implementeren met behulp van verschillende verificatiestromen.
De app maakt gebruik **van Login With Microsoft**, waarmee de app profielen kan lezen van gebruikers die zijn toegevoegd aan de lijst van onze Azure AD-gebruiker met één tenant.

Configureer de app in de Azure-portal om de vereiste referenties te gebruiken:

1. Selecteer **Azure Active Directory**of zoek ernaar met het zoekvak.

2. Selecteer **Nieuwe registratie:**

   ![Een registratie maken](./media/secure-web-app/ad-auth-create.png)

   *Een Azure AD-app-registratie maken*

3. Voer op de volgende pagina de naam van de app in. Selecteer onder **Ondersteunde accounttypen** **alleen Accounts in deze organisatiemap**.
    Voer **onder Uri omleiden**het basisdomein in waarop de app wordt uitgevoerd plus één met het tokeneindpunt. Bijvoorbeeld: *GATEWAY_HASH*.cloudapp.net/token.

   ![Azure AD-appregistratie configureren](./media/secure-web-app/ad-auth-type.png)

   *Azure AD-appregistratie configureren*

4. U krijgt een scherm te zien dat de geregistreerde app en de bijbehorende informatie weergeeft. U moet deze informatie toevoegen aan het exemplaar Azure Key Vault.
   1. Kopieer de toepassings-id (client) en `CLIENTID`sla deze op in Key Vault als .
   2. Kopieer de omleidings-URI die u in `REDIRECTURI`de vorige stap hebt ingevoerd en sla deze op als .
   3. Kopieer de standaardnaam van de Azure AD-standaardmap, die de `TENANT` *opmaaknaam*.microsoftonline.com heeft, en sla deze op in Key Vault als .
   4. Ga naar het tabblad **Certificaten & geheimen** van de Azure AD-app die u eerder hebt gemaakt en selecteer Nieuw **clientgeheim**, zoals in de volgende schermafbeelding wordt weergegeven. Stel een vervaldatum in en kopieer de gegenereerde waarde `CLIENTSECRET`en sla deze op in Key Vault als .

      ![Azure AD-autorisatiegeheim](./media/secure-web-app/ad-auth-secrets.png)

      *Azure AD-autorisatiegeheim*

   5. Genereer een veilige willekeurige geheime sleutel met behulp van een command-line / online tool. Bewaar het in `FLASKSECRETKEY`Key Vault als . Het toepassingskader gebruikt deze sleutel om sessies te maken.
        Zie [Flask Sessions](http://flask.pocoo.org/docs/1.0/quickstart/#sessions)voor meer informatie over het genereren van een geheime sleutel.

5. Nadat u de aanmelding hebt geconfigureerd, moet u gebruikers toevoegen aan de Azure AD-koppeling om hen in staat te stellen zich bij de bron aan te melden. Als u ze wilt toevoegen, gaat u naar het tabblad **Gebruikers** in Azure AD, selecteert u **Alle gebruikers**en selecteert u Vervolgens **Nieuwe gebruiker** of **Nieuwe gastgebruiker**. Voor het testen u een gastgebruiker toevoegen en de gebruiker uitnodigen in de directory. U ook een nieuwe gebruiker toevoegen als het domein waarop de app wordt uitgevoerd, is gevalideerd. In dit voorbeeld kunnen alleen gebruikers die zijn geregistreerd in de Azure AD-tenant worden geregistreerd voor toegang. Zie de documentatie voor informatie over aanmeldingstoegang voor meerdere huurders.

   ![Gebruikers toevoegen aan standaarddomein](./media/secure-web-app/ad-auth-add-user.png)

   *Gebruikers toevoegen aan het standaard Azure Active Directory-domein*

Nadat u de Azure AD-configuratie en -geheimen aan Key Vault hebt toegevoegd, kunnen gebruikers worden geverifieerd in de app met Azure OAuth-verificatie.
In de app-code wordt dit afgehandeld door de Azure Active Directory Authentication Library (ADAL).

Nadat de geheimen zich in Key Vault bevinden en de toepassing toegang heeft tot de geheimen en de\/database, kan de applicatieservice worden bereikt via de url van de toepassing van de gateway (https: /GATEWAY_HASH.cloudapp.net), die u krijgen van het blad.

Als u zich bij Azure AD aanmeldt bij Azure AD, krijgt u een foutmelding met de tekst 'Gebruiker is niet geregistreerd in de map waarop u zich probeert aan te melden', moet u de gebruiker toevoegen. Als u de gebruiker wilt toevoegen, gaat u naar het tabblad **Gebruikers** van Azure AD en voegt u de gebruiker handmatig toe door zijn gegevens in te voeren of de gebruiker uit te nodigen door zijn e-mailadres in te voeren als gastgebruiker in Azure AD in het gastenblad **Uitnodigen.**

#### <a name="deploy-application-insights"></a>Application Insights implementeren
Nu de app is geïmplementeerd en werkt, moet u fouten verwerken die zich in de app voordoen, samen met het registreren en traceren van gegevensverzameling.
Logboekregistratie en traceergegevens verzameling biedt een overzicht van audit gebeurtenissen die zich voordoen in de app.

Application Insights is een service die logboeken verzamelt die kunnen worden gegenereerd door gebruikers of door het systeem.

Ga als u een instantie Application Insights maken:

1. Zoek **naar toepassingsinzichten** met behulp van het zoekvak in de Azure-portal.
2. Selecteer **Application Insights**. Geef de details op die hier worden weergegeven om een instantie te maken.

   ![Een instantie Application Insights maken](./media/secure-web-app/app-insights-data.png)

Nadat de implementatie is voltooid, hebt u een instantie Application Insights.

Nadat u het exemplaar Applications Insights hebt gemaakt, moet u de app bewust maken van de instrumentatiesleutel waarmee logboeken naar de cloud kunnen worden verzonden. U doet dit door de toepassingsinzichten op te halen en te gebruiken in de toepassingsbibliotheken die Azure biedt voor Toepassingsinzichten. Het beste is om sleutels en geheimen op te slaan in Azure Key Vault om ze veilig te houden.

Voor de basisvoorbeeld-app moet u, nadat u het exemplaar Applications Insights hebt gemaakt, de app bewust maken van de instrumentatiesleutel waarmee de app logboeken naar de cloud kan verzenden.
Stel in Key `APPINSIGHTSKEY` Vault een geheim in en stel de waarde ervan in als instrumentatietoets. Hierdoor kan de app logboeken en statistieken naar Application Insights verzenden.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Multi-Factor-verificatie implementeren voor Azure Active Directory

Beheerders moeten ervoor zorgen dat de abonnementsaccounts in de portal worden beveiligd. Het abonnement is kwetsbaar voor aanvallen omdat het de resources beheert die u hebt gemaakt. Als u het abonnement wilt beveiligen, schakelt u Multi-Factor Authentication in op het tabblad **Azure Active Directory** van het abonnement.

Azure AD werkt op basis van beleidsregels die worden toegepast op een gebruiker of groepen gebruikers die aan een bepaalde criteria voldoen.
Azure maakt een standaardbeleid waarin wordt aangegeven dat beheerders tweestapsverificatie nodig hebben om zich aan te melden bij de portal.
Nadat u dit beleid hebt ingevonden, wordt u mogelijk gevraagd om u af te melden en u weer aan te melden bij de Azure-portal.

Ga als bedoeld als u MFA inschakelt voor beheerdersaanmeldingen:

1. Ga naar het tabblad **Azure Active Directory** in de Azure-portal
2. Selecteer onder de beveiligingscategorie voorwaardelijke toegang. U ziet dit scherm:

   ![Voorwaardelijke toegang - Beleid](./media/secure-web-app/ad-mfa-conditional-add.png)

Als u geen nieuw beleid maken:

1. Ga naar het **MFA-tabblad.**
2. Selecteer de proefversiekoppeling Azure AD Premium **Free** om u te abonneren op de gratis proefversie.

   ![Gratis proefversie van Azure AD Premium](./media/secure-web-app/ad-trial-premium.png)

Terug naar het scherm met voorwaardelijke toegang.

1. Selecteer het nieuwe tabblad beleid.
2. Voer de naam van het beleid in.
3. Selecteer de gebruikers of groepen waarvoor u MFA wilt inschakelen.
4. Selecteer **onder Toegangsbesturingselementen**het tabblad **Verlening** en selecteer Desgewenst **multifactorauthenticatie** (en andere instellingen vereisen).

   ![MFA vereisen](./media/secure-web-app/ad-mfa-conditional-add.png)

U het beleid inschakelen door het selectievakje boven aan het scherm in te schakelen of dit te doen op het tabblad **Voorwaardelijke toegang.** Wanneer het beleid is ingeschakeld, hebben gebruikers MFA nodig om zich aan te melden bij de portal.

Er is een basislijnbeleid waarvoor MFA vereist is voor alle Azure-beheerders. U het direct inschakelen in de portal. Als u dit beleid inschakelt, kan de huidige sessie ongeldig worden gemaakt en u zich opnieuw aanmelden.

Als het basislijnbeleid niet is ingeschakeld:

1. Selecteer **MFA vereisen voor beheerders**.
2. Selecteer **Beleid gebruiken onmiddellijk**.

   ![Beleid gebruiken onmiddellijk selecteren](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Azure Sentinel gebruiken om apps en resources te controleren

Naarmate een toepassing groeit, wordt het moeilijk om alle beveiligingssignalen en statistieken die van resources zijn ontvangen, samen te voegen en ze op een actiegerichte manier nuttig te maken.

Azure Sentinel is ontworpen om gegevens te verzamelen, de soorten bedreigingen te detecteren die mogelijk zijn en inzicht te bieden in beveiligingsincidenten.
Terwijl het wacht op handmatige interventie, kan Azure Sentinel vertrouwen op vooraf geschreven playbooks om waarschuwingen en incidentbeheerprocessen te starten.

De voorbeeld-app bestaat uit verschillende bronnen die Azure Sentinel kan controleren.
Als u Azure Sentinel wilt instellen, moet u eerst een Log Analytics-werkruimte maken die alle gegevens opslaat die zijn verzameld uit de verschillende bronnen.

Ga als volgt te werk om deze werkruimte te maken:

1. Zoek in het zoekvak in de Azure-portal naar **Log Analytics**. Selecteer **Logboekanalysewerkruimten**.

   ![Zoeken naar Log Analytics-werkruimten](./media/secure-web-app/sentinel-log-analytics.png)

    *Zoeken naar Log Analytics-werkruimten*

2. Selecteer op de volgende pagina **Toevoegen** en geef vervolgens een naam, resourcegroep en locatie op voor de werkruimte.
   ![Een Log Analytics-werkruimte maken](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Een Log Analytics-werkruimte maken*

3. Gebruik het zoekvak om te zoeken naar **Azure Sentinel**.

   ![Zoeken naar Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *Zoeken naar Azure Sentinel*

4. Selecteer **Toevoegen** en selecteer vervolgens de werkruimte Log Analytics die u eerder hebt gemaakt.

   ![Een werkruimte Logboekanalyse toevoegen](./media/secure-web-app/sentinel-workspace-add.png)

    *Een werkruimte Logboekanalyse toevoegen*

5. Selecteer op de pagina **Azure Sentinel - Gegevensconnectoren** onder **Configuratie**de optie **Gegevensconnectoren**. U ziet een array met Azure-services die u koppelen aan de opslaginstantie Log Analytics voor analyse in Azure Sentinel.

   ![Gegevensconnectors voor Log Analytics](./media/secure-web-app/sentinel-connectors.png)

    *Een gegevensconnector toevoegen aan Azure Sentinel*

   Als u bijvoorbeeld de toepassingsgateway wilt verbinden, neemt u de volgende stappen:

   1. Open het instantieblad azure application gateway.
   2. Selecteer **Diagnostische instellingen** onder **Controle**.
   3. Selecteer **Diagnostische instelling toevoegen**.

      ![Application Gateway-diagnose toevoegen](./media/secure-web-app/sentinel-gateway-connector.png)

      *Application Gateway-diagnose toevoegen*

   4. Selecteer **op** de pagina Diagnostische instellingen de werkruimte Log Analytics die u hebt gemaakt en selecteer vervolgens alle statistieken die u wilt verzamelen en verzenden naar Azure Sentinel. Selecteer **Opslaan**.

        ![Azure Sentinel-connectorinstellingen](./media/secure-web-app/sentinel-connector-settings.png)

        *Azure Sentinel-connectorinstellingen*

  De statistieken van de bron bevinden zich in Azure Sentinel, waar u ze opvragen en onderzoeken.

   Voeg dezelfde statistieken toe aan diagnostische instellingen voor Azure Key Vault, het openbare IP-adres, Azure Database voor PostgreSQL en alle services die diagnostische logboeken in uw account ondersteunen.

Nadat u de statistieken hebt ingesteld, moet Azure Sentinel gegevens analyseren.

## <a name="evaluate-and-verify"></a>Evalueren en verifiëren

Nadat u de architectuur hebt ontwikkeld en geïmplementeerd, moet u ervoor zorgen dat de code en de geïmplementeerde services voldoen aan beveiligingsstandaarden. Dit zijn enkele stappen die u nemen om de software te verifiëren:

- Analyse van statische code
- Kwetsbaarheid scannen
- Kwetsbaarheden in toepassingsafhankelijkheden vinden en oplossen

Dit zijn de basisbouwstenen voor best practices in veilige ontwikkeling.

### <a name="static-code-analysis"></a>Analyse van statische code

Voor de voorbeeld-app omvat verificatie met statische analysetools het vinden van kwetsbaarheden in de app-code met behulp van technieken zoals taint checking en data-flow analyse. Python statische analyse tools geven u meer vertrouwen dat uw app veilig is.

**Linting (Linting)**

PyFlakes, een Python linting bibliotheek, helpt u dode code en ongebruikte functies uit apps te verwijderen, zoals hier wordt weergegeven:

![Pyflakes](./media/secure-web-app/pyflakes.png)

Linting geeft tips en mogelijke wijzigingen die uw code schoner en minder foutgevoelig kunnen maken tijdens runtime.

**Pylint**

PyLint gaf de meeste waarde voor dit project. Het voert code-standaard controles, foutcontrole, en refactoring tips om ervoor te zorgen dat de code die op de server is veilig. Door PyLint te gebruiken om uw code bij te werken, u bugs elimineren en de PyLint-beoordeling verbeteren, zoals de volgende afbeeldingen laten zien.

![Vóór PyLint](./media/secure-web-app/before-pylint.png)

*Vóór PyLint*

Nadat u een aantal codefouten hebt opgelost die door de lintingtools zijn gevonden, hebt u er meer vertrouwen in dat de code niet gevoelig is voor fouten. Het oplossen van de fouten vermindert aanzienlijk de beveiligingsrisico's die kunnen optreden wanneer de code wordt geïmplementeerd in productieomgevingen.

![Na Pylint](./media/secure-web-app/after-pylint.png)

*Na PyLint*

### <a name="vulnerability-scanning"></a>Kwetsbaarheid scannen

[OWASP's ZAP](https://www.zaproxy.org/) tool is een open-source web applicatie kwetsbaarheid scanner die u gebruiken om de sample app te controleren op kwetsbaarheden. Het uitvoeren van het gereedschap op de voorbeeld-app onthult een aantal mogelijke fouten en aanvalsvectoren.

![Zap, gereedschap](./media/secure-web-app/zap-tool.png)

*Zap, gereedschap*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Kwetsbaarheden in app-afhankelijkheden zoeken en oplossen

Als u toepassingsafhankelijkheden wilt zoeken en oplossen, u [de afhankelijkheidscontrole van OWASP gebruiken.](https://owasp.org/www-project-dependency-check/)

Veiligheid is een vergelijkbare toepassing die afhankelijkheden controleert. Je het vinden op [GitHub.](https://github.com/pyupio/safety) Veiligheidsscans voor kwetsbaarheden gevonden in bekende kwetsbaarheid databases.

![Veiligheid](./media/secure-web-app/pysafety.png)

*Veiligheid*

## <a name="next-steps"></a>Volgende stappen

De volgende artikelen kunnen u helpen bij het ontwerpen, ontwikkelen en implementeren van beveiligde toepassingen.

- [Ontwerp](secure-design.md)
- [Ontwikkelen](secure-develop.md)
- [Implementeren](secure-deploy.md)
