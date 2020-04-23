---
title: Beveiliging voor bedrijven
titleSuffix: Azure Machine Learning
description: 'Gebruik Azure Machine Learning veilig: verificatie, autorisatie, netwerkbeveiliging, gegevensversleuteling en bewaking.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 1af1a1ccd8bff8fc4b578ecdeec3ac5f7c2352b1
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082132"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Bedrijfsbeveiliging voor Azure Machine Learning

In dit artikel vindt u meer informatie over beveiligingsfuncties die beschikbaar zijn voor Azure Machine Learning.

Wanneer u een cloudservice gebruikt, is het best practice om de toegang te beperken tot alleen de gebruikers die deze nodig hebben. Begin met het begrijpen van het verificatie- en autorisatiemodel dat door de service wordt gebruikt. U ook de toegang tot het netwerk beperken of veilig resources in uw on-premises netwerk met de cloud aansluiten. Gegevensversleuteling is ook van vitaal belang, zowel in rust als terwijl gegevens tussen services worden verplaatst. Ten slotte moet u de service kunnen controleren en een controlelogboek van alle activiteiten kunnen opstellen.

> [!NOTE]
> De informatie in dit artikel werkt met de Azure Machine Learning Python SDK-versie 1.0.83.1 of hoger.

## <a name="authentication"></a>Verificatie

Meervoudige verificatie wordt ondersteund als Azure Active Directory (Azure AD) is geconfigureerd om deze te gebruiken. Hier is het verificatieproces:

1. De client meldt zich aan bij Azure AD en krijgt een Azure Resource Manager-token.  Gebruikers en serviceprincipals worden volledig ondersteund.
1. De client presenteert het token aan Azure Resource Manager en aan alle Azure Machine Learning.
1. De Machine Learning-service biedt een Machine Learning-servicetoken aan het rekendoel van de gebruiker (bijvoorbeeld Machine Learning Compute). Dit token wordt gebruikt door het berekendoel van de gebruiker om terug te bellen naar de Machine Learning-service nadat de run is voltooid. Het bereik is beperkt tot de werkruimte.

[![Verificatie in Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Zie [Verificatie instellen voor Azure Machine Learning-resources en -werkstromen voor](how-to-setup-authentication.md)meer informatie. In dit artikel vindt u informatie en voorbeelden over verificatie, waaronder het gebruik van serviceprincipals en geautomatiseerde workflows.

### <a name="authentication-for-web-service-deployment"></a>Verificatie voor implementatie van webservice

Azure Machine Learning ondersteunt twee vormen van verificatie voor webservices: sleutel en token. Elke webservice kan slechts één vorm van verificatie tegelijk inschakelen.

|Verificatiemethode|Beschrijving|Azure Container Instances|AKS|
|---|---|---|---|
|Sleutel|Toetsen zijn statisch en hoeven niet te worden vernieuwd. Toetsen kunnen handmatig worden geregenereerd.|Standaard uitgeschakeld| Standaard ingeschakeld|
|Token|Tokens verlopen na een bepaalde periode en moeten worden vernieuwd.| Niet beschikbaar| Standaard uitgeschakeld |

Zie de [sectie webserviceverificatie](how-to-setup-authentication.md#web-service-authentication)voor codevoorbeelden .

## <a name="authorization"></a>Autorisatie

U meerdere werkruimten maken en elke werkruimte kan door meerdere personen worden gedeeld. Wanneer u een werkruimte deelt, u de toegang tot de werkruimte beheren door deze rollen toe te wijzen aan gebruikers:

* Eigenaar
* Inzender
* Lezer

In de volgende tabel worden enkele van de belangrijkste Azure Machine Learning-bewerkingen en de rollen weergegeven die deze kunnen uitvoeren:

| Azure Machine Learning-bewerking | Eigenaar | Inzender | Lezer |
| ---- |:----:|:----:|:----:|
| Werkruimte maken | ✓ | ✓ | |
| Werkruimte delen | ✓ | |  |
| Werkruimte upgraden naar Enterprise-editie | ✓ | |
| Compute-doel maken | ✓ | ✓ | |
| Compute-doel koppelen | ✓ | ✓ | |
| Gegevensopslag bijvoegen | ✓ | ✓ | |
| Experiment uitvoeren | ✓ | ✓ | |
| Runs/metrics weergeven | ✓ | ✓ | ✓ |
| Model registreren | ✓ | ✓ | |
| Afbeelding maken | ✓ | ✓ | |
| Webservice implementeren | ✓ | ✓ | |
| Modellen/afbeeldingen weergeven | ✓ | ✓ | ✓ |
| Webservice bellen | ✓ | ✓ | ✓ |

Als de ingebouwde rollen niet aan uw behoeften voldoen, u aangepaste rollen maken. Aangepaste rollen worden alleen ondersteund voor bewerkingen op de werkruimte en Machine Learning Compute. Aangepaste rollen kunnen machtigingen lezen, schrijven of verwijderen op de werkruimte en op de compute resource in die werkruimte. U de rol beschikbaar maken op een specifiek werkplekniveau, een specifiek resourcegroepniveau of een specifiek abonnementsniveau. Zie [Gebruikers en rollen beheren in een Azure Machine Learning-werkruimte](how-to-assign-roles.md)voor meer informatie.

> [!WARNING]
> Azure Machine Learning wordt momenteel niet ondersteund met azure Active Directory business-to-business-samenwerking.

### <a name="securing-compute-targets-and-data"></a>Compute targets en data beveiligen

Eigenaren en bijdragers kunnen alle rekendoelen en gegevensopslag gebruiken die aan de werkruimte zijn gekoppeld.  

Elke werkruimte heeft ook een gekoppelde door het systeem toegewezen beheerde identiteit met dezelfde naam als de werkruimte. De beheerde identiteit heeft de volgende machtigingen voor gekoppelde resources die in de werkruimte worden gebruikt.

Zie [Beheerde identiteiten voor Azure-bronnen voor](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)meer informatie over beheerde identiteiten.

| Resource | Machtigingen |
| ----- | ----- |
| Werkruimte | Inzender |
| Storage-account | Opslagblob-gegevensbijdrager |
| Key Vault | Toegang tot alle sleutels, geheimen, certificaten |
| Azure Container Registry | Inzender |
| Resourcegroep die de werkruimte bevat | Inzender |
| Resourcegroep die de sleutelkluis bevat (als deze verschilt van de groep die de werkruimte bevat) | Inzender |

We raden beheerders niet aan de toegang van de beheerde identiteit tot de bronnen in de vorige tabel in te trekken. U de toegang herstellen met de bewerking resynckeys.

Azure Machine Learning maakt een extra `aml-` toepassing `Microsoft-AzureML-Support-App-`(de naam begint met of ) met toegang op inzenderniveau in uw abonnement voor elke werkruimteregio. Als u bijvoorbeeld één werkruimte in Oost-VS hebt en één in Noord-Europa in hetzelfde abonnement, ziet u twee van deze toepassingen. Met deze toepassingen u azure machine learning inschakelen om u te helpen bij het beheren van rekenbronnen.

## <a name="network-security"></a>Netwerkbeveiliging

Azure Machine Learning is afhankelijk van andere Azure-services voor rekenbronnen. Compute resources (compute targets) worden gebruikt om modellen te trainen en te implementeren. U deze compute targets maken in een virtueel netwerk. U bijvoorbeeld Azure Data Science Virtual Machine gebruiken om een model te trainen en het model vervolgens te implementeren in AKS.  

Zie [Experimenten en gevolgtrekkingen uitvoeren in een virtueel netwerk](how-to-enable-virtual-network.md)voor meer informatie.

U Azure Private Link ook inschakelen voor uw werkruimte. Met Private Link u de communicatie beperken tot uw werkruimte vanuit een Azure Virtual Network. Zie [Privékoppeling configureren](how-to-configure-private-link.md)voor meer informatie.

> [!TIP]
> U virtueel netwerk en Privékoppeling combineren om de communicatie tussen uw werkruimte en andere Azure-bronnen te beschermen. Voor sommige combinaties is echter een werkruimte voor de Enterprise-editie vereist. Gebruik de volgende tabel om te begrijpen welke scenario's enterprise-editie vereisen:
>
> | Scenario | Enterprise</br>Edition | Basic</br>Edition |
> | ----- |:-----:|:-----:| 
> | Geen virtueel netwerk of private link | ✔ | ✔ |
> | Werkruimte zonder privékoppeling. Andere resources (behalve Azure Container Registry) in een virtueel netwerk | ✔ | ✔ |
> | Werkruimte zonder privékoppeling. Andere bronnen met Private Link | ✔ | |
> | Werkruimte met privékoppeling. Andere resources (behalve Azure Container Registry) in een virtueel netwerk | ✔ | ✔ |
> | Werkruimte en andere bronnen met Private Link | ✔ | |
> | Werkruimte met privékoppeling. Andere bronnen zonder Private Link of virtueel netwerk | ✔ | ✔ |
> | Azure Container Registry in een virtueel netwerk | ✔ | |
> | Door de klant beheerde sleutels voor werkruimte | ✔ | |
> 

> [!WARNING]
> De voorbeeldpreview van Azure Machine Learning compute instances wordt niet ondersteund in een werkruimte waarin Private Link is ingeschakeld.
> 
> Azure Machine Learning biedt geen ondersteuning voor het gebruik van een Azure Kubernetes-service waarmee een privékoppeling is ingeschakeld. In plaats daarvan u Azure Kubernetes Service gebruiken in een virtueel netwerk. Zie [Secure Azure ML-experimenten en inference-taken binnen een Azure Virtual Network](how-to-enable-virtual-network.md)voor meer informatie.

## <a name="data-encryption"></a>Gegevensversleuteling

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

> [!IMPORTANT]
> Als uw werkruimte gevoelige gegevens bevat, raden we u aan de [hbi_workspace vlag](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) in te stellen tijdens het maken van uw werkruimte. 

De `hbi_workspace` vlag bepaalt de hoeveelheid gegevens die Microsoft verzamelt voor diagnostische doeleinden en maakt extra versleuteling mogelijk in door Microsoft beheerde omgevingen. Daarnaast maakt het het volgende mogelijk:

* Hiermee wordt begonnen met het versleutelen van de lokale krasschijf in uw Amlcompute-cluster, op voorwaarde dat u geen eerdere clusters in dat abonnement hebt gemaakt. Anders moet u een ondersteuningsticket verhogen om versleuteling van de krasschijf van uw compute-clusters mogelijk te maken 
* Ruimt uw lokale krasschijf tussen de uitvoeringen op
* Doorgeeft u referenties voor uw opslagaccount, containerregister en SSH-account veilig van de uitvoeringslaag naar uw compute clusters met behulp van uw sleutelkluis
* Maakt IP-filtering mogelijk om ervoor te zorgen dat de onderliggende batchgroepen niet kunnen worden aangeroepen door andere externe services dan AzureMachineLearningService


Zie [Azure-gegevensversleuteling in rust](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)voor meer informatie over hoe versleuteling in rust werkt.

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning slaat momentopnamen, uitvoer en logboeken op in het Azure Blob-opslagaccount dat is gekoppeld aan de Azure Machine Learning-werkruimte en uw abonnement. Alle gegevens die zijn opgeslagen in Azure Blob-opslag worden in rust versleuteld met door Microsoft beheerde sleutels.

Zie [Azure Storage-versleuteling met door de klant beheerde sleutels in Azure Key Vault](../storage/common/storage-encryption-keys-portal.md)voor informatie over het gebruik van uw eigen sleutels voor gegevens die zijn opgeslagen in Azure Blob-opslag.

Trainingsgegevens worden doorgaans ook opgeslagen in Azure Blob-opslag, zodat deze toegankelijk zijn voor trainingscomputetargets. Deze opslag wordt niet beheerd door Azure Machine Learning, maar is gemonteerd om doelen te berekenen als een extern bestandssysteem.

Als u uw sleutel moet __draaien of intrekken,__ u dit op elk gewenst moment doen. Bij het roteren van een sleutel gebruikt het opslagaccount de nieuwe sleutel (nieuwste versie) om gegevens in rust te versleutelen. Bij het intrekken (uitschakelen) van een sleutel zorgt de opslagaccount voor falende aanvragen. Het duurt meestal een uur voor de rotatie of intrekking effectief te zijn.

Zie [Toegangssleutels voor opslagopnieuw genereren](how-to-change-storage-access-key.md)voor informatie over het regenereren van de toegangssleutels.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning slaat metrische gegevens en metagegevens op in een Azure Cosmos DB-exemplaar. Deze instantie is gekoppeld aan een Microsoft-abonnement dat wordt beheerd door Azure Machine Learning. Alle gegevens die zijn opgeslagen in Azure Cosmos DB worden in rust versleuteld met door Microsoft beheerde sleutels.

Als u uw eigen (door de klant beheerde) sleutels wilt gebruiken om het Azure Cosmos DB-exemplaar te versleutelen, u een speciaal Cosmos DB-exemplaar maken voor gebruik met uw werkruimte. We raden deze aanpak aan als u uw gegevens, zoals geschiedenisgegevens uitvoeren, wilt opslaan buiten het multi-tenant Cosmos DB-exemplaar dat wordt gehost in ons Microsoft-abonnement. 

Voer de volgende acties uit om een Cosmos DB-exemplaar in uw abonnement in te schakelen met door de klant beheerde sleutels:

* Schakel door de klant beheerde sleutelmogelijkheden in voor Cosmos DB. Op dit moment moet u toegang vragen om deze mogelijkheid te gebruiken. Neem hiervoor contact [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)op met .

* Registreer de Azure Machine Learning- en Azure Cosmos DB-bronproviders in uw abonnement, als dit nog niet is gedaan.

* Autoriseer de Machine Learning-app (in Identiteits- en Toegangsbeheer) met machtigingen voor bijdragen aan uw abonnement.

    ![De 'Azure Machine Learning App' autoriseren in Identiteits- en Toegangsbeheer in de portal](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Gebruik de volgende parameters bij het maken van de Azure Machine Learning-werkruimte. Beide parameters zijn verplicht en worden ondersteund in SJABLONEN VOOR SDK, CLI, REST API's en Resource Manager.

    * `resource_cmk_uri`: Deze parameter is de volledige resource URI van de door de klant beheerde sleutel in uw sleutelkluis, inclusief de [versie-informatie voor de sleutel.](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning) 

    * `cmk_keyvault`: Deze parameter is de bron-ID van de sleutelkluis in uw abonnement. Deze sleutelkluis moet zich in dezelfde regio bevinden en een abonnement dat u gebruikt voor de Azure Machine Learning-werkruimte. 
    
        > [!NOTE]
        > Deze sleutelkluisinstantie kan anders zijn dan de sleutelkluis die is gemaakt door Azure Machine Learning wanneer u de werkruimte indient. Als u dezelfde sleutelkluisinstantie voor de werkruimte wilt gebruiken, geeft u dezelfde sleutelkluis door terwijl u de werkruimte inshaagt met behulp van de [parameter key_vault.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) 

Deze instantie Cosmos DB wordt gemaakt in een door Microsoft beheerde brongroep in uw abonnement. De beheerde resourcegroep wordt benoemd in de indeling<AML Workspace Resource Group Name><GUID>

> [!IMPORTANT]
> * Als u deze instantie Cosmos DB moet verwijderen, moet u de Azure Machine Learning-werkruimte verwijderen die deze gebruikt. 
> * De [__standaardaanvraageenheden__](../cosmos-db/request-units.md) voor dit Cosmos DB-account zijn ingesteld op __8000__. Het wijzigen van deze waarde wordt niet ondersteund. 

Als u uw sleutel moet __draaien of intrekken,__ u dit op elk gewenst moment doen. Bij het roteren van een sleutel zal Cosmos DB de nieuwe sleutel (nieuwste versie) gaan gebruiken om gegevens in rust te versleutelen. Bij het intrekken (uitschakelen) van een sleutel, Cosmos DB zorgt voor falende verzoeken. Het duurt meestal een uur voor de rotatie of intrekking effectief te zijn.

Zie [Door klanten beheerde sleutels configureren voor uw Azure Cosmos DB-account voor](../cosmos-db/how-to-setup-cmk.md)meer informatie over door de klant beheerde sleutels.

#### <a name="azure-container-registry"></a>Azure Container Registry

Alle containerafbeeldingen in uw register (Azure Container Registry) worden in rust versleuteld. Azure versleutelt automatisch een afbeelding voordat deze wordt opgeslagen en decodeert deze wanneer Azure Machine Learning de afbeelding optrekt.

Als u uw eigen (door de klant beheerde) sleutels wilt gebruiken om uw Azure Container Registry te versleutelen, moet u uw eigen ACR maken en deze koppelen terwijl u de werkruimte indient of de standaardinstantie versleutelen die wordt gemaakt op het moment van het inrichten van de werkruimte.

Zie de volgende artikelen voor een voorbeeld van het maken van een werkruimte met een bestaand Azure Container Registry:

* [Maak een werkruimte voor Azure Machine Learning met Azure CLI](how-to-manage-workspace-cli.md).
* [Een Azure Resource Manager-sjabloon gebruiken om een werkruimte voor Azure Machine Learning te maken](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure Container Instance

U een geïmplementeerde ACI-bron (Azure Container Instance) versleutelen met behulp van door de klant beheerde sleutels. De door de klant beheerde sleutel die voor ACI wordt gebruikt, kan worden opgeslagen in de Azure Key Vault voor uw werkruimte. Zie [Gegevens versleutelen met een door de klant beheerde sleutel voor](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)informatie over het genereren van een sleutel.

Als u de sleutel wilt gebruiken bij het implementeren van `AciWebservice.deploy_configuration()`een model in Azure Container Instance, maakt u een nieuwe implementatieconfiguratie met behulp van . Geef de belangrijkste informatie op met behulp van de volgende parameters:

* `cmk_vault_base_url`: De URL van de sleutelkluis die de sleutel bevat.
* `cmk_key_name`: De naam van de sleutel.
* `cmk_key_version`: De versie van de sleutel.

Zie de volgende artikelen voor meer informatie over het maken en gebruiken van een implementatieconfiguratie:

* [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) referentie
* [Waar en hoe u implementeert](how-to-deploy-and-where.md)
* [Een model implementeren in Azure Container Instances](how-to-deploy-azure-container-instance.md)

Zie Gegevens versleutelen met een door de [klant beheerde sleutel voor](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)meer informatie over het gebruik van een door de klant beheerde sleutel met ACI.

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

U een geïmplementeerde Azure Kubernetes Service-bron op elk gewenst moment versleutelen met door de klant beheerde sleutels. Zie [Uw eigen sleutels met Azure Kubernetes Service](../aks/azure-disk-customer-managed-keys.md)voor meer informatie. 

Met dit proces u zowel de gegevens als de OS-schijf van de geïmplementeerde virtuele machines in het Kubernetes-cluster versleutelen.

> [!IMPORTANT]
> Dit proces werkt alleen met AKS K8s versie 1.17 of hoger. Azure Machine Learning heeft op 13 januari 2020 ondersteuning voor AKS 1.17 toegevoegd.

#### <a name="machine-learning-compute"></a>Machine Learning Compute

De OS-schijf voor elk compute-knooppunt dat is opgeslagen in Azure Storage, is versleuteld met door Microsoft beheerde sleutels in Azure Machine Learning-opslagaccounts. Dit rekendoel is vluchtig en clusters worden meestal verkleind wanneer er geen runs in de wachtrij staan. De onderliggende virtuele machine is gede-ingericht en de OS-schijf wordt verwijderd. Azure Disk Encryption wordt niet ondersteund voor de OS-schijf.

Elke virtuele machine heeft ook een lokale tijdelijke schijf voor OS-bewerkingen. Als u wilt, u de schijf gebruiken om trainingsgegevens te fasen. De schijf is standaard versleuteld voor `hbi_workspace` werkruimten `TRUE`met de parameter ingesteld op . Deze omgeving is alleen van korte duur voor de duur van uw run, en encryptie-ondersteuning is beperkt tot alleen door het systeem beheerde sleutels.

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks kunnen worden gebruikt in Azure Machine Learning-pijplijnen. Het Databricks File System (DBFS) dat door Azure Databricks wordt gebruikt, wordt standaard versleuteld met een door Microsoft beheerde sleutel. Zie Door [de klant beheerde sleutels configureren op standaardDBFS (root) DBFS](/azure/databricks/security/customer-managed-keys-dbfs)als u Azure Databricks wilt configureren om door de klant beheerde sleutels te gebruiken.

### <a name="encryption-in-transit"></a>Versleuteling 'in transit'

Azure Machine Learning gebruikt TLS om interne communicatie tussen verschillende Azure Machine Learning-microservices te beveiligen. Alle Azure Storage-toegang vindt ook plaats via een beveiligd kanaal.

Als u externe oproepen naar het scoreeindpunt Azure Machine Learning wilt beveiligen, wordt TLS gebruikt. Zie [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)voor meer informatie.

### <a name="using-azure-key-vault"></a>Azure Key Vault gebruiken

Azure Machine Learning gebruikt de Azure Key Vault-instantie die aan de werkruimte is gekoppeld om referenties van verschillende soorten op te slaan:

* De tekenreeks voor de gekoppelde opslagaccountverbinding
* Wachtwoorden voor Azure Container Repository-exemplaren
* Verbindingstekenreeksen met gegevensopslag

SSH-wachtwoorden en sleutels voor rekendoelen zoals Azure HDInsight en VM's worden opgeslagen in een afzonderlijke sleutelkluis die is gekoppeld aan het Microsoft-abonnement. Azure Machine Learning slaat geen wachtwoorden of sleutels op die door gebruikers worden geleverd. In plaats daarvan genereert, autoriseert en slaat het zijn eigen SSH-sleutels op om verbinding te maken met VM's en HDInsight om de experimenten uit te voeren.

Elke werkruimte heeft een gekoppelde door het systeem toegewezen beheerde identiteit met dezelfde naam als de werkruimte. Deze beheerde identiteit heeft toegang tot alle sleutels, geheimen en certificaten in de sleutelkluis.

## <a name="data-collection-and-handling"></a>Gegevensverzameling en -verwerking

### <a name="microsoft-collected-data"></a>Microsoft verzamelde gegevens

Microsoft kan niet-gebruikersidentificerende informatie verzamelen, zoals resourcenamen (bijvoorbeeld de naam van de gegevensset of de naam van het machine learning-experiment) of variabelen voor taakomgeving voor diagnostische doeleinden. Al deze gegevens worden opgeslagen met door Microsoft beheerde sleutels in opslag die wordt gehost in abonnementen die eigendom zijn van Microsoft en volgen [het standaardprivacybeleid en de normen voor gegevensverwerking van Microsoft.](https://privacy.microsoft.com/privacystatement)

Microsoft raadt ook aan om gevoelige informatie (zoals sleutelgeheimen van accountgegevens) niet op te slaan in omgevingsvariabelen. Omgevingsvariabelen worden door ons geregistreerd, versleuteld en opgeslagen. Ook bij het benoemen van [runid,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)vermijd het opnemen van gevoelige informatie, zoals gebruikersnamen of geheime projectnamen. Deze informatie kan worden weergegeven in telemetrielogboeken die toegankelijk zijn voor Microsoft Support-technici.

U zich afmelden voor diagnostische `hbi_workspace` gegevens `TRUE` die worden verzameld door de parameter in te stellen op het inrichten van de werkruimte. Deze functionaliteit wordt ondersteund bij het gebruik van de azureml python python sdk-, CLI-, REST-API's of Azure Resource Manager-sjablonen.

### <a name="microsoft-generated-data"></a>Door Microsoft gegenereerde gegevens

Bij het gebruik van services zoals Automated Machine Learning kan Microsoft tijdelijke, vooraf verwerkte gegevens genereren voor het trainen van meerdere modellen. Deze gegevens worden opgeslagen in een gegevensarchief in uw werkruimte, waarmee u toegangsbesturingselementen en versleuteling op de juiste manier afdwingen.

U ook [diagnostische gegevens die vanaf uw geïmplementeerde eindpunt](how-to-enable-app-insights.md) zijn vastgelegd, versleutelen in uw Azure Application Insights-exemplaar.

## <a name="monitoring"></a>Bewaking

### <a name="metrics"></a>Metrische gegevens

U Azure Monitor-statistieken gebruiken om statistieken voor uw Azure Machine Learning-werkruimte weer te geven en te controleren. Selecteer in de [Azure-portal](https://portal.azure.com)uw werkruimte en selecteer **Metrische gegevens:**

[![Schermafbeelding van voorbeeldstatistieken voor een werkruimte](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

De statistieken bevatten informatie over uitvoeringen, implementaties en registraties.

Zie [Statistieken in Azure Monitor](/azure/azure-monitor/platform/data-platform-metrics)voor meer informatie.

### <a name="activity-log"></a>Activiteitenlogboek

U het activiteitenlogboek van een werkruimte bekijken om verschillende bewerkingen te bekijken die op de werkruimte worden uitgevoerd. Het logboek bevat basisgegevens zoals de naam van de bewerking, de gebeurtenisinitiator en de tijdstempel.

In deze schermafbeelding wordt het activiteitenlogboek van een werkruimte weergegeven:

[![Schermafbeelding van het activiteitenlogboek van een werkruimte](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Gegevens over het scoren van aanvragen worden opgeslagen in Application Insights. Application Insights wordt gemaakt in uw abonnement wanneer u een werkruimte maakt. Geregistreerde informatie omvat velden zoals:

* HTTP-methode
* Useragent
* ComputeType (ComputeType)
* AanvragerUrl
* Statuscode
* AanvraagId
* Duur

> [!IMPORTANT]
> Sommige acties in de Azure Machine Learning-werkruimte registreren geen gegevens in het activiteitenlogboek. Bijvoorbeeld, het begin van een training run en de registratie van een model worden niet geregistreerd.
>
> Sommige van deze acties worden weergegeven in het gebied **Activiteiten** van uw werkruimte, maar deze meldingen geven niet aan wie de activiteit heeft gestart.

## <a name="data-flow-diagrams"></a>Gegevensstroomdiagrammen

### <a name="create-workspace"></a>Werkruimte maken

In het volgende diagram wordt de werkstroom voor werkruimte maken weergegeven.

* U meldt zich aan bij Azure AD van een van de ondersteunde Azure Machine Learning-clients (Azure CLI, Python SDK, Azure-portal) en vraagt het juiste Azure Resource Manager-token aan.
* U belt Azure Resource Manager om de werkruimte te maken. 
* Azure Resource Manager neemt contact op met de Azure Machine Learning-resourceprovider om de werkruimte in te richten.

Tijdens het maken van de werkruimte worden aanvullende bronnen gemaakt in het abonnement van de gebruiker:

* Key Vault (om geheimen op te slaan)
* Een Azure-opslagaccount (inclusief blob en bestandsshare)
* Azure Container Registry (om Docker-afbeeldingen op te slaan voor gevolgtrekking/scoren en experimenteren)
* Toepassingsinzichten (om telemetrie op te slaan)

De gebruiker kan ook andere rekendoelen inrichten die zijn gekoppeld aan een werkruimte (zoals Azure Kubernetes Service of VM's) indien nodig.

[![Werkstroom voor werkruimtemaken](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Broncode opslaan (trainingsscripts)

In het volgende diagram ziet u de werkstroom voor codemomentopnamen.

Gekoppeld aan een Azure Machine Learning-werkruimte zijn mappen (experimenten) die de broncode (trainingsscripts) bevatten. Deze scripts worden opgeslagen op uw lokale machine en in de cloud (in de Azure Blob-opslag voor uw abonnement). De codemomentopnamen worden gebruikt voor uitvoering of inspectie voor historische controle.

[![Codemomentopnamewerkstroom](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Training

In het volgende diagram ziet u de trainingsworkflow.

* Azure Machine Learning wordt aangeroepen met de momentopname-id voor de codemomentopname die in de vorige sectie is opgeslagen.
* Azure Machine Learning maakt een run ID (optioneel) en een Machine Learning-servicetoken, die later wordt gebruikt door rekendoelen zoals Machine Learning Compute/VM's om te communiceren met de Machine Learning-service.
* U een beheerd rekendoel (zoals Machine Learning Compute) of een onbeheerd rekendoel (zoals VM's) kiezen om trainingstaken uit te voeren. Hier volgen de gegevensstromen voor beide scenario's:
   * VM's/HDInsight, toegankelijk via SSH-referenties in een sleutelkluis in het Microsoft-abonnement. Azure Machine Learning voert beheercode uit op het rekendoel dat:

   1. Bereidt het milieu voor. (Docker is een optie voor VM's en lokale computers. Bekijk de volgende stappen voor Machine Learning Compute om te begrijpen hoe experimenten uitvoeren op Docker-containers werkt.)
   1. Downloadt de code.
   1. Hiermee stelt u omgevingsvariabelen en -configuraties in.
   1. Voert gebruikersscripts uit (de codemomentopname die in de vorige sectie wordt genoemd).

   * Machine Learning Compute, toegankelijk via een door de werkruimte beheerde identiteit.
Omdat Machine Learning Compute een beheerd rekendoel is (dat wil zeggen dat het wordt beheerd door Microsoft) wordt het uitgevoerd onder uw Microsoft-abonnement.

   1. Remote Docker bouw wordt afgetrapt, indien nodig.
   1. Beheercode is geschreven naar het Azure Files-aandeel van de gebruiker.
   1. De container wordt gestart met een eerste opdracht. Dat wil zeggen, management code zoals beschreven in de vorige stap.

#### <a name="querying-runs-and-metrics"></a>Query's en statistieken

In het onderstaande stroomdiagram treedt deze stap op wanneer het trainingsgegevensdoel de runmetrics terugschrijft naar Azure Machine Learning vanuit opslag in de Cosmos DB-database. Clients kunnen Azure Machine Learning bellen. Machine Learning haalt op zijn beurt statistieken uit de Cosmos DB-database en stuurt ze terug naar de client.

[![Trainingswerkstroom](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Webservices maken

In het volgende diagram ziet u de gevolgtrekkingswerk. Gevolgtrekking, of modelscore, is de fase waarin het geïmplementeerde model wordt gebruikt voor voorspelling, meestal op productiegegevens.

Hier zijn de details:

* De gebruiker registreert een model met behulp van een client zoals de Azure Machine Learning SDK.
* De gebruiker maakt een afbeelding met behulp van een model, een scorebestand en andere modelafhankelijkheden.
* De Docker-afbeelding wordt gemaakt en opgeslagen in Azure Container Registry.
* De webservice wordt geïmplementeerd in het rekendoel (Container Instances/AKS) met behulp van de afbeelding die in de vorige stap is gemaakt.
* Gegevens over het scoren van aanvragen worden opgeslagen in Application Insights, dat in het abonnement van de gebruiker staat.
* Telemetrie wordt ook naar het Microsoft/Azure-abonnement gepusht.

[![Inference-werkstroom](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* [Azure Machine Learning-webservices beveiligen met TLS](how-to-secure-web-service.md)
* [Een Machine Learning-model gebruiken dat is geïmplementeerd als webservice](how-to-consume-web-service.md)
* [Batchvoorspellingen uitvoeren](how-to-use-parallel-run-step.md)
* [Uw Azure Machine Learning-modellen controleren met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Azure Machine Learning-SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure Machine Learning gebruiken met Azure Virtual Network](how-to-enable-virtual-network.md)
* [Aanbevolen procedures voor het bouwen van aanbevelingssystemen](https://github.com/Microsoft/Recommenders)
* [Een realtime aanbevelings-API bouwen op Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
