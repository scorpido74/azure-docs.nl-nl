---
title: Hyperledger Fabric consortium op Azure Kubernetes Service (AKS)
description: Hyperledger Fabric-consortiumnetwerk implementeren en configureren op Azure Kubernetes Service
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476437"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Hyperledger Fabric consortium op Azure Kubernetes Service (AKS)

U de sjabloon Hyperledger Fabric (HLF) op Azure Kubernetes Service (AKS) gebruiken om een Hyperledger Fabric-consortiumnetwerk op Azure te implementeren en configureren.

Wanneer u dit artikel hebt gelezen:

- Verkrijg werkkennis van Hyperledger Fabric en de verschillende componenten die de bouwstenen vormen van Hyperledger Fabric blockchain-netwerk.
- Meer informatie over het implementeren en configureren van een Hyperledger Fabric-consortium op Azure Kubernetes Service voor uw productiescenario's.

## <a name="hyperledger-fabric-consortium-architecture"></a>Hyperledger Fabric Consortium architectuur

Als u hyperledgerfabric-netwerk op Azure wilt bouwen, moet u de bestelservice en -organisatie implementeren met peer nodes. De verschillende fundamentele componenten die worden gemaakt als onderdeel van de sjabloonimplementatie zijn:

- **Ordererknooppunten**: een knooppunt dat verantwoordelijk is voor het bestellen van transacties in het grootboek. Samen met andere knooppunten vormen de geordende knooppunten de bestelservice van het Hyperledger Fabric-netwerk.

- **Peer nodes**: Een knooppunt dat voornamelijk grootboeken en slimme contracten host, deze fundamentele elementen van het netwerk.

- **Fabric CA:** Fabric CA is the Certificate Authority (CA) for Hyperledger Fabric. Met de Fabric CA u het serverproces initialiseren en starten waarmee de certificaatautoriteit wordt host. Hiermee u identiteiten en certificaten beheren. Elk AKS-cluster dat als onderdeel van de sjabloon wordt geïmplementeerd, heeft standaard een Fabric CA-pod.

- **CouchDB of LevelDB**: World state database voor de peer nodes kan worden opgeslagen in LevelDB of CouchDB. LevelDB is de standaardstatusdatabase die is ingesloten in de peer node en slaat ketencodegegevens op als eenvoudige sleutelwaardeparen en ondersteunt alleen sleutel-, sleutelbereik- en samengestelde sleutelquery's. CouchDB is een optionele alternatieve statusdatabase die uitgebreide query's ondersteunt wanneer chaincode-gegevenswaarden worden gemodelleerd als JSON.

De sjabloon voor implementatie draait verschillende Azure-bronnen in uw abonnement op. De verschillende Azure-resources die zijn geïmplementeerd, zijn:

- **AKS-cluster**: Azure Kubernetes-cluster dat is geconfigureerd volgens de invoerparameters die door de klant worden opgegeven. Het AKS-cluster heeft verschillende pods geconfigureerd voor het uitvoeren van de Hyperledger Fabric-netwerkcomponenten. De verschillende pods die zijn gemaakt zijn:

  - **Fabric tools**: De fabric tool is verantwoordelijk voor het configureren van de Hyperledger Fabric componenten.
  - **Orderer/peer pods**: De knooppunten van het HLF-netwerk.
  - **Proxy:** een NGNIX proxy pod waarmee de clientapplicaties kunnen communiceren met het AKS-cluster.
  - **Stof CA:** de pod die de Fabric CA draait.
- **PostgreSQL**: Een instantie van PostgreSQL wordt geïmplementeerd om de Fabric CA-identiteiten te behouden.

- **Azure Key vault**: Een sleutelkluisinstantie wordt geïmplementeerd om de Fabric CA-referenties en de rootcertificaten van de klant op te slaan, die worden gebruikt in het geval van opnieuw proberen van sjabloonimplementatie, dit is om de mechanica van de sjabloon te verwerken.
- **Azure Managed disk**: Azure Managed disk is voor permanente opslag voor grootboek- en peernodeworld state database.
- **Openbaar IP:** een openbaar IP-eindpunt van het AKS-cluster dat is geïmplementeerd voor interfacing met het cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger Fabric Blockchain-netwerksetup

Om te beginnen hebt u een Azure-abonnement nodig dat ondersteuning kan bieden voor het implementeren van verschillende virtuele machines en standaardopslagaccounts. Als u geen Azure-abonnement hebt, u [een gratis Azure-account maken.](https://azure.microsoft.com/free/)

Stel Hyperledger Fabric Blockchain-netwerk in met de volgende stappen:

- [De orderer/peer-organisatie implementeren](#deploy-the-ordererpeer-organization)
- [Bouw het consortium](#build-the-consortium)
- [Native HLF-bewerkingen uitvoeren](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>De orderer/peer-organisatie implementeren

Als u aan de slag wilt met de implementatie van HLF-netwerkcomponenten, navigeert u naar de [Azure-portal.](https://portal.azure.com) Selecteer **Een bron maken > Blockchain** > zoeken naar **Hyperledger Fabric op Azure Kubernetes Service.**

1. Selecteer **maken** om de sjabloonimplementatie te starten. De **Hyperledger-structuur maken op Azure Kubernetes-service** wordt weergegeven.

    ![Hyperledger Fabric op Azure Kubernetes Service Template](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Voer de projectdetails in de pagina **Basisbeginselen** in.

    ![Hyperledger Fabric op Azure Kubernetes Service Template](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Voer de volgende details in:
    - **Abonnement:** kies de abonnementsnaam waar u de HLF-netwerkcomponenten wilt implementeren.
    - **Resourcegroep**: Maak een nieuwe resourcegroep of kies een bestaande lege resourcegroep, de resourcegroep houdt alle resources die zijn geïmplementeerd als onderdeel van de sjabloon.
    - **Regio**: Kies het Azure-gebied waar u het Azure Kubernetes-cluster wilt implementeren voor de HLF-componenten. De sjabloon is beschikbaar in alle regio's waar AKS beschikbaar is Zorg ervoor dat u een regio kiest waar uw abonnement de vm-quotumlimiet (Virtual Machine) niet bereikt.
    - **Resourcevoorvoegsel:** voorvoegsel voor het benoemen van resources die zijn geïmplementeerd. Het voorvoegsel van resources moet minder dan zes tekens lang zijn en de combinatie van tekens moet zowel getallen als letters bevatten.
4. Selecteer het tabblad **Structuurinstellingen** om de HLF-netwerkcomponenten te definiëren die worden geïmplementeerd.

    ![Hyperledger Fabric op Azure Kubernetes Service Template](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Voer de volgende details in:
    - **Organisatienaam**: De naam van de Fabric-organisatie, die vereist is voor verschillende bewerkingen van gegevensvlak. De naam van de organisatie moet uniek zijn per implementatie. 
    - **Fabric-netwerkcomponent:** kies bestelservice of Peer-knooppunten op basis van blockchain-netwerkcomponent die u wilt instellen.
    - **Aantal knooppunten** - De volgende zijn de twee typen knooppunten:
        - Bestelservice - selecteer het aantal knooppunten dat de fouttolerantie voor het netwerk biedt. Slechts 3,5 en 7 zijn de ondersteunde orderer node tellen.
        - Peer-knooppunten - u 1-10 knooppunten kiezen op basis van uw vereiste.
    - **Peer node world state database**: Kies tussen LevelDB en CoucbDB. Dit veld wordt weergegeven wanneer de gebruiker peer node kiest in de vervolgkeuzelijst Fabric-netwerkcomponent.
    - **Gebruikersnamen voor stof:** voer de gebruikersnaam in die wordt gebruikt voor de Fabric CA-verificatie.
    - **Fabric CA-wachtwoord:** voer het wachtwoord in voor Fabric CA-verificatie.
    - **Wachtwoord bevestigen:** bevestig het wachtwoord van Fabric CA.
    - **Certificaten:** Als u uw eigen basiscertificaten wilt gebruiken om de Fabric CA te initialiseren, kiest u Rootcertificaat uploaden voor de optie Fabric CA, anders maakt Fabric CA standaard zelfondertekende certificaten.
    - **Rootcertificaat**: Upload rootcertificaat (public key) waarmee Fabric CA moet worden geïnitialiseerd. Certificaten van .pem-formaat worden ondersteund, de certificaten moeten geldig zijn in utc-tijdzone.
    - **Privésleutel basiscertificaat:** Upload de privésleutel van het hoofdcertificaat. Als u een .pem-certificaat hebt, dat zowel openbare als privésleutel heeft gecombineerd, upload deze dan ook hier.


6. Selecteer het tabblad **AKS-clusterinstellingen** om de Azure Kubernetes-clusterconfiguratie te definiëren die de onderliggende infrastructuur is waarop de fabric-netwerkcomponenten worden ingesteld.

    ![Hyperledger Fabric op Azure Kubernetes Service Template](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Voer de volgende details in:
    - **Kubernetes-clusternaam:** de naam van het AKS-cluster dat is gemaakt. Dit veld is vooraf ingevuld op basis van het meegeleverde bronvoorvoegsel, u indien nodig wijzigen.
    - **Kubernetes-versie**: De versie van de Kubernetes die op het cluster wordt geïmplementeerd. Op basis van het gebied dat is geselecteerd op het tabblad **Basisbeginselen,** kunnen de beschikbare ondersteunde versies worden gewijzigd.
    - **DNS-voorvoegsel**: DNS-naamvoorvoegsel (Domain Name System) voor AKS-cluster. U gebruikt DNS om verbinding te maken met de Kubernetes API bij het beheren van containers na het maken van het cluster.
    - **Knooppuntgrootte**: De grootte van het Kubernetes-knooppunt, u kiezen uit de lijst met VM Stock keeping unit (SKU's) die beschikbaar is op Azure. Voor optimale prestaties raden we standaard DS3 v2 aan.
    - **Aantal knooppunten**: het aantal Kubernetes-knooppunten dat in het cluster moet worden geïmplementeerd. We raden u aan het aantal knooppunten ten minste gelijk of hoger te houden dan het aantal HLF-knooppunten dat is opgegeven in de fabric-instellingen.
    - **Serviceprincipal client ID:** Voer de client-ID van een bestaande serviceprincipal in of maak een nieuwe, die vereist is voor de AKS-verificatie. Zie, stappen om [serviceprincipal te maken.](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)
    - **Service belangrijkste klant geheim**: Voer de klant geheim van de service principal die in de service principal client ID.
    - **Bevestig het geheim van**de klant: bevestig het klantgeheim in het geheim van de hoofdklant van de dienst.
    - **Containerbewaking inschakelen:** kies om AKS-bewaking in te schakelen, waardoor de AKS-logboeken naar de opgegeven loganalytics-werkruimte kunnen worden geduwd.
    - **Werkruimte Log Analytics**: De werkruimte Logboekanalyse wordt gevuld met de standaardwerkruimte die wordt gemaakt als bewaking is ingeschakeld.

8. Nadat u alle bovenstaande details hebt opgegeven, selecteert u Controleren en het tabblad **maken.** Met de beoordeling en het maken activeert u de validatie voor de opgegeven waarden.
9. Zodra de validatie is doorgegeven, u **kiezen voor .**
De implementatie duurt meestal 10-12 minuten, kan variëren afhankelijk van de grootte en het aantal opgegeven AKS-knooppunten.
10. Na de succesvolle implementatie wordt u hiervan op de hoogte gesteld via Azure-meldingen in de rechterbovenhoek.
11. Selecteer **Ga naar resourcegroep om** alle resources te controleren die zijn gemaakt als onderdeel van de sjabloonimplementatie. Alle resourcenamen beginnen met het voorvoegsel in de instelling **Basisbeginselen.**

## <a name="build-the-consortium"></a>Bouw het consortium

Om de blockchain-consortium post te bouwen die de bestelservice en peer nodes implementeert, moet u de onderstaande stappen achter elkaar uitvoeren. **Bouw uw netwerkscript** (byn.sh), waarmee u het consortium instellen, kanaal maken en chaincode installeren.

> [!NOTE]
> Build Your Network (byn) script is strikt te gebruiken voor demo / devtest scenario's. Voor de installatie van productiekwaliteit raden we u aan de native HLF API's te gebruiken.

Alle opdrachten voor het uitvoeren van het byn-script kunnen worden uitgevoerd via de Azure Bash Command Line Interface (CLI). U inloggen op azure shell webversie via ![Hyperledger Fabric op Azure Kubernetes Service Template](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) optie in de rechterbovenhoek van de Azure-portal. Typ bash en voer op de opdrachtprompt in om over te schakelen naar bash CLI.

Zie [Azure shell](https://docs.microsoft.com/azure/cloud-shell/overview) voor meer informatie.

![Hyperledger Fabric op Azure Kubernetes Service Template](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Download byn.sh en fabric-admin.yaml-bestand.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Hieronder omgevingsvariabelen instellen op Azure CLI Bash-shell:**

Kanaalinformatie en informatie over ordererorganisaties instellen

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Informatie over peer-organisaties instellen

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Maak één Azure-bestandsshare om verschillende openbare certificaten te delen tussen peer- en ordererorganisaties.

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**Kanaalbeheeropdrachten**

Ga naar het AKS-cluster en de opdracht Voor een nieuw kanaal om een nieuw kanaal te maken

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Opdrachten voor consortiumbeheer**

Voer onderstaande opdrachten uit in de gegeven volgorde om een peer-organisatie toe te voegen aan een kanaal en consortium.

1. Ga naar het AKS-cluster van peerorganisatie en upload de MSP (Member Service Provide) op een Azure-bestandsopslag.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Ga naar het AKS-cluster van ordererorganisatie en voeg de peer-organisatie toe in kanaal en consortium.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Ga terug naar peer organization en geef de opdracht uit om peer nodes in het kanaal aan te sluiten.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Als u meer peer-organisaties in het kanaal wilt toevoegen, worden ak-omgevingsvariabelen peer bijgewerkt volgens de vereiste peer-organisatie en u de stappen 1 tot en met 3 uitvoeren.

**Opdrachten voor beheer van chaincode**

Voer de onderstaande opdracht uit om de ketencodegerelateerde bewerking uit te voeren. Deze opdrachten voeren alle bewerkingen uit op een demochaincode. Deze demo chaincode heeft twee variabelen "a" en "b". Op instantiatie van de chaincode, "a" is geïnitialiseerd met 1000 en "b" is geïnitialiseerd met 2000. Bij elke aanroep van de kettingcode worden 10 eenheden overgedragen van "a" naar "b". Querybewerking op chaincode toont de wereldstatus van "a" variabele.

Voer de volgende opdrachten uit die zijn uitgevoerd op het AKS-cluster van peerorganisatie.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Opdrachten voor de bewerking van chaincode**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Native HLF-bewerkingen uitvoeren

Klanten aan de slag helpen met het uitvoeren van Hyperledger native opdrachten op HLF-netwerk op AKS. De voorbeeldtoepassing wordt geleverd die stof NodeJS SDK gebruikt om de HLF-bewerkingen uit te voeren. De opdrachten worden geleverd om nieuwe gebruikersidentiteit te maken en uw eigen chaincode te installeren.

### <a name="before-you-begin"></a>Voordat u begint

Volg de onderstaande opdrachten voor de eerste installatie van de toepassing:

- Toepassingsbestanden downloaden
- Verbindingsprofiel en beheerdersprofiel genereren
- Gebruikersidentiteit van beheerders importeren

Nadat u de eerste installatie hebt voltooid, u de SDK gebruiken om de onderstaande bewerkingen te bereiken:

- Het genereren van gebruikersidentiteit
- Chaincode bewerkingen

De bovengenoemde opdrachten kunnen worden uitgevoerd vanuit Azure Cloud Shell.

### <a name="download-application-files"></a>Toepassingsbestanden downloaden

De eerste setup voor het uitvoeren van toepassing is het downloaden van alle toepassingsbestanden in een map.

**Maak de map van apps en voer de map in:**

```bash
mkdir app
cd app
```
Voer onderstaande opdracht uit om alle vereiste bestanden en pakketten te downloaden:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Deze opdracht kost tijd om alle pakketten te laden. Na een succesvolle uitvoering van `node_modules` de opdracht u een map in de huidige map zien. Alle benodigde pakketten worden `node_modules` in de map geladen.

### <a name="generate-connection-profile-and-admin-profile"></a>Verbindingsprofiel en beheerdersprofiel genereren

Map `profile` maken `app` in de map

```bash
cd app
mkdir ./profile
```
Deze omgevingsvariabelen instellen op Azure-cloudshell

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Onderstaande opdracht uitvoeren om verbindingsprofiel en beheerdersprofiel van de organisatie te genereren

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Het zal verbindingprofiel `profile` en admin van de organisatie `<orgname>-ccp.json` `<orgname>-admin.json` in de profielmap met naam en respectievelijk creëren.

Op dezelfde manier u voor elke orderer en peer-organisatie het verbindingsprofiel en het beheerdersprofiel genereren.


### <a name="import-admin-user-identity"></a>Gebruikersidentiteit van beheerders importeren

De laatste stap is het importeren van de beheerdersidentiteit van de organisatie in de portemonnee.

```bash
npm run importAdmin -- -o <orgName>

```
Met de bovenstaande opdracht wordt importAdmin.js uitgevoerd om de identiteit van de beheerdersgebruiker in de portemonnee te importeren. Het script leest de beheerdersidentiteit uit het beheerdersprofiel `<orgname>-admin.json` en importeert deze in de portemonnee voor het uitvoeren van HLF-bewerkingen.

De scripts gebruiken de portemonnee van het bestandssysteem om de identiteiten op te slaan. Het maakt een portemonnee volgens het pad dat is opgegeven in het veld '.wallet' in het verbindingsprofiel. Standaard wordt het veld '.wallet' `<orgname>`geïnitialiseerd `<orgname>` met , wat betekent dat er een map met de naam wordt gemaakt in de huidige map om de identiteiten op te slaan. Als u portemonnee wilt maken op een ander pad, wijzigt u het veld '.wallet' in het verbindingsprofiel voordat u de gebruiker van de inschrijfbeheerder en andere HLF-bewerkingen uitvoert.

Importeer ook de gebruikersidentiteit van beheerders voor elke organisatie.

Raadpleeg de opdrachthelp voor meer informatie over de argumenten die in de opdracht zijn doorgegeven.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Het genereren van gebruikersidentiteit

Voer onderstaande opdrachten uit in de gegeven volgorde om nieuwe gebruikersidentiteiten voor de HLF-organisatie te genereren.

> [!NOTE]
> Voordat u begint met stappen voor het genereren van gebruikersidentiteit, moet u ervoor zorgen dat de eerste installatie van de toepassing wordt uitgevoerd.

Hieronder omgevingsvariabelen instellen op azure cloudshell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Nieuwe gebruiker registreren en inschrijven

Als u een nieuwe gebruiker wilt registreren en inschrijven, voert u de onderstaande opdracht uit die registerUser.js uitvoert. Het slaat de gegenereerde gebruikersidentiteit op in de portemonnee.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> Beheerdersgebruikersidentiteit wordt gebruikt om de opdracht Register voor de nieuwe gebruiker uit te geven. Daarom is het verplicht om de identiteit van de beheerdersgebruiker in de portemonnee te hebben voordat u deze opdracht uitvoert. Anders mislukt deze opdracht.

Opdrachthulp doorverwijzen voor meer informatie over de argumenten die in de opdracht zijn doorgegeven

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Chaincode bewerkingen


> [!NOTE]
> Voordat u begint met een ketencodebewerking, moet u ervoor zorgen dat de eerste installatie van de toepassing wordt uitgevoerd.

Stel hieronder ketencodespecifieke omgevingsvariabelen in op Azure Cloud shell:

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

De onderstaande chaincode bewerkingen kunnen worden uitgevoerd:

- Chaincode installeren
- Instantiate chaincode
- Chaincode aanroepen
- Queryketencode

### <a name="install-chaincode"></a>Chaincode installeren

Voer onderstaande opdracht uit om chaincode te installeren op de peer-organisatie.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Het installeert chaincode op alle peer nodes `ORGNAME` van de organisatie ingesteld in omgevingsvariabele. Als er twee of meer peer-organisaties in uw kanaal zijn en u chaincode op alle van deze organisaties wilt installeren, voert u de opdrachten afzonderlijk uit voor elke peer-organisatie.

Volg de stappen:

- Opdracht `ORGNAME` `<peerOrg1Name>` instellen `installCC` en uitgeven.
- Opdracht `ORGNAME` `<peerOrg2Name>` instellen `installCC` en uitgeven.

  Voer het uit voor elke peer organisatie.

Raadpleeg de opdrachthelp voor meer informatie over de argumenten die in de opdracht zijn doorgegeven.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Instantiate chaincode

Voer onderstaande opdracht uit om chaincode op de peer te instantiëren.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Pass instantiation functie naam en komma `<instantiateFunc>` gescheiden `<instantiateFuncArgs>` lijst van argumenten in en respectievelijk. Bijvoorbeeld, in [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go), om de chaincode `"Init"` `<instantiateFuncArgs>` ingesteld `<instantiateFunc>` op `""`en op lege string instantiate .

> [!NOTE]
> Voer de opdracht voor één keer uit vanuit één peer-organisatie in het kanaal.
> Zodra de transactie is ingediend bij de orderer, distribueert de orderorderdeze transactie naar alle peer-organisaties in het kanaal. Vandaar dat de chaincode wordt geinstantieerd op alle peer nodes op alle peer organisaties in het kanaal.

Opdrachthulp doorverwijzen voor meer informatie over de argumenten die in de opdracht zijn doorgegeven

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Chaincode aanroepen

Voer de onderstaande opdracht uit om de functie chaincode aan te roepen:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Pass aanroepen functie naam en komma `<invokeFunction>` `<invokeFuncArgs>` gescheiden lijst van argumenten in en respectievelijk. Doorgaan met het fabcar-kettingcodevoorbeeld, om `<invokeFunction>` initLedger-functie in te roepen op `"initLedger"` en `<invokeFuncArgs>` naar `""`.

> [!NOTE]
> Voer de opdracht voor één keer uit vanuit één peer-organisatie in het kanaal.
> Zodra de transactie is ingediend bij de orderer, distribueert de orderorderdeze transactie naar alle peer-organisaties in het kanaal. Vandaar dat de wereldstaat wordt bijgewerkt op alle peer nodes van alle peer organisaties in het kanaal.

Opdrachthulp doorverwijzen voor meer informatie over de argumenten die in de opdracht zijn doorgegeven

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Queryketencode

Uitvoer onder opdracht naar querychaincode:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

De naam van de queryfunctie doorgeven `<queryFunction>` `<queryFuncArgs>` en de lijst met argumenten in respectievelijk komma's gescheiden. Nogmaals, `fabcar` het nemen van chaincode als referentie, om `<queryFunction>` `"queryAllCars"` alle `<queryArgs>` `""`auto's in de wereld staat ingesteld op en naar query .

Opdrachthulp doorverwijzen voor meer informatie over de argumenten die in de opdracht zijn doorgegeven

```bash
npm run queryCC -- -h

```
