---
title: '& privacy voor netwerk isolatie'
titleSuffix: Azure Machine Learning
description: Gebruik een geïsoleerde Azure-Virtual Network met Azure Machine Learning voor het beveiligen van experimenten/trainingen en voor het afleiden/scoren van taken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 58f0a25ebeb7fb715cfba27d7482a031d1fe8c32
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121201"
---
# <a name="network-isolation-during-training-and-inference-with-private-endpoints-and-virtual-networks"></a>Netwerk isolatie tijdens de training en demijnen met persoonlijke eind punten en virtuele netwerken
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u uw machine learning levenscyclus kunt beveiligen door Azure Machine Learning training te isoleren en taken in een Azure-Virtual Network (vnet) af te leiden. Een __virtueel netwerk__ fungeert als beveiligings grens en isoleert uw Azure-resources van het open bare Internet. U kunt ook een virtueel Azure-netwerk toevoegen aan uw on-premises netwerk. Door netwerken aan te koppelen, kunt u uw modellen veilig trainen en toegang verkrijgen tot uw geïmplementeerde modellen.

Azure Machine Learning-werk ruimte kan worden geopend vanuit een virtueel netwerk met behulp van een __persoonlijk eind punt__. Het persoonlijke eind punt is een set privé-IP-adressen in uw virtuele netwerk en de toegang tot uw werk ruimte is beperkt tot het virtuele netwerk. Het persoonlijke eind punt helpt het risico van gegevens exfiltration te verminderen. Zie het artikel over een [persoonlijke Azure-koppeling](/azure/private-link/private-link-overview) voor meer informatie over privé-eind punten.

> [!NOTE]
> U kunt problemen ondervinden bij het openen van een werk ruimte via een persoonlijk eind punt wanneer u gebruikmaakt van Mozilla Firefox. Het probleem kan zijn gerelateerd aan de instelling DNS via HTTPS in de browser. We raden u aan micro soft Edge of Google Chrome te gebruiken om dit probleem te omzeilen.

Azure Machine Learning is afhankelijk van andere Azure-Services voor gegevens opslag en reken bronnen (gebruikt voor het trainen en implementeren van modellen). Deze resources kunnen ook worden gemaakt in een virtueel netwerk. U kunt bijvoorbeeld Azure Machine Learning Compute gebruiken om een model te trainen en het model vervolgens implementeren in azure Kubernetes service (AKS). 

## <a name="prerequisites"></a>Vereisten

+ Een Azure Machine Learning- [werk ruimte](how-to-manage-workspace.md).

+ Algemene werk ervaring van zowel de [Azure Virtual Network-Service](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) als [IP-netwerken](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Een bestaand virtueel netwerk en subnet voor gebruik met uw reken resources.

+ Als u resources wilt implementeren in een virtueel netwerk of subnet, moet uw gebruikers account over machtigingen beschikken voor de volgende acties in op rollen gebaseerde toegangs beheer (RBAC) van Azure:

    - ' Micro soft. Network/virtualNetworks/lid/Action ' op de virtuele netwerk resource.
    - ' Micro soft. Network/virtualNetworks/subnet/lid/Action ' op de bron van het subnet.

    Zie voor meer informatie over RBAC met netwerken de [ingebouwde rollen voor netwerken](/azure/role-based-access-control/built-in-roles#networking)

## <a name="secure-your-workspace"></a>Uw werk ruimte beveiligen

Uw Azure Machine Learning-werk ruimte kan een __openbaar eind punt__ of een __persoonlijk eind punt__hebben. Een openbaar eind punt is een reeks IP-adressen die toegankelijk zijn op het open bare Internet, terwijl een persoonlijk eind punt een set privé-IP-adressen in een virtueel netwerk is. 

Gebruik een persoonlijk eind punt om de toegang tot uw werk ruimte te beperken tot alleen via de privé-IP-adressen.

Omdat de communicatie met de werk ruimte alleen is toegestaan vanuit het virtuele netwerk, moeten ontwikkel omgevingen die gebruikmaken van de werk ruimte lid zijn van het virtuele netwerk. Bijvoorbeeld een virtuele machine in het virtuele netwerk.

> [!IMPORTANT]
> Het persoonlijke eind punt is niet van invloed op Azure Control-vlak (beheer bewerkingen), zoals het verwijderen van de werk ruimte of het beheren van reken resources. Bijvoorbeeld maken, bijwerken of verwijderen van een berekenings doel. Deze bewerkingen worden normaal gesp roken uitgevoerd via het open bare Internet.
>
> Het persoonlijke eind punt voor komt toegang tot de werk ruimte van buiten het virtuele netwerk.

Voor sommige combi Naties van resources met een persoonlijk eind punt is een werk ruimte in de Enter prise-editie vereist. Gebruik de volgende tabel om te begrijpen welke scenario's Enter prise Edition vereist:

| Scenario | Onderneming</br>editie | Basic</br>editie |
| ----- |:-----:|:-----:| 
| Geen virtueel netwerk of persoonlijk eind punt | ✔ | ✔ |
| Werk ruimte zonder persoonlijk eind punt. Andere resources (met uitzonde ring van Azure Container Registry) in een virtueel netwerk | ✔ | ✔ |
| Werk ruimte zonder persoonlijk eind punt. Andere resources met een persoonlijk eind punt | ✔ | |
| Werk ruimte met een persoonlijk eind punt. Andere resources (met uitzonde ring van Azure Container Registry) in een virtueel netwerk | ✔ | ✔ |
| Werk ruimte en alle andere resources met een persoonlijk eind punt | ✔ | |
| Werk ruimte met een persoonlijk eind punt. Andere resources zonder persoonlijk eind punt of virtueel netwerk | ✔ | ✔ |
| Azure Container Registry in een virtueel netwerk | ✔ | |
| Door de klant beheerde sleutels voor de werk ruimte | ✔ | |

> [!WARNING]
> 
> Azure Machine Learning Compute-exemplaren worden niet ondersteund in een werk ruimte waar een persoonlijk eind punt is ingeschakeld.
>
> Azure Machine Learning biedt geen ondersteuning voor het gebruik van een Azure Kubernetes-service waarvoor een persoonlijk eind punt is ingeschakeld. In plaats daarvan kunt u de Azure Kubernetes-service in een virtueel netwerk gebruiken. Zie voor meer informatie [beveiligd Azure ml-experimenten en de functies voor invallen binnen een Azure-Virtual Network](how-to-enable-virtual-network.md).

Zie het artikel over een [persoonlijke Azure-koppeling](/azure/private-link/private-link-overview) voor meer informatie over privé-eind punten in Azure.

### <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Een werk ruimte maken die gebruikmaakt van een persoonlijk eind punt

U kunt een nieuwe werk ruimte maken met een persoonlijk eind punt met behulp van de Azure Machine Learning SDK, CLI, een Azure Resource Manager sjabloon of de Azure Portal.

__Vereisten__

* Het virtuele netwerk dat u met het persoonlijke eind punt gebruikt, moet netwerk beleid uitgeschakeld hebben. Zie [netwerk beleid voor een persoonlijk eind punt uitschakelen](/azure/private-link/disable-private-endpoint-network-policy)voor meer informatie.

__Beperkingen__

* U kunt geen verbinding maken met de werk ruimte via het open bare Internet, alleen vanuit het virtuele netwerk.

* Als er meerdere werk ruimten zijn gemaakt met behulp van privé-eind punten en deze dezelfde privé-DNS-zone gebruiken, wordt alleen de eerste werk ruimte toegevoegd aan de koppelingen van het __virtuele netwerk__ van de privé-DNS-zone.

    U kunt deze beperking omzeilen door de koppeling van het virtuele netwerk voor de extra werk ruimten hand matig toe te voegen. Zie [Wat is een virtuele netwerk koppeling](/azure/dns/private-dns-virtual-network-links)voor meer informatie.

__Configuratie__

Raadpleeg de volgende artikelen voor meer informatie over het maken van een werk ruimte met behulp van een virtueel netwerk en een persoonlijk eind punt, samen met andere configuratie opties:

* Een [Azure Resource Manager sjabloon gebruiken om een werk ruimte voor Azure machine learning te maken](how-to-create-workspace-template.md).
* [Werk ruimten maken in de portal](how-to-manage-workspace.md).
* [Maak werk ruimten met Azure cli](how-to-manage-workspace-cli.md).
* Als u de python-SDK wilt gebruiken, raadpleegt u de referentie documentatie voor [PrivateEndPointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.private_endpoint.privateendpointconfig?view=azure-ml-py) en [Workspace. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) .

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning Studio

__Vereisten__

* Om toegang te krijgen tot gegevens in een opslag account, moet het opslag account zich in hetzelfde virtuele netwerk bevinden als de werk ruimte.

* Als uw gegevens worden opgeslagen in een virtueel netwerk, moet u een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) voor een werk ruimte gebruiken om de toegang tot uw gegevens te verlenen aan de Studio.

    > [!IMPORTANT]
    > Hoewel het meren deel van Studio werkt met gegevens die zijn opgeslagen in een virtueel netwerk, hebben geïntegreerde notebooks __dit niet__. Geïntegreerde notebooks bieden geen ondersteuning voor het gebruik van opslag die zich in een virtueel netwerk bevindt. In plaats daarvan kunt u Jupyter-notebooks van een reken instantie gebruiken. Zie de sectie [compute clusters & instances](#compute-instance) voor meer informatie.

    Als u geen studio toegang verleent, ontvangt u deze fout, `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` de volgende bewerkingen zijn niet beschikbaar:

    * Bekijk de gegevens in de Studio.
    * Gegevens visualiseren in de ontwerp functie.
    * Een AutoML-experiment verzenden.
    * Een label project starten.

__Beperkingen__

* Azure Machine Learning Studio ondersteunt het lezen van gegevens uit de volgende gegevens opslag typen in een virtueel netwerk:

    * Azure Blob
    * Azure Data Lake Storage Gen1
    * Azure Data Lake Storage Gen2
    * Azure SQL Database

__Configuratie__

* __Configureer data stores voor het gebruik van een beheerde identiteit__ voor toegang tot uw gegevens. Met deze stappen wordt de beheerde identiteit van de werk ruimte als __lezer__ aan de opslag service toegevoegd met behulp van het Azure resource-based Access Control (RBAC). Met __Reader__ toegang kan de werk ruimte Firewall instellingen ophalen en ervoor zorgen dat gegevens het virtuele netwerk niet verlaten.

    1. Selecteer __gegevens opslag__in de Studio.

    1. Selecteer __+ Nieuw gegevens archief__als u een nieuwe gegevens opslag wilt maken. Als u een bestaand item wilt bijwerken, selecteert u het gegevens archief en selecteert u __referenties bijwerken__.

    1. Selecteer in de instellingen voor gegevens archief de optie __Ja__ __Als u wilt dat Azure machine learning-service toegang heeft tot de opslag met behulp van door werk ruimte beheerde identiteit__.

    > [!NOTE]
    > Het kan tot tien minuten duren voordat deze wijzigingen van kracht worden.

* Voor __Azure Blob Storage__moet de beheerde identiteit van de werk ruimte ook worden toegevoegd als een [gegevens lezer voor blobs](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , zodat deze gegevens uit de Blob-opslag kan lezen.

* De ontwerp functie gebruikt het opslag account dat aan uw werk ruimte is gekoppeld om standaard uitvoer op te slaan. U kunt het echter opgeven om de uitvoer op te slaan in een gegevens opslag waartoe u toegang hebt. Als uw omgeving gebruikmaakt van virtuele netwerken, kunt u deze besturings elementen gebruiken om ervoor te zorgen dat uw gegevens veilig en toegankelijk blijven. Een nieuwe standaard opslag voor een pijp lijn instellen:

    1. Selecteer in een pijp lijn concept het **tandwiel pictogram instellingen** in de buurt van de titel van de pijp lijn.
    1. Selecteer de vermelding **standaard gegevens archief selecteren** .
    1. Geef een nieuwe gegevens opslag op.

    U kunt ook de standaard gegevens opslag per module negeren. Dit geeft u de controle over de opslag locatie voor elke afzonderlijke module.

    1. Selecteer de module waarvan u de uitvoer wilt opgeven.
    1. Vouw de sectie **uitvoer instellingen** uit.
    1. Selecteer **standaard instellingen voor uitvoer negeren**.
    1. Selecteer **uitvoer instellingen instellen**.
    1. Geef een nieuwe gegevens opslag op.

* Als u __Azure data Lake Storage Gen2__gebruikt, kunt u zowel RBAC-als POSIX-toegangscontrole lijsten (acl's) gebruiken om de toegang tot gegevens binnen een virtueel netwerk te beheren.

    Als u RBAC wilt gebruiken, voegt u de beheerde identiteit van de werk ruimte toe aan de rol [BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Zie [Op rollen gebaseerd toegangsbeheer](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control) voor meer informatie.

    Als u Acl's wilt gebruiken, kunt u toegang krijgen tot de beheerde identiteit van de werk ruimte, net als bij andere beveiligings principes. Zie [toegangs beheer lijsten voor bestanden en mappen](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)voor meer informatie.

* __Azure data Lake Storage gen1__ ondersteunt alleen Access Control Lists in POSIX-stijl. U kunt de werk ruimte Managed Identity Access to resources net als andere beveiligings principes toewijzen. Zie [toegangs beheer in azure data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md)voor meer informatie.

* Als u toegang wilt krijgen tot gegevens die zijn opgeslagen in een __Azure SQL database__ met behulp van beheerde identiteit, moet u een SQL-Inge sloten gebruiker maken die is gekoppeld aan de beheerde identiteit. Zie voor meer informatie over het maken van een gebruiker van een externe provider [opgenomen gebruikers maken die zijn toegewezen aan Azure AD-identiteiten](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

    Nadat u een SQL-Inge sloten gebruiker hebt gemaakt, moet u er machtigingen voor verlenen met behulp van de [opdracht T-SQL toewijzen](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

* Als u __de Studio opent vanuit een resource binnen een virtueel netwerk__ (bijvoorbeeld een reken instantie of virtuele machine), moet u uitgaand verkeer van het virtuele netwerk naar de Studio toestaan. 

    Als u bijvoorbeeld netwerk beveiligings groepen (NSG) gebruikt om uitgaand verkeer te beperken, voegt __u een regel__ toe aan een servicetag bestemming __AzureFrontDoor.__ front-end.

## <a name="use-datastores-and-datasets"></a>Data stores en gegevens sets gebruiken

> [!NOTE]
> In deze sectie wordt het gebruik van gegevens opslag en gegevensset voor de SDK-ervaring besproken. Zie de sectie [machine learning Studio](#machine-learning-studio)voor meer informatie over de Studio-ervaring.

__Beperkingen__

Azure Machine Learning voert standaard gegevens over geldigheid en referenties controles uit wanneer u gegevens probeert te openen met behulp van de SDK. Als uw gegevens zich achter een virtueel netwerk bevindt, heeft Azure Machine Learning geen toegang tot de gegevens en mislukt de controles. U kunt dit probleem omzeilen door de validatie bij het maken van gegevens opslag en gegevens sets over te slaan.

* Bij gebruik van een __gegevens opslag__:

    Azure Data Lake Store gen1 en Azure Data Lake Store Gen2 de validatie standaard overs Laan, dus er is geen verdere actie nodig. Voor de volgende services kunt u echter soort gelijke syntaxis gebruiken om de validatie van gegevens opslag over te slaan:

    - Azure Blob Storage
    - Azure-bestands share
    - PostgreSQL
    - Azure SQL Database

    Met het volgende code voorbeeld maakt u een nieuwe Azure Blob-gegevens opslag en-sets `skip_validation=True` .

    ```python
    blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  
                                                            datastore_name=blob_datastore_name,  
                                                            container_name=container_name,  
                                                            account_name=account_name, 
                                                            account_key=account_key, 
                                                            skip_validation=True ) // Set skip_validation to true
    ```

* Wanneer u een __gegevensset__gebruikt:

    De syntaxis voor het overs laan van de validatie van de gegevensset is vergelijkbaar met de volgende typen gegevens sets:
    - Bestand met scheidings tekens
    - JSON 
    - Parquet
    - SQL
    - File

    Met de volgende code wordt een nieuwe JSON-gegevensset en-sets gemaakt `validate=False` .

    ```python
    json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 
                                                   validate=False) 
    ```

## <a name="azure-storage-account"></a>Azure Storage-account

> [!IMPORTANT]
> U kunt het _standaard opslag account_ voor Azure machine learning en _niet-standaard Storage-accounts_ in een virtueel netwerk plaatsen.

__Vereisten__

* Het opslag account moet zich in hetzelfde virtuele netwerk en subnet bevinden als de reken instanties of clusters die worden gebruikt voor de training of de interferentie.

__Configuratie__

Als u het Azure Storage-account wilt beveiligen dat door uw werk ruimte wordt gebruikt, schakelt u een __persoonlijk eind punt__ of een __service-eind punt__ in voor het opslag account in uw virtuele netwerk.

* Zie het artikel [privé-eind punten gebruiken](/azure/storage/common/storage-private-endpoints.md) om het opslag account te configureren voor het gebruik van een __persoonlijk eind punt__.

* Als u het opslag account wilt configureren voor het gebruik van een __service-eind punt__, gebruikt u de volgende stappen:

    1. Als u het opslag account wilt toevoegen aan het virtuele netwerk dat door uw werk ruimte wordt gebruikt, gebruikt u de informatie in de sectie __toegang verlenen vanuit een virtueel netwerk__ van het artikel [Azure Storage firewalls en virtuele netwerken configureren](/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) .
    1. Als u toegang wilt toestaan vanuit micro soft-Services op het virtuele netwerk (zoals Azure Machine Learning), gebruikt u de informatie in de sectie __uitzonde ringen__ van het artikel [Azure Storage firewalls en virtuele netwerken configureren](/azure/storage/common/storage-network-security#exceptions) .
    1. Wanneer u werkt met de Azure Machine Learning SDK, moet uw ontwikkel omgeving verbinding kunnen maken met het Azure Storage-account. Wanneer het opslag account zich in een virtueel netwerk bevindt, moet de firewall toegang toestaan vanuit het IP-adres van de ontwikkel omgeving. Als u het IP-adres van de ontwikkel omgeving wilt toevoegen, gebruikt u de informatie in de sectie __toegang verlenen vanuit een IP-bereik voor Internet__ van het artikel [Azure Storage firewalls en virtuele netwerken configureren](/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range) .

## <a name="azure-container-registry"></a>Azure Container Registry

__Vereisten__

* Uw Azure Machine Learning-werk ruimte moet Enter prise Edition zijn. Zie voor meer informatie over het uitvoeren van een [upgrade naar Enter prise Edition](how-to-manage-workspace.md#upgrade).
* De regio voor de Azure Machine Learning werkruimte moet een [regio voor persoonlijke koppelingen zijn ingeschakeld](https://docs.microsoft.com/azure/private-link/private-link-overview#availability). 
* Uw Azure Container Registry moet Premium-versie zijn. Zie [wijzigen van sku's](/azure/container-registry/container-registry-skus#changing-skus)voor meer informatie over het uitvoeren van upgrades.
* Uw Azure Container Registry moeten zich in hetzelfde virtuele netwerk en subnet bevinden als het opslag account en reken doelen die worden gebruikt voor training of deinterferentie.
* Uw Azure Machine Learning-werk ruimte moet een [Azure machine learning Compute-Cluster](how-to-set-up-training-targets.md#amlcompute)bevatten.

__Beperkingen__

* Wanneer ACR zich achter een virtueel netwerk bevindt, kan Azure Machine Learning het niet gebruiken om docker-installatie kopieën rechtstreeks te bouwen. In plaats daarvan wordt het berekenings cluster gebruikt voor het bouwen van de installatie kopieën.

__Configuratie__

1. Gebruik een van de volgende methoden om de naam van de Azure Container Registry voor uw werk ruimte te vinden:

    __Azure-portal__

    Vanuit het gedeelte Overzicht van uw werk ruimte koppelt de __register__ waarde aan de Azure container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry voor de werk ruimte" border="true":::

    __Azure-CLI__

    Als u [de machine learning extensie voor Azure cli hebt geïnstalleerd](reference-azure-machine-learning-cli.md), kunt u de `az ml workspace show` opdracht gebruiken om de werkruimte gegevens weer te geven.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Het laatste deel van de teken reeks is de naam van de Azure Container Registry voor de werk ruimte.

1. Als u de toegang tot het virtuele netwerk wilt beperken, gebruikt u de stappen in [netwerk toegang voor REGI ster configureren](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Wanneer u het virtuele netwerk toevoegt, selecteert u het virtuele netwerk en subnet voor uw Azure Machine Learning-resources.

1. Gebruik de Azure Machine Learning python-SDK om een berekenings cluster te configureren voor het bouwen van docker-installatie kopieën. In het volgende code fragment ziet u hoe u dit doet:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Uw opslag account, reken cluster en Azure Container Registry moeten zich allemaal in hetzelfde subnet van het virtuele netwerk bevinden.
    
    Zie voor meer informatie de verwijzing naar methode [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) .

1. Als u uw werk ruimte wilt gebruiken om te communiceren met het ACR-exemplaar, past u de volgende Azure Resource Manager sjabloon toe:

    > [!WARNING]
    > Met deze sjabloon wordt een persoonlijk eind punt voor uw werk ruimte ingeschakeld en gewijzigd in een werk ruimte in de onderneming. U kunt deze wijzigingen niet ongedaan maken.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="key-vault-instance"></a>Sleutel kluis-exemplaar 

__Vereisten__

__Beperkingen__

__Configuratie__ 

Als u Azure Machine Learning experimenten wilt gebruiken met Azure Key Vault achter een virtueel netwerk, gebruikt u het artikel [Azure Key Vault firewalls en virtuele netwerken configureren](/azure/key-vault/general/network-security) .

> [!IMPORTANT]
> Wanneer u de stappen in het artikel hebt uitgevoerd, gebruikt u hetzelfde virtuele netwerk als voor uw experimenten-reken resources. U moet ook __vertrouwde micro soft-Services toestaan deze firewall over te slaan__.

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>& exemplaren van compute-clusters 

__Vereisten__

* Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio bevinden als de Azure Machine Learning-werk ruimte.
* Het subnet dat is opgegeven voor het reken proces of het cluster moet voldoende niet-toegewezen IP-adressen hebben om het aantal Vm's te kunnen ondersteunen. Als het subnet onvoldoende niet-toegewezen IP-adressen heeft, wordt een reken cluster gedeeltelijk toegewezen.
* Als u van plan bent om het virtuele netwerk te beveiligen door verkeer te beperken, moeten sommige poorten open blijven voor de compute-service.
* Als u meerdere reken instanties of clusters in één virtueel netwerk wilt plaatsen, moet u mogelijk een quotum verhoging aanvragen voor een of meer van uw resources.
* Als de Azure Storage account (s) voor de werk ruimte ook worden beveiligd in een virtueel netwerk, moeten ze zich in hetzelfde virtuele netwerk bevinden als de Azure Machine Learning Reken instantie of het cluster. 
* Zorg ervoor dat de communicatie tussen websockets niet is uitgeschakeld voor de Jupyter-functionaliteit van reken instanties.

__Beperkingen__

* Het Machine Learning Reken exemplaar of cluster wijst automatisch extra netwerk bronnen toe __aan de resource groep die het virtuele netwerk bevat__. Voor elk reken exemplaar of cluster wijst de service de volgende bronnen toe:

    * Eén netwerk beveiligings groep
    * Eén openbaar IP-adres
    * Een load balancer
    
    Voor __reken clusters__worden deze bronnen verwijderd (en opnieuw gemaakt) telkens wanneer het cluster omlaag wordt geschaald naar 0 knoop punten.
    
    Voor een __reken instantie__ worden de resources bewaard totdat de instantie wordt verwijderd (stoppen de resources worden niet verwijderd).

    De beperkingen die voor deze resources gelden, worden bepaald door de [resourcequota](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) van het abonnement.

__Configuratie__

* Als u een Machine Learning Compute cluster wilt maken, gebruikt u de volgende stappen:

    1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com/)en selecteer vervolgens uw abonnement en werk ruimte.
    1. Selecteer __Compute__ aan de linkerkant.
    1. Selecteer __trainings clusters__ in het midden en selecteer deze __+__ .
    1. Vouw in het dialoog venster __nieuw trainings cluster__ het gedeelte __Geavanceerde instellingen__ uit.
    1. Als u deze reken resource wilt configureren voor het gebruik van een virtueel netwerk, voert u de volgende acties uit in de sectie __virtueel netwerk configureren__ :

        1. Selecteer de resource groep met het virtuele netwerk in de vervolg keuzelijst __resource groep__ .
        1. Selecteer in de vervolg keuzelijst __virtueel netwerk__ het virtuele netwerk dat het subnet bevat.
        1. Selecteer in de vervolg keuzelijst __subnet__ het subnet dat u wilt gebruiken.

    ![De instellingen van het virtuele netwerk voor Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

    U kunt ook een Machine Learning Compute cluster maken met behulp van de Azure Machine Learning SDK. Met de volgende code wordt een nieuw Machine Learning Compute Cluster gemaakt in het `default` subnet van een virtueel netwerk met de naam `mynetwork` :

    ```python
    from azureml.core.compute import ComputeTarget, AmlCompute
    from azureml.core.compute_target import ComputeTargetException

    # The Azure virtual network name, subnet, and resource group
    vnet_name = 'mynetwork'
    subnet_name = 'default'
    vnet_resourcegroup_name = 'mygroup'

    # Choose a name for your CPU cluster
    cpu_cluster_name = "cpucluster"

    # Verify that cluster does not exist already
    try:
        cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
        print("Found existing cpucluster")
    except ComputeTargetException:
        print("Creating new cpucluster")

        # Specify the configuration for the new cluster
        compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            min_nodes=0,
                                                            max_nodes=4,
                                                            vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                            vnet_name=vnet_name,
                                                            subnet_name=subnet_name)

        # Create the cluster with the specified name and configuration
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

        # Wait for the cluster to be completed, show the output log
        cpu_cluster.wait_for_completion(show_output=True)
    ```

    Wanneer het maken van het proces is voltooid, traint u uw model met behulp van het cluster in een experiment. Zie voor meer informatie [een berekenings doel selecteren en gebruiken voor training](how-to-set-up-training-targets.md).

    [!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

* Als u notitie blokken op een Azure Compute-instantie gebruikt, moet u ervoor zorgen dat uw notitie blok wordt uitgevoerd op een reken resource achter hetzelfde virtuele netwerk en subnet als uw gegevens. 

    Configureer uw reken instantie zodat deze zich in hetzelfde virtuele netwerk bevindt tijdens het maken van **Geavanceerde instellingen**  >  .**Configureer het virtuele netwerk**. U kunt een bestaand reken exemplaar niet toevoegen aan een virtueel netwerk.

* Als u van plan bent het virtuele netwerk te beveiligen door het netwerk verkeer naar/van het open bare Internet te beperken, moet u binnenkomende communicaties van de Azure Batch-service toestaan.

    De batch-service voegt netwerk beveiligings groepen (Nsg's) toe op het niveau van netwerk interfaces (Nic's) die zijn gekoppeld aan Vm's. Met deze netwerkbeveiligingsgroepen worden automatisch binnenkomende en uitgaande regels geconfigureerd om het volgende verkeer toe te staan:

    - Binnenkomend TCP-verkeer op de poorten 29876 en 29877 van een __service label__ van __BatchNodeManagement__.

    - Beschrijving Binnenkomend TCP-verkeer op poort 22 om externe toegang toe te staan. Gebruik deze poort alleen als u verbinding wilt maken met behulp van SSH op het open bare IP-adres.

    - Uitgaand verkeer op een willekeurige poort naar het virtuele netwerk.

    - Uitgaand verkeer op een willekeurige poort naar internet.

    - Voor het binnenkomende TCP-verkeer van reken instanties op poort 44224 van een __service-tag__ van __AzureMachineLearning__.

    > [!IMPORTANT]
    > Wees voorzichtig als u binnenkomende of uitgaande regels toevoegt of wijzigt in netwerkbeveiligingsgroepen die door Batch zijn geconfigureerd. Als een NSG communicatie met de reken knooppunten blokkeert, stelt de compute-service de status van de reken knooppunten in op onbruikbaar.
    >
    > U hoeft Nsg's niet op subnetniveau op te geven omdat de Azure Batch-service zijn eigen Nsg's configureert. Als er echter een Nsg's of een firewall is gekoppeld aan het subnet met de Azure Machine Learning compute, moet u ook het verkeer toestaan dat hierboven wordt vermeld.

* Als u de standaard regels voor uitgaande verbindingen niet wilt gebruiken en u de uitgaande toegang van uw virtuele netwerk wilt beperken, gebruikt u de volgende stappen:

    1. Uitgaande Internet verbinding weigeren met behulp van de NSG-regels.
    1. Beperk het uitgaande verkeer voor een __reken instantie__ of een __berekenings cluster__tot de volgende items:
        - Azure Storage, door gebruik te maken van de __service tag__ __Storage. regionaam__. Waar `{RegionName}` de naam is van een Azure-regio.
        - Azure Container Registry, met behulp van de __service-tag__ __AzureContainerRegistry. regionaam__. Waar `{RegionName}` de naam is van een Azure-regio.
        - Azure Machine Learning, met behulp van het __service label__ __AzureMachineLearning__
        - Azure Resource Manager, met behulp van het __service label__ __AzureResourceManager__
        - Azure Active Directory, met behulp van het __service label__ __AzureActiveDirectory__

    > [!NOTE]
    > Als u van plan bent om standaard-docker-installatie kopieën te gebruiken die door micro soft worden meegeleverd en door de gebruiker beheerde afhankelijkheden in te scha kelen, moet u ook de volgende __service Tags__gebruiken:
    >
    > * __MicrosoftContainerRegistry__
    > * __AzureFrontDoor.FirstParty__
    >
    > Deze configuratie is vereist wanneer u code die vergelijkbaar is met de volgende fragmentten als onderdeel van uw trainings scripts:
    >
    > __RunConfig-training__
    > ```python
    > # create a new runconfig object
    > run_config = RunConfiguration()
    > 
    > # configure Docker 
    > run_config.environment.docker.enabled = True
    > # For GPU, use DEFAULT_GPU_IMAGE
    > run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
    > run_config.environment.python.user_managed_dependencies = True
    > ```
    >
    > __Estimator-training__
    > ```python
    > est = Estimator(source_directory='.',
    >                 script_params=script_params,
    >                 compute_target='local',
    >                 entry_script='dummy_train.py',
    >                 user_managed=True)
    > run = exp.submit(est)
    > ```

* Als u gebruik wilt maken van [geforceerde tunneling](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) met Azure machine learning compute, moet u communicatie met het open bare Internet toestaan vanaf het subnet dat de reken resource bevat. Deze communicatie wordt gebruikt voor het plannen van taken en het openen van Azure Storage.

    U kunt dit op twee manieren doen:

    * Gebruik een [Virtual Network NAT](../virtual-network/nat-overview.md). Een NAT-gateway biedt een uitgaande Internet verbinding voor een of meer subnetten in het virtuele netwerk. Zie [virtuele netwerken ontwerpen met NAT-gateway bronnen](../virtual-network/nat-gateway-resource.md)voor meer informatie.

    * Door de [gebruiker gedefinieerde routes (udr's)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) toevoegen aan het subnet dat de reken resource bevat. Stel een UDR in voor elk IP-adres dat wordt gebruikt door de Azure Batch-service in de regio waar uw resources bestaan. Deze Udr's inschakelen de batch-service om te communiceren met reken knooppunten voor het plannen van taken. Voeg ook het IP-adres voor de Azure Machine Learning-service toe waarin de resources bestaan, omdat dit vereist is voor toegang tot reken instanties. Gebruik een van de volgende methoden om een lijst met IP-adressen van de batch-service en Azure Machine Learning-service te verkrijgen:

        * Down load de [Azure IP-bereiken en-service Tags](https://www.microsoft.com/download/details.aspx?id=56519) en zoek het bestand voor `BatchNodeManagement.<region>` en `AzureMachineLearning.<region>` , waar `<region>` is uw Azure-regio.

        * Gebruik de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om de informatie te downloaden. In het volgende voor beeld worden de IP-adres gegevens gedownload en worden de gegevens voor de regio VS Oost 2 gefilterd:

            ```azurecli-interactive
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
            ```
        
        Wanneer u de Udr's toevoegt, definieert u de route voor elk gerelateerde IP-adres voorvoegsel voor batch en stelt u het __type volgende hop__ in op __Internet__. 

        Naast de Udr's die u definieert, moet uitgaand verkeer naar Azure Storage worden toegestaan via uw on-premises netwerk apparaat. De Url's voor dit verkeer zijn met name in de volgende formulieren: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` en `<account>.blob.core.windows.net` . 

        Zie [een Azure batch groep maken in een virtueel netwerk](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)voor meer informatie.

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

__Vereisten__

* Het exemplaar van de Azure Kubernetes-service (AKS) en het virtuele Azure-netwerk moeten zich in dezelfde regio bevinden. Als u de Azure Storage account (s) die door de werk ruimte in een virtueel netwerk worden gebruikt, beveiligt, moeten ze zich in hetzelfde virtuele netwerk bevinden als het AKS-exemplaar.

* Als u __de IP-adres Sering__ voor uw cluster wilt plannen, volgt u de vereisten in het artikel [geavanceerde netwerken configureren in azure KUBERNETES service (AKS)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) .

* Als u de inkomende en uitgaande communicatie met het AKS-cluster wilt beperken, volgt u de richt lijnen in het artikel uitgaand [verkeer controleren voor cluster knooppunten in azure Kubernetes service](/azure/aks/limit-egress-traffic) om ervoor te zorgen dat _uitgaande_ communicatie van AKS op de juiste wijze is geconfigureerd. De _inkomende_ communicatie vereisten, indien van toepassing, worden aangeroepen in de onderstaande configuratie sectie.

__Beperkingen__

* Als u een Azure Kubernetes-service wilt gebruiken waarvoor een persoonlijke koppeling is ingeschakeld, moet u deze koppelen aan uw werk ruimte. U kunt geen Azure Kubernetes-service cluster maken met een persoonlijke koppeling van Azure Machine Learning (SDK, portal, CLI, enzovoort).

__Configuratie__

> [!IMPORTANT]
> Deze sectie bevat meerdere configuraties. Selecteer het abonnement dat het beste bij uw behoeften past.

* Als u __AKS achter het virtuele netwerk wilt gebruiken, met een openbaar Load Balancer__:

    1. Maak of koppel het AKS-cluster. Als u een nieuw cluster __maakt__ , moet u het virtuele netwerk opgeven dat u voor het cluster wilt gebruiken.
    
        In het volgende voor beeld ziet u hoe u een nieuw AKS-cluster maakt met behulp van de python-SDK:

        ```python
        from azureml.core.compute import ComputeTarget, AksCompute

        # Create the compute configuration and set virtual network information
        config = AksCompute.provisioning_configuration(location="eastus2")
        config.vnet_resourcegroup_name = "mygroup"
        config.vnet_name = "mynetwork"
        config.subnet_name = "default"
        config.service_cidr = "10.0.0.0/16"
        config.dns_service_ip = "10.0.0.10"
        config.docker_bridge_cidr = "172.17.0.1/16"

        # Create the compute target
        aks_target = ComputeTarget.create(workspace=ws,
                                        name="myaks",
                                        provisioning_configuration=config)
        ```

        Als u een bestaand AKS-cluster hebt dat zich al achter het virtuele netwerk bevindt, gebruikt u de informatie in het artikel [implementeren in azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    1. Zorg ervoor dat voor de netwerk beveiligings groep die het virtuele netwerk beheert een __binnenkomende__ beveiligings regel is ingeschakeld voor het Score-eind punt, zodat deze kan worden aangeroepen buiten het virtuele netwerk.

* Als u __AKS achter het virtuele netwerk wilt gebruiken, met een persoonlijke Load Balancer__:

    1. Maak of koppel het AKS-cluster. Als u een nieuw cluster __maakt__ , moet u het virtuele netwerk opgeven dat u voor het cluster wilt gebruiken.
    
        In het volgende voor beeld ziet u hoe u een nieuw AKS-cluster maakt met behulp van de python-SDK:

        ```python
        from azureml.core.compute import ComputeTarget, AksCompute

        # Create the compute configuration and set virtual network information
        config = AksCompute.provisioning_configuration(location="eastus2")
        config.vnet_resourcegroup_name = "mygroup"
        config.vnet_name = "mynetwork"
        config.subnet_name = "default"
        config.service_cidr = "10.0.0.0/16"
        config.dns_service_ip = "10.0.0.10"
        config.docker_bridge_cidr = "172.17.0.1/16"

        # Create the compute target
        aks_target = ComputeTarget.create(workspace=ws,
                                        name="myaks",
                                        provisioning_configuration=config)
        ```

        Als u een bestaand AKS-cluster hebt dat zich al achter het virtuele netwerk bevindt, gebruikt u de informatie in het artikel [implementeren in azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    1. Gebruik de volgende Azure CLI-opdrachten om de service-principal of de beheerde identiteits-ID voor AKS te vinden. Vervang door `<aks-cluster-name>` de naam van het cluster. Vervang door `<resource-group-name>` de naam van de resource groep die _het AKS-cluster bevat_:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
        ``` 

        Als met deze opdracht een waarde wordt geretourneerd van `msi` , gebruikt u de volgende opdracht om de principal-id voor de beheerde identiteit te identificeren:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
        ```
    1. Gebruik de volgende opdracht om de ID te vinden van de resource groep die het virtuele netwerk bevat. Vervang door `<resource-group-name>` de naam van de resource groep die _het virtuele netwerk bevat_:

        ```azurecli-interactive
        az group show -n <resource-group-name> --query id
        ```

    1. Gebruik de volgende opdracht om de service-principal of beheerde identiteit als Inzender voor het netwerk toe te voegen. Vervang door `<SP-or-managed-identity>` de id die wordt geretourneerd voor de service-principal of beheerde identiteit. Vervang door `<resource-group-id>` de id die wordt geretourneerd voor de resource groep die het virtuele netwerk bevat:

        ```azurecli-interactive
        az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
        ```

    1. Als u het AKS-cluster wilt bijwerken om een __privé-Load Balancer__te gebruiken, gebruikt u de PYTHON-SDK. Het volgende code fragment laat zien hoe u een bestaand AKS-cluster bijwerkt dat is toegevoegd aan of gekoppeld aan de werk ruimte:
    
        ```python
        import azureml.core
        from azureml.core.compute.aks import AksUpdateConfiguration
        from azureml.core.compute import AksCompute

        # ws = workspace object. Creation not shown in this snippet
        aks_target = AksCompute(ws,"myaks")

        # Change to the name of the subnet that contains AKS
        subnet_name = "default"
        # Update AKS configuration to use an internal load balancer
        update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
        aks_target.update(update_config)
        # Wait for the operation to complete
        aks_target.wait_for_completion(show_output = True)
        ```

* __AKS koppelen met behulp van een persoonlijk eind punt__:

    1. Gebruik de volgende Azure CLI-opdracht om de __subnet-id__ op te halen van het subnet dat door het AKS-cluster wordt gebruikt. Bijvoorbeeld het standaard subnet voor het virtuele netwerk:
    
        ```azurecli
        az network vnet show -g myresourcegroup -n myvnet --query subnets[].id
        ```
        
        Met deze opdracht wordt een matrix met Id's geretourneerd voor de subnetten in het virtuele netwerk. De volgende JSON is een voor beeld van een virtueel netwerk dat slechts één subnet heeft:

        ```json
        [
            "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/default"
        ]
        ```

        Als er meerdere Id's worden geretourneerd, selecteert u de id die u wilt gebruiken.

    1. Als u een AKS-cluster wilt maken een persoonlijk eind punt, gebruikt u de informatie in de sectie __geavanceerde netwerken__ van het artikel [een persoonlijk Azure Kubernetes service-cluster maken](/azure/aks/private-clusters#advanced-networking) . Gebruik bij het maken van het cluster de subnet-ID uit de vorige opdracht met de `--vnet-subnet-id` para meter.

    1. Als u het cluster wilt koppelen, gebruikt u de informatie in het artikel [implementeren naar Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    > [!TIP]
    > Wanneer u deze configuratie gebruikt en uitgaande communicatie vanuit het AKS-cluster beperkt, hoeft u geen communicatie toe te staan op de poorten __1194__ en __9000__. Raadpleeg de richt lijnen in het artikel uitgaand [verkeer voor cluster knooppunten in azure Kubernetes service](/azure/aks/limit-egress-traffic) voor meer poorten die moeten worden toegestaan.

## <a name="use-azure-container-instances-aci"></a>Azure Container Instances (ACI) gebruiken

__Vereisten__

* Azure Container Instances worden dynamisch gemaakt bij het implementeren van een model. Als u wilt dat Azure Machine Learning ACI in het virtuele netwerk maakt, moet u __subnet delegering__ inschakelen voor het subnet dat wordt gebruikt door de implementatie.

__Beperkingen__

* Het virtuele netwerk moet zich in dezelfde resource groep bevinden als uw Azure Machine Learning-werk ruimte.

* De Azure Container Registry (ACR) voor uw werk ruimte kan zich ook niet in het virtuele netwerk bevinden.

__Configuratie__

Als u ACI wilt gebruiken in een virtueel netwerk naar uw werk ruimte, gebruikt u de volgende stappen:

1. Als u subnet delegering wilt inschakelen voor het virtuele netwerk, gebruikt u de informatie in het artikel [een subnet delegering toevoegen of verwijderen](../virtual-network/manage-subnet-delegation.md) . U kunt delegeren inschakelen bij het maken van een virtueel netwerk of het toevoegen aan een bestaand netwerk.

    > [!IMPORTANT]
    > Wanneer delegering wordt ingeschakeld, gebruikt u `Microsoft.ContainerInstance/containerGroups` als het __subnet aan service waarde delegeren__ .

2. Implementeer het model met behulp van [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), gebruik de `vnet_name` `subnet_name` para meters en. Stel deze para meters in op de naam van het virtuele netwerk en het subnet waar u delegering hebt ingeschakeld.

## <a name="azure-databricks"></a>Azure Databricks

__Vereisten__

* Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio bevinden als de Azure Machine Learning-werk ruimte.
* Als de Azure Storage account (s) voor de werk ruimte ook worden beveiligd in een virtueel netwerk, moeten ze zich in hetzelfde virtuele netwerk bevinden als het Azure Databricks-cluster.
* Naast de __databricks-particuliere__ en __databricks-open bare__ subnetten die worden gebruikt door Azure Databricks, is ook het __standaard__ subnet dat voor het virtuele netwerk is gemaakt, vereist.

__Beperkingen__

__Configuratie__

Zie [deploying Azure Databricks in uw Azure Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)voor specifieke informatie over het gebruik van Azure Databricks met een virtueel netwerk.

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtuele machine of HDInsight-cluster

__Vereisten__

* Azure Machine Learning ondersteunt alleen virtuele machines waarop Ubuntu wordt uitgevoerd.
* De SSH-poort moet zijn ingeschakeld op de virtuele machine of in het HDInsight-cluster.

__Beperkingen__

__Configuratie__

1. Maak een virtuele machine of een HDInsight-cluster met behulp van de Azure Portal of de Azure CLI en plaats het cluster in een virtueel Azure-netwerk. Raadpleeg voor meer informatie de volgende artikelen:

    * [Virtuele Azure-netwerken voor Linux-Vm's maken en beheren](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [HDInsight uitbreiden met behulp van een virtueel Azure-netwerk](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Er wordt automatisch een NSG gemaakt voor Azure Virtual Machines op basis van Linux. Met deze NSG is toegang tot poort 22 vanaf elke bron toegestaan. Als u de toegang tot de SSH-poort wilt beperken, moet u toegang vanaf Azure Machine Learning toestaan. Als u de toegang voor Azure ML wilt behouden, moet u toegang toestaan vanuit een __bron service__ met het __bron service label__ __AzureMachineLearning__. Met de volgende Azure CLI-opdrachten wijzigt u de SSH-regel bijvoorbeeld zodat alleen toegang vanaf Azure Machine Learning wordt toegestaan.

    ```azurecli
    # Get default SSH rule
    nsgrule=$(az network nsg rule list --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --query [0].name -o tsv)
    # Update network security group rule to limit SSH to source service.
    az network nsg rule update --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
    --name $nsgrule --protocol tcp --direction inbound --priority 100 \
    --source-address-prefix AzureMachineLearning --source-port-range '*' --destination-address-prefix '*' \
    --destination-port-range 22 --access allow
    ```

    Zie de sectie [netwerk beveiligings groepen maken](/azure/virtual-machines/linux/tutorial-virtual-network#create-network-security-groups) in het artikel virtuele Azure-netwerken voor virtuele Linux-machines voor meer informatie.
    
    Behoud de standaard regels voor uitgaande verbindingen voor de netwerk beveiligings groep. Zie de standaard beveiligings regels in [beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)voor meer informatie.

1. Koppel de virtuele machine of het HDInsight-cluster aan uw Azure Machine Learning-werk ruimte. Zie [Compute-doelen voor model training instellen](how-to-set-up-training-targets.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Gebruik Azure machine learning werk ruimte achter Azure firewall](how-to-access-azureml-behind-firewall.md).
* [Een trainingsomgeving instellen](how-to-set-up-training-targets.md)
* [Privé-eind punten instellen](how-to-configure-private-link.md)
* [Waar modellen te implementeren](how-to-deploy-and-where.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
