---
title: Hyperledger Fabric consortium op Azure Kubernetes service (AKS)
description: Het Hyperledger Fabric consortium-netwerk implementeren en configureren op de Azure Kubernetes-service
ms.date: 06/04/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: e85d8c196afa5535d4d36ffdc03078e2046e4ca1
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85209705"
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

- **Azure-sleutel kluis**: er wordt een sleutel kluis-exemplaar geïmplementeerd voor het opslaan van de CA-certificerings instantie en de basis certificaten van de klant, die wordt gebruikt in het geval van een nieuwe poging voor een sjabloon implementatie om de mechanismen van de sjabloon te verwerken.
- **Azure Managed Disk**: Azure Managed disk is voor een permanente opslag voor de data base in de wereld van het groot boek-en peer knooppunt
- **Openbaar IP-adres**: een openbaar IP-eind punt van het AKS-cluster dat is geïmplementeerd voor confacing met het cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Hyperledger Fabric Block chain-netwerk installatie

Om te beginnen moet u een Azure-abonnement hebben dat ondersteuning biedt voor het implementeren van verschillende virtuele machines en standaard opslag accounts. Als u geen Azure-abonnement hebt, kunt u [een gratis Azure-account maken](https://azure.microsoft.com/free/).

Stel Hyperledger Fabric Block Chain Network in met behulp van de volgende stappen:

- [De orderer/peer-organisatie implementeren](#deploy-the-ordererpeer-organization)
- [Het consortium bouwen](#build-the-consortium)

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
    - **Organisatie naam**: de naam van de infrastructuur organisatie, die is vereist voor verschillende bewerkingen voor gegevenslaag. De naam van de organisatie moet uniek zijn per implementatie.
    - **Fabric-netwerk onderdeel**: Kies service-of peer knooppunten die zijn gebaseerd op Block chain netwerk onderdeel dat u wilt instellen.
    - **Aantal knoop punten** -de volgende twee typen knoop punten zijn:
        - Order service: Selecteer het aantal knoop punten waarvoor fout tolerantie aan het netwerk is gegeven. Alleen 3, 5 en 7 zijn het aantal ondersteunde orderer-knoop punten.
        - Knoop punten op hetzelfde niveau: u kunt 1-10-knoop punten kiezen op basis van uw vereiste.
    - **Data Base voor wereld wijde status van peer-knoop punt**: Kies tussen LevelDB en CoucbDB. Dit veld wordt weer gegeven wanneer de gebruiker de vervolg keuzelijst peer-knoop punt in infrastructuur netwerk onderdeel kiest.
    - **Fabric-gebruikers naam**: Voer de gebruikers naam in die wordt gebruikt voor de CA-verificatie van de infra structuur.
    - **CA-wacht woord van infrastructuur resources**: Voer het wacht woord in voor infrastructuur ca-authenticatie.
    - **Bevestig het wacht woord**: Bevestig het CA-wacht woord van de infra structuur.
    - **Certificaten**: als u uw eigen basis certificaten wilt gebruiken om de infrastructuur certificerings instantie te initialiseren, kiest u basis certificaat uploaden voor infrastructuur ca-optie, anders door de CA-standaard infrastructuur maakt zelfondertekende certificaten.
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

Als u het block Chain consortium na het implementeren van de best maat service en peer-knoop punten wilt maken, moet u de onderstaande stappen in volg orde uitvoeren. Azure HLF script (azhlf), dat u helpt bij het instellen van het consortium, het maken van Channel-en chaincode-bewerkingen.

> [!NOTE]
> Er is een update in het script. deze update is om meer functionaliteit te bieden met het Azure HLF-script. Als u wilt verwijzen naar het oude script, kunt u [hier te vinden](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Dit script is compatibel met Hyperledger Fabric op de Azure Kubernetes service-sjabloon versie 2.0.0 en hoger. Volg de stappen in [problemen oplossen](#troubleshoot)om de versie van de implementatie te controleren.

> [!NOTE]
> Azure HLF (azhlf)-script is bedoeld om u te helpen met alleen demonstratie-en DevTest-scenario's. Het door dit script gemaakte kanaal en consortium heeft een basis HLF-beleid om het scenario van de demo-DevTest te vereenvoudigen. Voor het instellen van de productie raden we u aan om het Channel/consortium HLF-beleid op te nemen in overeenstemming met de vereisten voor naleving van uw organisatie via de native HLF-Api's.


Alle opdrachten voor het uitvoeren van het Azure HLF-script kunnen worden uitgevoerd via de Azure bash-opdracht regel. Interface (CLI). U kunt zich aanmelden bij de Azure shell-webversie via  ![Hyperledger Fabric op de Azure Kubernetes-service sjabloon](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) in de rechter bovenhoek van de Azure Portal. Typ bash en Enter in het opdracht prompt om over te scha kelen naar bash CLI.

Zie [Azure shell](https://docs.microsoft.com/azure/cloud-shell/overview) voor meer informatie.

![Hyperledger Fabric op de Azure Kubernetes-service sjabloon](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


In de volgende afbeelding ziet u het stapsgewijze proces voor het bouwen van consortium tussen een order organisatie en peer-organisatie. Gedetailleerde opdrachten voor het uitvoeren van deze stappen worden vastgelegd in de volgende secties.

![Hyperledger Fabric op de Azure Kubernetes-service sjabloon](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Volg de onderstaande opdrachten voor de eerste installatie van de client toepassing: 

1.  [Client toepassings bestanden downloaden](#download-client-application-files)
2.  [Omgevingsvariabelen instellen](#setup-environment-variables)
3.  [Het verbindings Profiel van de organisatie, de gebruikers beheerder en de MSP importeren](#import-organization-connection-profile-admin-user-identity-and-msp)

Nadat de eerste installatie is voltooid, kunt u de-client toepassing gebruiken om de onderstaande bewerkingen te verzorgen:  

- [Opdrachten voor kanaal beheer](#channel-management-commands)
- [Management-opdrachten voor consortium](#consortium-management-commands)
- [Chaincode-beheer opdrachten](#chaincode-management-commands)

### <a name="download-client-application-files"></a>Client toepassings bestanden downloaden

De eerste installatie is het downloaden van de client toepassings bestanden. Voer de volgende opdracht uit om alle vereiste bestanden en pakketten te downloaden:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup

```
Met deze opdrachten wordt de Azure HLF-client toepassings code van de open bare GitHub opslag plaats gekloond, gevolgd door het laden van alle afhankelijke NPM-pakketten. Nadat de opdracht is uitgevoerd, ziet u een node_modules map in de huidige map. Alle vereiste pakketten worden geladen in de map node_modules.


### <a name="setup-environment-variables"></a>Omgevingsvariabelen instellen

> [!NOTE]
> Alle omgevings variabelen volgen de Azure resource Naming Convention.


**Onder omgevings variabelen instellen voor de orderer-organisatie-client**


```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```
**De onderstaande omgevings variabelen instellen voor de client van de peer-organisatie**

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> Op basis van het aantal peer-organisaties in uw consortium moet u mogelijk de peer-opdrachten herhalen en de omgevings variabele dienovereenkomstig instellen.

**De onderstaande omgevings variabelen instellen voor het instellen van Azure Storage account**

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Volg de onderstaande stappen voor het maken van Azure Storage-account. Als u Azure Storage account al hebt gemaakt, kunt u deze stappen overs Laan

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Volg de onderstaande stappen voor het maken van een bestands share in Azure Storage-account. Als u een bestands share al hebt gemaakt, kunt u deze stappen overs Laan

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Volg de onderstaande stappen voor het genereren van een Azure-bestands share connection string

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>Het verbindings Profiel van de organisatie, de gebruikers-id van de beheerder en de MSP importeren

Voer onderstaande opdrachten uit om het verbindings Profiel van de organisatie, de gebruikers-id van de beheerder en de MSP van het Azure Kubernetes-cluster op te halen en deze identiteiten op te slaan in het lokale archief van de client toepassing, bijvoorbeeld in de map azhlfTool/Stores.

Voor de organisatie van de bestelling:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Voor peer-organisatie:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="channel-management-commands"></a>Opdrachten voor kanaal beheer

> [!NOTE]
> Voordat u met een kanaal bewerking begint, moet u ervoor zorgen dat de eerste installatie van de client toepassing wordt uitgevoerd.  

Hieronder ziet u de twee opdrachten voor het beheren van kanalen:

1. [Kanaal maken, opdracht](#create-channel-command)
2. [De opdracht anker peer (s) instellen](#setting-anchor-peers-command)


#### <a name="create-channel-command"></a>Kanaal maken, opdracht

Van een orderer-client, opdracht geven om een nieuw kanaal te maken. Met deze opdracht wordt een kanaal met alleen een orderer-organisatie gemaakt.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

#### <a name="setting-anchor-peers-command"></a>De opdracht anker peer (s) instellen
Meld u bij de client van peer-organisatie onder de opdracht voor het instellen van anker peer (s) voor de peer-organisatie op het opgegeven kanaal.

>[!NOTE]
> Voordat u deze opdracht uitvoert, moet u ervoor zorgen dat peer-organisatie wordt toegevoegd aan het kanaal met behulp van consortium beheer opdrachten.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY
```

`<anchorPeersList>`is een door spaties gescheiden lijst met peer knooppunten die moeten worden ingesteld als een anker peer. Bijvoorbeeld:

  - Stel `<anchorPeersList>` in als ' Peer1 ' als u alleen Peer1-knoop punt wilt instellen als anker peer.
  - Stel `<anchorPeersList>` in als "Peer1" "peer3" als u het knoop punt Peer1 en peer3 wilt instellen als anker peer.

### <a name="consortium-management-commands"></a>Management-opdrachten voor consortium

>[!NOTE]
> Voordat u met een consortium bewerking begint, moet u ervoor zorgen dat de eerste installatie van de client toepassing wordt uitgevoerd.  

Voer de onderstaande opdrachten in de aangegeven volg orde uit om een peer-organisatie toe te voegen aan een kanaal en consortium
1.  Upload de MSP van peer-organisaties van de peer-organisatie-client op Azure Storage

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Down load het Azure Storage MSP-bestand van peer-organisaties van de organisatie-client van de orderer en geef vervolgens opdracht om peer-organisatie toe te voegen in Channel/consortium.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Met behulp van dit verbindings profiel kunt u vanuit de organisatie-client een orderer-verbindings profiel uploaden op Azure Storage zodat peer-organisatie verbinding kan maken met de bewerkings knooppunten

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Down load orderer-verbindings Profiel van de client van de peer-organisatie van Azure Storage en geef vervolgens opdracht om peer-knoop punten toe te voegen aan het kanaal

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Als u meer peer-organisaties in het kanaal wilt toevoegen, moet u peer-omgevings variabelen bijwerken volgens de vereiste peer-organisatie en de stappen 1 tot en met 4 uitvoeren.


### <a name="chaincode-management-commands"></a>Chaincode-beheer opdrachten

>[!NOTE]
> Voordat u met een chaincode-bewerking begint, moet u ervoor zorgen dat de eerste installatie van de client toepassing wordt uitgevoerd.  

**De onderstaande chaincode-specifieke omgevings variabelen instellen**

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

De onderstaande chaincode-bewerkingen kunnen worden uitgevoerd:  

- [Chaincode installeren](#install-chaincode)  
- [Chaincode instantiëren](#instantiate-chaincode)  
- [Chaincode aanroepen](#invoke-chaincode)
- [Query chaincode](#query-chaincode)


### <a name="install-chaincode"></a>Chaincode installeren  

Voer de onderstaande opdracht uit om chaincode op de peer-organisatie te installeren.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Er wordt chaincode geïnstalleerd op alle peer knooppunten van de peer-organisatie die is ingesteld in de ORGNAME-omgevings variabele. Als er twee of meer peer-organisaties in uw kanaal zijn en u chaincode wilt installeren, moet u deze opdracht afzonderlijk voor elke peer-organisatie uitvoeren.  

Volg de stappen:  

1.  Set `ORGNAME` en de `USER_IDENTITY` opdracht peerOrg1 en issue `./azhlf chaincode install` .  
2.  Set `ORGNAME` en de `USER_IDENTITY` opdracht peerOrg2 en issue `./azhlf chaincode install` .  

### <a name="instantiate-chaincode"></a>Chaincode instantiëren  

Voer vanuit peer-client toepassing de volgende opdracht uit om chaincode op het kanaal te instantiëren.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```
Geef de naam van de functie en de spaties gescheiden lijst met argumenten in `<instantiateFunc>` `<instantiateFuncArgs>` respectievelijk door. Bijvoorbeeld in chaincode_example02. go chaincode, voor het instantiëren van de chaincode `<instantiateFunc>` die is ingesteld op `init` `<instantiateFuncArgs>` "a" "2000" "b" "1000".

> [!NOTE]
> Voer de opdracht uit voor één van de peer-organisaties in het kanaal. Zodra de trans actie is verzonden naar de orderer, wordt deze trans actie door de orderer gedistribueerd naar alle peer-organisaties in het kanaal. Daarom wordt de chaincode geïnstantieerd op alle peer knooppunten op alle peer-organisaties in het kanaal.  


### <a name="invoke-chaincode"></a>Chaincode aanroepen  

Voer de volgende opdracht uit op de peer-client voor het aanroepen van de functie chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Geef de naam van de functie invoke en de door de ruimte gescheiden lijst met argumenten in  `<invokeFunction>`    `<invokeFuncArgs>`   respectievelijk door. Ga verder met het chaincode_example02. go chaincode-voor beeld om invoke-bewerking uit te voeren  `<invokeFunction>`   op  `invoke`    `<invokeFuncArgs>`   ' a ' "b" "10".  

>[!NOTE]
> Voer de opdracht uit voor één van de peer-organisaties in het kanaal. Zodra de trans actie is verzonden naar de orderer, wordt deze trans actie door de orderer gedistribueerd naar alle peer-organisaties in het kanaal. De wereld staat wordt daarom bijgewerkt op alle peer knooppunten van alle peer-organisaties in het kanaal.  


### <a name="query-chaincode"></a>Query chaincode  

Voer de onderstaande opdracht uit om een query uit te voeren op chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
Geef de naam van de query functie en de spaties gescheiden lijst met argumenten in  `<queryFunction>`    `<queryFuncArgs>`   respectievelijk. Als u chaincode_example02. go chaincode als referentie wilt uitvoeren, moet u de waarde ' a ' in de wereld status instellen  `<queryFunction>`   op  `query`  `<queryArgs>` a.  

## <a name="troubleshoot"></a>Problemen oplossen

**De versie van de actieve sjabloon controleren**

Voer de onderstaande opdrachten uit om de versie van de sjabloon implementatie te vinden.

Onder omgevings variabelen instellen volgens de resource groep waarin de sjabloon is geïmplementeerd.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
Voer de onderstaande opdracht uit om de sjabloon versie af te drukken
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```

## <a name="support-and-feedback"></a>Ondersteuning en feedback

Voor nieuws voer Azure Blockchain gaat u naar de [Azure Blockchain-blog](https://azure.microsoft.com/blog/topics/blockchain/) om op de hoogte te blijven van aanbiedingen van blockchainservices en informatie van het technische team van Azure Blockchain.

Als u feedback over producten wilt geven of nieuwe functies wilt aanvragen, kunt u een idee plaatsen of erop stemmen via het [Azure-feedbackforum voor blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Ondersteuning voor community

In contact komen met Microsoft-technici en experts uit de Azure Blockchain-community.

- [Micro soft Q&een vraag pagina voor de Azure Block Chain-Service](https://docs.microsoft.com/answers/topics/azure-blockchain-workbench.html). Technische ondersteuning voor Block Chain-sjablonen is beperkt tot implementatie problemen.
- [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)