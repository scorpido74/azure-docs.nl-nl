---
title: Hyperledger Fabric consortium op Azure Kubernetes service (AKS)
description: Het Hyperledger Fabric consortium-netwerk implementeren en configureren op de Azure Kubernetes-service
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 59e13b671f68c29271227d481b41562256d66fd6
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289642"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Hyperledger Fabric consortium op Azure Kubernetes service (AKS)

U kunt de Hyperledger Fabric (HLF) op de Azure Kubernetes service (AKS)-sjabloon gebruiken om een Hyperledger Fabric consortium-netwerk te implementeren en configureren op Azure.

Wanneer u dit artikel hebt gelezen:

- Krijg praktische kennis over de Hyperledger-infra structuur en de verschillende onderdelen die de bouw stenen van Hyperledger Fabric Block chain-netwerk vormen.
- Meer informatie over het implementeren en configureren van een Hyperledger Fabric consortium op de Azure Kubernetes-service voor uw productie scenario's.

## <a name="hyperledger-fabric-consortium-architecture"></a>Consortium architectuur voor Hyperledger Fabric

Als u Hyperledger Fabric-netwerk op Azure wilt bouwen, moet u de order service en organisatie implementeren met peer knooppunten. De verschillende fundamentele onderdelen die worden gemaakt als onderdeel van de sjabloon implementatie zijn:

- **Orderer-knoop punten**: een knoop punt dat verantwoordelijk is voor transactie ordening in het groot boek. Naast andere knoop punten vormen de geordende knoop punten de bestel service van het Hyperledger-infrastructuur netwerk.

- **Gelijkwaardige knoop punten**: een knoop punt dat voornamelijk als host dient voor groot boeken en slimme contracten, deze fundamentele elementen van het netwerk.

- **Infrastructuur ca**: Fabric-CA is de certificerings instantie (CA) voor de Hyperledger-infra structuur. Met de infrastructuur certificerings instantie kunt u het Server proces dat als host fungeert voor de certificerings instantie, initialiseren en starten. Hiermee kunt u identiteiten en certificaten beheren. Elk AKS-cluster dat is geïmplementeerd als onderdeel van de sjabloon, heeft standaard een Fabric-CA pod.

- **Couchdb of LevelDB**: World State Data Base voor de peer knooppunten kan worden opgeslagen in LevelDB of couchdb. LevelDB is de standaard status database die in het knoop punt peer is inge sloten en waar chaincode-gegevens worden opgeslagen als eenvoudige sleutel-waardeparen en alleen sleutel, sleutel bereik en samengestelde sleutel query's ondersteunt. CouchDB is een optionele alternatieve status database die Rich query's ondersteunt wanneer chaincode-gegevens waarden als JSON worden gemodelleerd.

Met de sjabloon voor implementatie worden verschillende Azure-resources in uw abonnement gedraaid. De verschillende Azure-resources die zijn geïmplementeerd zijn:

- **AKS-cluster**: Azure Kubernetes-cluster dat is geconfigureerd met de invoer parameters van de klant. Het AKS-cluster heeft verschillende peulen die zijn geconfigureerd voor het uitvoeren van de onderdelen van het Hyperledger Fabric-netwerk. De verschillende peulen die worden gemaakt zijn:

  - **Infrastructuur hulpprogramma's**: het Fabric-hulp programma is verantwoordelijk voor het configureren van de Hyperledger Fabric-onderdelen.
  - **Orderer/peer-peul**: de knoop punten van het HLF-netwerk.
  - **Proxy**: een NGNIX-proxy pod die de client toepassingen kan interface met het AKS-cluster.
  - **Fabric-ca**: de pod die de Fabric-CA uitvoert.
- **Postgresql**: er is een instantie van postgresql geïmplementeerd voor het onderhouden van de CA-identiteiten van de infra structuur.

- **Azure-sleutel kluis**: er wordt een sleutel kluis-exemplaar geïmplementeerd voor het opslaan van de CA-certificerings instantie en de basis certificaten van de klant, die wordt gebruikt in het geval van een nieuwe poging voor een sjabloon implementatie. Dit is om de mechanismen van de sjabloon te verwerken.
- **Azure Managed Disk**: Azure Managed disk is voor een permanente opslag voor de data base in de wereld van het groot boek-en peer knooppunt
- **Openbaar IP-adres**: een openbaar IP-eind punt van het AKS-cluster dat is geïmplementeerd voor confacing met het cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger Fabric Block chain-netwerk installatie

Om te beginnen hebt u een Azure-abonnement nodig dat ondersteuning biedt voor de implementatie van verschillende virtuele machines en standaardopslagaccounts. Als u geen Azure-abonnement hebt, kunt u een [gratis Azure-account maken](https://azure.microsoft.com/free/).

Stel Hyperledger Fabric Block Chain Network in met behulp van de volgende stappen:

- [De orderer/peer-organisatie implementeren](#deploy-the-ordererpeer-organization)
- [Het consortium bouwen](#build-the-consortium)
- [Systeem eigen HLF-bewerkingen uitvoeren](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>De orderer/peer-organisatie implementeren

Om aan de slag te gaan met de implementatie van HLF-netwerk onderdelen, gaat u naar de [Azure Portal](https://portal.azure.com). Selecteer **een resource maken > block chain** > zoek naar **Hyperledger Fabric op de Azure Kubernetes-service**.

1. Selecteer **maken** om de sjabloon implementatie te starten. De **Hyperledger Fabric maken op de Azure Kubernetes-service** wordt weer gegeven.

    ![Hyperledger Fabric op de Azure Kubernetes-service sjabloon](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Voer de project details in op de pagina **basis beginselen** .

    ![Hyperledger Fabric op de Azure Kubernetes-service sjabloon](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Voer de volgende details in:
    - **Abonnement**: Kies de naam van het abonnement waarin u de HLF-netwerk onderdelen wilt implementeren.
    - **Resource groep**: Maak een nieuwe resource groep of kies een bestaande lege resource groep. de resource groep bevat alle resources die zijn geïmplementeerd als onderdeel van de sjabloon.
    - **Regio**: Kies de Azure-regio waar u het Azure Kubernetes-cluster wilt implementeren voor de HLF-onderdelen. De sjabloon is beschikbaar in alle regio's waar AKS beschikbaar is, moet u een regio kiezen waar uw abonnement niet de quotum limiet van de virtuele machine (VM) aankomt.
    - **Resource voorvoegsel**: voor voegsel voor de naamgeving van resources die worden geïmplementeerd. Het resource voorvoegsel moet uit minder dan zes tekens bestaan en de combi natie van tekens moet cijfers en letters bevatten.
4. Selecteer het tabblad **infrastructuur instellingen** om de HLF-netwerk onderdelen te definiëren die worden geïmplementeerd.

    ![Hyperledger Fabric op de Azure Kubernetes-service sjabloon](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Voer de volgende details in:
    - **Organisatie naam**: de naam van de infrastructuur organisatie, die is vereist voor verschillende bewerkingen voor gegevenslaag.
    - **Fabric-netwerk onderdeel**: Kies service-of peer knooppunten die zijn gebaseerd op Block chain netwerk onderdeel dat u wilt instellen.
    - **Aantal knoop punten** -de volgende twee typen knoop punten zijn:
        - Order service: Selecteer het aantal knoop punten waarvoor fout tolerantie aan het netwerk is gegeven. Alleen 3, 5 en 7 zijn het aantal ondersteunde orderer-knoop punten.
        - Knoop punten op hetzelfde niveau: u kunt 1-10-knoop punten kiezen op basis van uw vereiste.
    - **Data Base voor wereld wijde status van peer-knoop punt**: Kies tussen LevelDB en CoucbDB. Dit veld wordt weer gegeven wanneer de gebruiker de vervolg keuzelijst peer-knoop punt in infrastructuur netwerk onderdeel kiest.
    - **Fabric-gebruikers naam**: Voer de gebruikers naam in die wordt gebruikt voor de CA-verificatie van de infra structuur.
    - **CA-wacht woord van infrastructuur resources**: Voer het wacht woord in voor infrastructuur ca-authenticatie.
    - **Bevestig het wacht woord**: Bevestig het CA-wacht woord van de infra structuur.
    - **Certificaten**: als u uw eigen basis certificaten wilt gebruiken om de infrastructuur certificerings instantie te initialiseren, kiest u basis certificaat uploaden voor Fabric-ca-optie, anders door de CA-standaard infrastructuur maakt zelfondertekende certificaten.
    - **Basis certificaat**: basis certificaat uploaden (open bare sleutel) waarmee infrastructuur certificerings instantie moet worden geïnitialiseerd. Certificaten van. pem-indeling worden ondersteund. de certificaten moeten geldig zijn in de UTC-tijd zone.
    - **Persoonlijke sleutel van het basis certificaat**: de persoonlijke sleutel van het basis certificaat uploaden. Als u een. PEM-certificaat hebt, dat zowel een open bare als een persoonlijke sleutel bevat, kunt u dit hier ook uploaden.


6. Selecteer **AKS tabblad Cluster instellingen** om de Azure Kubernetes-cluster configuratie te definiëren die de onderliggende infra structuur is waarop de infrastructuur netwerk onderdelen moeten worden ingesteld.

    ![Hyperledger Fabric op de Azure Kubernetes-service sjabloon](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Voer de volgende details in:
    - **Kubernetes-cluster naam**: de naam van het AKS-cluster dat wordt gemaakt. Dit veld wordt vooraf ingevuld op basis van het voor voegsel van de resource, u kunt zo nodig wijzigen.
    - **Kubernetes-versie**: de versie van de Kubernetes die in het cluster wordt geïmplementeerd. Op basis van de regio die is geselecteerd op het tabblad **basis beginselen** , kunnen de ondersteunde versies worden gewijzigd.
    - **DNS-voor voegsel**: Domain Name System (DNS)-naam VOORVOEGSEL voor AKS-cluster. U gebruikt DNS om verbinding te maken met de Kubernetes-API wanneer u containers beheert nadat u het cluster hebt gemaakt.
    - **Knooppunt grootte**: de grootte van het Kubernetes-knoop punt, kunt u kiezen uit de lijst met Vm's (Stock Keeping Unit) die beschikbaar zijn op Azure. Voor optimale prestaties raden wij standaard DS3 v2 aan.
    - **Knooppunt telling**: het aantal Kubernetes-knoop punten dat in het cluster moet worden geïmplementeerd. U wordt aangeraden dit aantal knoop punten ten minste gelijk te houden aan het aantal HLF knooppunten dat is opgegeven in de infrastructuur instellingen.
    - **Service-Principal-client-id**: Voer de client-id van een bestaande Service-Principal in of maak een nieuwe, die vereist is voor de AKS-verificatie. Zie stappen voor het [maken](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal)van een service-principal.
    - **Service-Principal-client geheim**: Voer het client geheim in van de service-principal die is opgegeven in de client-id van de Service-Principal.
    - **Client geheim bevestigen**: Bevestig het client geheim dat is opgenomen in het client geheim van de Service-Principal.
    - **Container bewaking inschakelen**: Selecteer deze optie om AKS-bewaking in te scha kelen, zodat de AKS-logboeken naar de opgegeven log Analytics werk ruimte pushen.
    - **Log Analytics werk ruimte**: de werk ruimte voor log Analytics wordt gevuld met de standaardwerk ruimte die wordt gemaakt als bewaking is ingeschakeld.

8. Nadat u alle bovenstaande gegevens hebt opgegeven, selecteert u het tabblad **controleren en maken** . Met de controle en het maken wordt de validatie geactiveerd voor de waarden die u hebt ingevoerd.
9. Zodra de validatie is geslaagd, kunt u **maken**selecteren.
De implementatie duurt doorgaans 10-12 minuten, afhankelijk van de grootte en het aantal opgegeven AKS-knoop punten.
10. Nadat de implementatie is voltooid, ontvangt u een melding via meldingen van Azure in de rechter bovenhoek.
11. Selecteer **Ga naar resource groep** om alle resources te controleren die zijn gemaakt als onderdeel van de sjabloon implementatie. Alle resource namen worden gestart met het voor voegsel dat is opgenomen in de instelling **basis beginselen** .

## <a name="build-the-consortium"></a>Het consortium bouwen

Als u het block Chain consortium na het implementeren van de best maat service en peer-knoop punten wilt maken, moet u de onderstaande stappen in volg orde uitvoeren. **Bouw uw netwerk** script (byn.sh), dat u helpt bij het instellen van het consortium, het maken van een kanaal en het installeren van chaincode.

> [!NOTE]
> Het byn-script dat u hebt gemaakt, is strikt te gebruiken voor demonstratie-devtest scenario's. Voor het instellen van de productie kwaliteit raden we u aan de systeem eigen HLF-Api's te gebruiken.

Alle opdrachten voor het uitvoeren van het script byn kunnen worden uitgevoerd via de bash-opdracht regel interface (CLI) van Azure. U kunt zich aanmelden bij de Azure shell-webversie via ![Hyperledger Fabric op de Azure Kubernetes-service sjabloon](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) in de rechter bovenhoek van de Azure Portal. Typ bash en Enter in het opdracht prompt om over te scha kelen naar bash CLI.

Zie [Azure shell](https://docs.microsoft.com/azure/cloud-shell/overview) voor meer informatie.

![Hyperledger Fabric op de Azure Kubernetes-service sjabloon](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Down load het byn.sh-en Fabric-admin. yaml-bestand.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Stel onder omgevings variabelen in op Azure cli bash shell**:

Informatie over de organisatie van het kanaal en de orderer instellen

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Informatie van peer-organisatie instellen

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Maak een Azure-bestands share om verschillende open bare certificaten te delen tussen de organisaties van de peer en de orderer.

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
**Opdrachten voor kanaal beheer**

Ga naar orderere organisatie AKS cluster en opdracht uitgeven om een nieuw kanaal te maken

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Management-opdrachten voor consortium**

Voer de onderstaande opdrachten in de aangegeven volg orde uit om een peer-organisatie toe te voegen aan een kanaal en consortium.

1. Ga naar het AKS-cluster van de peer-organisatie en upload de bijbehorende leden service (MSP) op een Azure-File Storage.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Ga naar de orderer organisatie AKS-cluster en voeg de peer-organisatie toe in Channel en consortium.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Ga terug naar de organisatie van de peer en de opdracht issue om peer knooppunten in het kanaal te koppelen.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Als u meer peer-organisaties wilt toevoegen aan het kanaal, moet u peer AKS-omgevings variabelen bijwerken volgens de vereiste peer-organisatie en de stappen 1 tot en met 3 uitvoeren.

**Chaincode-beheer opdrachten**

Voer de onderstaande opdracht uit om de gerelateerde bewerking chaincode uit te voeren. Met deze opdrachten worden alle bewerkingen uitgevoerd op een demo-chaincode. Deze demo-chaincode heeft twee variabelen: "a" en "b". Bij het instantiëren van de chaincode wordt "a" geïnitialiseerd met 1000 en "b" geïnitialiseerd met 2000. Bij elke aanroep van de chaincode worden 10 eenheden overgebracht van ' a ' naar ' b '. De query bewerking in chaincode toont de wereld status van de variabele "a".

Voer de volgende opdrachten uit die zijn uitgevoerd op het AKS-cluster van de peer-organisatie.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Opdracht Chaincode**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Systeem eigen HLF-bewerkingen uitvoeren

Om klanten te helpen aan de slag te gaan met het uitvoeren van Hyperledger systeem eigen opdrachten op HLF-netwerk op AKS. De voorbeeld toepassing maakt gebruik van Fabric NodeJS SDK om de HLF-bewerkingen uit te voeren. De opdrachten zijn bedoeld om nieuwe gebruikers identiteiten te maken en uw eigen chaincode te installeren.

### <a name="before-you-begin"></a>Voordat u begint

Volg de onderstaande opdrachten voor de eerste installatie van de toepassing:

- Toepassings bestanden downloaden
- Verbindings profiel en beheerders profiel genereren
- Gebruikers-id van beheerder importeren

Nadat de eerste installatie is voltooid, kunt u de SDK gebruiken om de onderstaande bewerkingen te verzorgen:

- Genereren van gebruikers identiteit
- Chaincode bewerkingen

De hierboven genoemde opdrachten kunnen worden uitgevoerd vanaf Azure Cloud Shell.

### <a name="download-application-files"></a>Toepassings bestanden downloaden

De eerste installatie voor het uitvoeren van een toepassing is het downloaden van alle toepassings bestanden in een map.

**Maak een app-map en voer de volgende map in**:

```bash
mkdir app
cd app
```
Voer de volgende opdracht uit om alle vereiste bestanden en pakketten te downloaden:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Met deze opdracht wordt gebruikgemaakt van de tijd om alle pakketten te laden. Nadat de opdracht is uitgevoerd, ziet u een `node_modules` map in de huidige map. Alle vereiste pakketten worden geladen in de map `node_modules`.

### <a name="generate-connection-profile-and-admin-profile"></a>Verbindings profiel en beheerders profiel genereren

`profile` map maken in de `app` map

```bash
cd app
mkdir ./profile
```
Deze omgevings variabelen instellen in azure Cloud shell

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Voer de onderstaande opdracht uit om het verbindings profiel en het profiel van de beheerder van de organisatie te genereren

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Hiermee worden het verbindings profiel en de beheerders `profile` van de organisatie in de profielmap gemaakt met de naam `<orgname>-ccp.json` en `<orgname>-admin.json` respectievelijk.

U kunt ook het verbindings profiel en het profiel van de beheerder voor elke orderer en peer-organisatie genereren.


### <a name="import-admin-user-identity"></a>Gebruikers-id van beheerder importeren

De laatste stap is het importeren van de identiteit van de beheerder van de organisatie in de wallet.

```bash
npm run importAdmin -- -o <orgName>

```
De bovenstaande opdracht voert importAdmin. js uit om de gebruikers identiteit van de beheerder in de wallet te importeren. Het script leest de beheerders identiteit van het beheer profiel `<orgname>-admin.json` en importeert deze in Wallet voor het uitvoeren van HLF-bewerkingen.

De scripts gebruiken bestands systeem Wallet om de identiteiten op te slaan. Hiermee wordt een wallet gemaakt volgens het pad dat is opgegeven in het veld '. wallet ' in het verbindings profiel. Standaard wordt het veld '. wallet ' geïnitialiseerd met `<orgname>`, wat betekent dat een map met de naam `<orgname>` wordt gemaakt in de huidige map om de identiteiten op te slaan. Als u wallet op een ander pad wilt maken, wijzigt u het veld '. wallet ' in het verbindings profiel voordat u de gebruiker inschrijven en andere HLF-bewerkingen uitvoert.

Importeer ook de gebruikers identiteit van de beheerder voor elke organisatie.

Raadpleeg de Help van de opdracht voor meer informatie over de argumenten die zijn door gegeven in de opdracht.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Genereren van gebruikers identiteit

Voer de onderstaande opdrachten uit in de aangegeven volg orde om nieuwe gebruikers identiteiten voor de HLF-organisatie te genereren.

> [!NOTE]
> Voordat u begint met de stappen voor het genereren van gebruikers-id's, moet u ervoor zorgen dat de initiële installatie van de toepassing wordt uitgevoerd.

Onder omgevings variabelen instellen in azure Cloud shell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Nieuwe gebruiker registreren en inschrijven

Als u een nieuwe gebruiker wilt registreren en inschrijven, voert u de onderstaande opdracht uit die registerUser. js uitvoert. De gegenereerde gebruikers-id wordt opgeslagen in de wallet.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> De gebruikers-id van de beheerder wordt gebruikt voor het uitgeven van de registratie opdracht voor de nieuwe gebruiker. Daarom is het verplicht de gebruikers-id van de beheerder in de wallet te hebben voordat u deze opdracht uitvoert. Anders mislukt deze opdracht.

Raadpleeg de Help van de opdracht voor meer informatie over de argumenten die zijn door gegeven in de opdracht

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Chaincode bewerkingen


> [!NOTE]
> Voordat u met een chaincode-bewerking begint, moet u ervoor zorgen dat de eerste installatie van de toepassing wordt uitgevoerd.

Hieronder stelt u de specifieke omgevings variabelen chaincode in op Azure Cloud shell:

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

De onderstaande chaincode-bewerkingen kunnen worden uitgevoerd:

- Chaincode installeren
- Chaincode instantiëren
- Chaincode aanroepen
- Query chaincode

### <a name="install-chaincode"></a>Chaincode installeren

Voer de onderstaande opdracht uit om chaincode op de peer-organisatie te installeren.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Er wordt chaincode geïnstalleerd op alle peer knooppunten van de organisatie die is ingesteld in `ORGNAME` omgevings variabele. Als er twee of meer peer-organisaties in uw kanaal zijn en u chaincode wilt installeren, moet u de opdrachten afzonderlijk voor elke peer-organisatie uitvoeren.

Volg de stappen:

- Stel `ORGNAME` in op `<peerOrg1Name>` en geef `installCC` opdracht.
- Stel `ORGNAME` in op `<peerOrg2Name>` en geef `installCC` opdracht.

  Voer deze uit voor elke peer-organisatie.

Raadpleeg de Help van de opdracht voor meer informatie over de argumenten die zijn door gegeven in de opdracht.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Chaincode instantiëren

Voer de onderstaande opdracht uit om chaincode op de peer te instantiëren.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Geef de naam van de functie en de door komma's gescheiden lijst met argumenten in `<instantiateFunc>` en `<instantiateFuncArgs>` respectievelijk door. U kunt bijvoorbeeld in [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go)de chaincode `<instantiateFunc>` instellen op `"Init"` en `<instantiateFuncArgs>` op een lege teken reeks `""`.

> [!NOTE]
> Voer de opdracht uit voor één van de peer-organisaties in het kanaal.
> Zodra de trans actie is verzonden naar de orderer, wordt deze trans actie door de orderer gedistribueerd naar alle peer-organisaties in het kanaal. Daarom wordt de chaincode geïnstantieerd op alle peer knooppunten op alle peer-organisaties in het kanaal.

Raadpleeg de Help van de opdracht voor meer informatie over de argumenten die zijn door gegeven in de opdracht

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Chaincode aanroepen

Voer de onderstaande opdracht uit om de chaincode-functie aan te roepen:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Geef de naam van de functie invoke en door komma's gescheiden lijst met argumenten in `<invokeFunction>` en `<invokeFuncArgs>` respectievelijk op. U kunt door gaan met het fabcar chaincode-voor beeld om initLedger-functie set aan te roepen `<invokeFunction>` op `"initLedger"` en `<invokeFuncArgs>` te `""`.

> [!NOTE]
> Voer de opdracht uit voor één van de peer-organisaties in het kanaal.
> Zodra de trans actie is verzonden naar de orderer, wordt deze trans actie door de orderer gedistribueerd naar alle peer-organisaties in het kanaal. De wereld staat wordt daarom bijgewerkt op alle peer knooppunten van alle peer-organisaties in het kanaal.

Raadpleeg de Help van de opdracht voor meer informatie over de argumenten die zijn door gegeven in de opdracht

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Query chaincode

Voer de onderstaande opdracht uit om een query uit te voeren op chaincode:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Geef de naam van de query functie en de door komma's gescheiden lijst met argumenten in `<queryFunction>` en `<queryFuncArgs>` respectievelijk op. `fabcar` chaincode als referentie om alle auto's in de wereld gezondheidsorganisatie in te voeren `<queryFunction>` in `"queryAllCars"` en `<queryArgs>' to `"" ".

Raadpleeg de Help van de opdracht voor meer informatie over de argumenten die zijn door gegeven in de opdracht

```bash
npm run queryCC -- -h

```
