---
title: Beveiliging voor bedrijven
titleSuffix: Azure Machine Learning
description: 'Gebruik veilig Azure Machine Learning: verificatie, autorisatie, netwerk beveiliging, gegevens versleuteling en bewaking.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/19/2020
ms.openlocfilehash: 5afa6b9127317fcd1a683651be86cdfe078cfcd6
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259432"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Enter prise Security voor Azure Machine Learning

In dit artikel vindt u informatie over de beschik bare beveiligings functies voor Azure Machine Learning.

Wanneer u een Cloud service gebruikt, is een best practice het beperken van de toegang tot alleen de gebruikers die er behoefte aan hebben. Begin met het verificatie-en autorisatie model dat door de service wordt gebruikt. U kunt ook de netwerk toegang beperken of bronnen veilig samen voegen in uw on-premises netwerk met de Cloud. Gegevens versleuteling is ook cruciaal, zowel op rest als tijdens het verplaatsen van gegevens tussen services. Ten slotte moet u de service kunnen bewaken en een audit logboek maken van alle activiteiten.

> [!NOTE]
> De informatie in dit artikel werkt met de Azure Machine Learning python SDK-versie 1.0.83.1 of hoger.

## <a name="authentication"></a>Verificatie

Multi-factor Authentication wordt ondersteund als Azure Active Directory (Azure AD) is geconfigureerd om het te gebruiken. Dit is het verificatie proces:

1. De client meldt zich aan bij Azure AD en ontvangt een Azure Resource Manager token.  Gebruikers en service-principals worden volledig ondersteund.
1. De client geeft het token aan Azure Resource Manager en alle Azure Machine Learning.
1. De Machine Learning-service levert een Machine Learning-service token aan het gebruikers Compute-doel (bijvoorbeeld Machine Learning Compute). Dit token wordt gebruikt door het gebruikers Compute-doel om terug te bellen naar de Machine Learning-service nadat de uitvoering is voltooid. Het bereik is beperkt tot de werk ruimte.

[![Verificatie in Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Zie [verificatie instellen voor Azure machine learning resources en werk stromen](how-to-setup-authentication.md)voor meer informatie. Dit artikel bevat informatie en voor beelden over verificatie, waaronder het gebruik van service-principals en geautomatiseerde werk stromen.

### <a name="authentication-for-web-service-deployment"></a>Verificatie voor de implementatie van de webservice

Azure Machine Learning ondersteunt twee verificatie vormen voor webservices: sleutel en token. Elke webservice kan per keer slechts één vorm van authenticatie inschakelen.

|Verificatiemethode|Beschrijving|Azure Container Instances|AKS|
|---|---|---|---|
|Sleutel|Sleutels zijn statisch en hoeven niet te worden vernieuwd. Sleutels kunnen hand matig opnieuw worden gegenereerd.|Standaard uitgeschakeld| Standaard ingeschakeld|
|Token|Tokens verlopen na een opgegeven tijds periode en moeten worden vernieuwd.| Niet beschikbaar| Standaard uitgeschakeld |

Zie de [sectie verificatie](how-to-setup-authentication.md#web-service-authentication)van de webservice voor code voorbeelden.

## <a name="authorization"></a>Autorisatie

U kunt meerdere werk ruimten maken en elke werk ruimte kan worden gedeeld door meerdere personen. Wanneer u een werk ruimte deelt, kunt u de toegang hiertoe beheren door deze rollen toe te wijzen aan gebruikers:

* Eigenaar
* Inzender
* Lezer

De volgende tabel bevat enkele van de belangrijkste Azure Machine Learning bewerkingen en de rollen die ze kunnen uitvoeren:

| Azure Machine Learning bewerking | Eigenaar | Inzender | Lezer |
| ---- |:----:|:----:|:----:|
| Werkruimte maken | ✓ | ✓ | |
| Werk ruimte delen | ✓ | |  |
| Werk ruimte bijwerken naar Enter prise Edition | ✓ | |
| Reken doel maken | ✓ | ✓ | |
| Reken doel koppelen | ✓ | ✓ | |
| Gegevens archieven koppelen | ✓ | ✓ | |
| Experiment uitvoeren | ✓ | ✓ | |
| Uitvoeringen/metrische gegevens weer geven | ✓ | ✓ | ✓ |
| Model registreren | ✓ | ✓ | |
| Installatie kopie maken | ✓ | ✓ | |
| Webservice implementeren | ✓ | ✓ | |
| Modellen/afbeeldingen weer geven | ✓ | ✓ | ✓ |
| Webservice aanroepen | ✓ | ✓ | ✓ |

Als de ingebouwde rollen niet aan uw behoeften voldoen, kunt u aangepaste rollen maken. Aangepaste rollen worden alleen ondersteund voor bewerkingen in de werk ruimte en Machine Learning Compute. Aangepaste rollen kunnen Lees-, schrijf-of verwijder machtigingen hebben voor de werk ruimte en op de reken resource in die werk ruimte. U kunt de rol beschikbaar maken op een specifiek werk ruimte niveau, op een specifiek niveau van de resource groep of op een specifiek abonnements niveau. Zie [gebruikers en rollen beheren in een Azure machine learning-werk ruimte](how-to-assign-roles.md)voor meer informatie.

> [!WARNING]
> Azure Machine Learning wordt ondersteund met Azure Active Directory Business-to-Business-samen werking, maar wordt momenteel niet ondersteund met Azure Active Directory Business-to-Consuming-samen werking.

### <a name="securing-compute-targets-and-data"></a>Reken doelen en gegevens beveiligen

Eigen aren en inzenders kunnen alle reken doelen en gegevens archieven gebruiken die aan de werk ruimte zijn gekoppeld.  

Aan elke werk ruimte is ook een aan het systeem toegewezen beheerde identiteit gekoppeld die dezelfde naam heeft als de werk ruimte. De beheerde identiteit heeft de volgende machtigingen op gekoppelde resources die worden gebruikt in de werk ruimte.

Zie [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)voor meer informatie over beheerde identiteiten.

| Resource | Machtigingen |
| ----- | ----- |
| Werkruimte | Inzender |
| Opslagaccount | Inzender voor Storage BLOB-gegevens |
| Key Vault | Toegang tot alle sleutels, geheimen, certificaten |
| Azure Container Registry | Inzender |
| Resource groep die de werk ruimte bevat | Inzender |
| De resource groep die de sleutel kluis bevat (als deze anders is dan de naam die de werk ruimte bevat) | Inzender |

Het is niet raadzaam om beheerders de toegang tot de beheerde identiteit in te trekken voor de resources die in de voor gaande tabel worden vermeld. U kunt de toegang herstellen met behulp van de bewerking voor het opnieuw synchroniseren van sleutels.

Azure Machine Learning maakt een extra toepassing (de naam begint met `aml-` of `Microsoft-AzureML-Support-App-` ) met toegang op Inzender niveau in uw abonnement voor elke werkruimte regio. Als u bijvoorbeeld één werk ruimte hebt in VS-Oost en één in Europa-noord in hetzelfde abonnement, ziet u twee van deze toepassingen. Met deze toepassingen kunt u Azure Machine Learning helpen bij het beheren van reken resources.

## <a name="network-security"></a>Netwerkbeveiliging

Azure Machine Learning is afhankelijk van andere Azure-Services voor reken resources. Reken bronnen (Compute-doelen) worden gebruikt om modellen te trainen en te implementeren. U kunt deze reken doelen maken in een virtueel netwerk. U kunt bijvoorbeeld Azure Data Science Virtual Machine gebruiken om een model te trainen en het model vervolgens implementeren in AKS.  

Zie voor meer informatie [veilig experimenten en demijnen uitvoeren in een geïsoleerd virtueel netwerk](how-to-enable-virtual-network.md).

U kunt ook een persoonlijke Azure-koppeling inschakelen voor uw werk ruimte. Met persoonlijke koppeling kunt u de communicatie met uw werk ruimte beperken vanuit een Azure-Virtual Network. Zie een [persoonlijke koppeling configureren](how-to-configure-private-link.md)voor meer informatie.

## <a name="data-encryption"></a>Gegevensversleuteling

> [!IMPORTANT]
> Voor productie kwaliteit versleuteling tijdens de __training__raadt micro soft aan om Azure machine learning Compute-cluster te gebruiken. Voor de versleuteling van de productie __kwaliteit tijdens de__degradatie raadt micro soft de Azure Kubernetes-service te gebruiken.
>
> Azure Machine Learning Compute-instantie is een ontwikkel-en test omgeving. Wanneer u deze gebruikt, raden we u aan om uw bestanden, zoals notebooks en scripts, op te slaan in een bestands share. Uw gegevens moeten worden opgeslagen in een gegevens opslag.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

> [!IMPORTANT]
> Als uw werk ruimte gevoelige gegevens bevat, kunt u het beste de [hbi_workspace vlag](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) instellen tijdens het maken van uw werk ruimte. 

De `hbi_workspace` markering bepaalt de hoeveelheid gegevens die micro soft verzamelt voor diagnostische doel einden en maakt extra versleuteling mogelijk in door micro soft beheerde omgevingen. Daarnaast kunnen de volgende acties worden uitgevoerd:

* Hiermee wordt het versleutelen van de lokale werk schijf in uw Azure Machine Learning Compute-Cluster gestart, mits u geen eerdere clusters hebt gemaakt in dat abonnement. Anders moet u een ondersteunings ticket genereren om versleuteling van de Scratch schijf van uw reken clusters mogelijk te maken 
* De lokale scratchschijf opschonen tussen uitvoeringen
* Referenties voor uw opslag account, container register en SSH-account veilig door gegeven van de uitvoerings slaag naar uw reken clusters met behulp van uw sleutel kluis
* IP-filtering inschakelen om ervoor te zorgen dat de onderliggende batch-groepen niet kunnen worden aangeroepen door andere externe services dan AzureMachineLearningService


Zie [Azure Data Encryption at rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)(Engelstalig) voor meer informatie over de werking van versleuteling bij rest in Azure.

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning slaat moment opnamen, uitvoer en logboeken op in het Azure Blob Storage-account dat is gekoppeld aan de Azure Machine Learning-werk ruimte en uw abonnement. Alle gegevens die zijn opgeslagen in Azure Blob Storage, worden op rest versleuteld met door micro soft beheerde sleutels.

Zie [Azure Storage versleuteling met door de klant beheerde sleutels in azure Key Vault](../storage/common/storage-encryption-keys-portal.md)voor meer informatie over het gebruik van uw eigen sleutels voor gegevens die zijn opgeslagen in Azure Blob-opslag.

Trainings gegevens worden doorgaans ook opgeslagen in Azure Blob-opslag, zodat deze toegankelijk is voor Compute-doel stellingen. Deze opslag wordt niet beheerd door Azure Machine Learning, maar gekoppeld aan Compute-doelen als een extern bestands systeem.

Als u de sleutel wilt __draaien of intrekken__ , kunt u dit op elk gewenst moment doen. Bij het draaien van een sleutel begint het opslag account met de nieuwe sleutel (meest recente versie) voor het versleutelen van gegevens in rust. Bij het intrekken (uitschakelen) van een sleutel zorgt het opslag account ervoor dat er mislukte aanvragen worden uitgevoerd. Het duurt doorgaans een uur voordat de draaiing of intrekken effectief is.

Zie [toegangs sleutels voor opslag opnieuw genereren](how-to-change-storage-access-key.md)voor meer informatie over het opnieuw genereren van de toegangs sleutels.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning metrische gegevens en meta data worden opgeslagen in een Azure Cosmos DB-exemplaar. Dit exemplaar is gekoppeld aan een micro soft-abonnement dat wordt beheerd door Azure Machine Learning. Alle gegevens die zijn opgeslagen in Azure Cosmos DB, worden op rest versleuteld met door micro soft beheerde sleutels.

Als u uw eigen (door de klant beheerde) sleutels wilt gebruiken om de Azure Cosmos DB-exemplaar te versleutelen, kunt u een speciaal Cosmos DB exemplaar maken voor gebruik met uw werk ruimte. We raden u aan deze aanpak te gebruiken als u uw gegevens wilt opslaan, zoals informatie over de uitvoerings geschiedenis, buiten het Cosmos DB exemplaar met meerdere tenants dat wordt gehost op het micro soft-abonnement. 

Voer de volgende acties uit om het inrichten van een Cosmos DB-exemplaar in uw abonnement met door de klant beheerde sleutels in te scha kelen:

* Registreer de resource providers micro soft. MachineLearning en Microsoft.DocumentDB in uw abonnement, als u dit nog niet hebt gedaan.

* Machtig de Machine Learning-app (in identiteits-en toegangs beheer) met Inzender machtigingen voor uw abonnement.

    ![De app ' Azure Machine Learning ' machtigen voor identiteits-en toegangs beheer in de portal](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Gebruik de volgende para meters bij het maken van de Azure Machine Learning-werk ruimte. Beide para meters zijn verplicht en worden ondersteund in SDK, CLI, REST Api's en Resource Manager-sjablonen.

    * `resource_cmk_uri`: Deze para meter is de volledige resource-URI van de door de klant beheerde sleutel in uw sleutel kluis, met inbegrip [van de versie gegevens voor de sleutel](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Deze para meter is de resource-ID van de sleutel kluis in uw abonnement. Deze sleutel kluis moet zich in dezelfde regio en hetzelfde abonnement bevinden als de-werk ruimte die u wilt gebruiken voor de Azure Machine Learning. 
    
        > [!NOTE]
        > Dit sleutel kluis exemplaar kan afwijken van de sleutel kluis die door Azure Machine Learning wordt gemaakt wanneer u de werk ruimte inricht. Als u hetzelfde sleutel kluis exemplaar voor de werk ruimte wilt gebruiken, moet u dezelfde sleutel kluis door geven tijdens het inrichten van de werk ruimte met behulp van de [para meter key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

Dit Cosmos DB exemplaar wordt gemaakt in een door micro soft beheerde resource groep in uw abonnement. De beheerde resource groep krijgt de naam in de indeling `<AML Workspace Resource Group Name><GUID>` .

> [!IMPORTANT]
> * Als u dit exemplaar van Cosmos DB wilt verwijderen, moet u de Azure Machine Learning-werk ruimte verwijderen waarin deze wordt gebruikt. 
> * De standaard [__aanvraag eenheden__](../cosmos-db/request-units.md) voor dit Cosmos DB account is ingesteld op __8000__. Het wijzigen van deze waarde wordt niet ondersteund. 

Als u de sleutel wilt __draaien of intrekken__ , kunt u dit op elk gewenst moment doen. Bij het draaien van een sleutel begint Cosmos DB met het gebruik van de nieuwe sleutel (meest recente versie) om gegevens in rust te versleutelen. Wanneer u een sleutel intrekt (uitschakelt), wordt Cosmos DB het uitvoeren van mislukte aanvragen uitgevoerd. Het duurt doorgaans een uur voordat de draaiing of intrekken effectief is.

Zie door [de klant beheerde sleutels voor uw Azure Cosmos DB account configureren](../cosmos-db/how-to-setup-cmk.md)voor meer informatie over door de klant beheerde sleutels met Cosmos db.

#### <a name="azure-container-registry"></a>Azure Container Registry

Alle container installatie kopieën in het REGI ster (Azure Container Registry) worden op rest versleuteld. Azure versleutelt automatisch een afbeelding voordat deze wordt opgeslagen en ontsleuteld wanneer Azure Machine Learning de installatie kopie ophaalt.

Als u uw eigen (door de klant beheerde) sleutels wilt gebruiken om uw Azure Container Registry te versleutelen, moet u uw eigen ACR maken en koppelen tijdens het inrichten van de werk ruimte, of het standaard exemplaar dat wordt gemaakt, versleutelen op het moment van de inrichting van de werk ruimte.

> [!IMPORTANT]
> Voor Azure Machine Learning moet het beheerders account zijn ingeschakeld op uw Azure Container Registry. Deze instelling is standaard uitgeschakeld wanneer u een container register maakt. Zie [beheerders account](/azure/container-registry/container-registry-authentication#admin-account)voor meer informatie over het inschakelen van het beheerders account.
>
> Als er een Azure Container Registry is gemaakt voor een werk ruimte, moet u dit niet verwijderen. Als u dit doet, wordt uw Azure Machine Learning-werk ruimte verbroken.

Raadpleeg de volgende artikelen voor een voor beeld van het maken van een werk ruimte met behulp van een bestaande Azure Container Registry:

* [Een werk ruimte maken voor Azure machine learning met Azure cli](how-to-manage-workspace-cli.md).
* [Een Azure Resource Manager sjabloon gebruiken om een werk ruimte te maken voor Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure Container Instance

U kunt een geïmplementeerde Azure container instance-resource (ACI) versleutelen met door de klant beheerde sleutels. De door de klant beheerde sleutel die wordt gebruikt voor ACI kan worden opgeslagen in de Azure Key Vault voor uw werk ruimte. Zie [gegevens versleutelen met een door de klant beheerde sleutel](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)voor meer informatie over het genereren van een sleutel.

Als u de sleutel wilt gebruiken bij het implementeren van een model naar Azure container instance, maakt u een nieuwe implementatie configuratie met `AciWebservice.deploy_configuration()` . Geef de belangrijkste informatie op met behulp van de volgende para meters:

* `cmk_vault_base_url`: De URL van de sleutel kluis die de sleutel bevat.
* `cmk_key_name`: De naam van de sleutel.
* `cmk_key_version`: De versie van de sleutel.

Zie de volgende artikelen voor meer informatie over het maken en gebruiken van een implementatie configuratie:

* Naslag informatie over [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Waar en hoe u implementeert](how-to-deploy-and-where.md)
* [Een model implementeren naar Azure Container Instances](how-to-deploy-azure-container-instance.md)

Zie [gegevens versleutelen met een door de klant beheerde sleutel](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)voor meer informatie over het gebruik van een door de klant beheerde sleutel met ACI.

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

U kunt een geïmplementeerde Azure Kubernetes service-resource op elk gewenst moment versleutelen met door de klant beheerde sleutels. Zie [uw eigen sleutels maken met de Azure Kubernetes-service](../aks/azure-disk-customer-managed-keys.md)voor meer informatie. 

Met dit proces kunt u zowel de gegevens als de besturingssysteem schijf van de geïmplementeerde virtuele machines in het Kubernetes-cluster versleutelen.

> [!IMPORTANT]
> Dit proces werkt alleen met AKS K8s versie 1,17 of hoger. Azure Machine Learning is ondersteuning toegevoegd voor AKS 1,17 op 13 januari 2020.

#### <a name="machine-learning-compute"></a>Machine Learning Compute

De besturingssysteem schijf voor elk reken knooppunt dat in Azure Storage is opgeslagen, is versleuteld met door micro soft beheerde sleutels in Azure Machine Learning-opslag accounts. Dit Compute-doel is kortstondig en clusters worden meestal omlaag geschaald wanneer er geen uitvoeringen in de wachtrij worden geplaatst. De inrichting van de onderliggende virtuele machine is ongedaan gemaakt en de besturingssysteem schijf wordt verwijderd. Azure Disk Encryption wordt niet ondersteund voor de besturingssysteem schijf.

Elke virtuele machine heeft ook een lokale tijdelijke schijf voor besturingssysteem bewerkingen. Als u wilt, kunt u de trainings gegevens voor de schijf gebruiken. De schijf is standaard versleuteld voor werk ruimten waarvoor de `hbi_workspace` para meter is ingesteld op `TRUE` . Deze omgeving is slechts korte tijd voor de duur van de uitvoering en de ondersteuning voor versleuteling is beperkt tot alleen door het systeem beheerde sleutels.

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks kunnen worden gebruikt in Azure Machine Learning pijp lijnen. Het Databricks File System (DBFS) dat door Azure Databricks wordt gebruikt, is standaard versleuteld met een door micro soft beheerde sleutel. Zie door de klant beheerde sleutels [configureren op standaard (root) DBFS](/azure/databricks/security/customer-managed-keys-dbfs)om Azure Databricks te configureren voor het gebruik van door de klant beheerde sleutels.

### <a name="encryption-in-transit"></a>Versleuteling 'in transit'

Azure Machine Learning gebruikt TLS om interne communicatie tussen verschillende Azure Machine Learning micro services te beveiligen. Alle Azure Storage toegang vindt ook plaats via een beveiligd kanaal.

Azure Machine Learning maakt gebruik van TLS om externe aanroepen naar het Score-eind punt te beveiligen. Zie [TLS gebruiken om een webservice te beveiligen via Azure machine learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)voor meer informatie.

### <a name="using-azure-key-vault"></a>Azure Key Vault gebruiken

Azure Machine Learning gebruikt het Azure Key Vault exemplaar dat is gekoppeld aan de werk ruimte om referenties van verschillende soorten op te slaan:

* Het gekoppelde opslag account connection string
* Wacht woorden voor Azure container repository-instanties
* Verbindings reeksen naar gegevens archieven

SSH-wacht woorden en sleutels voor het berekenen van doelen zoals Azure HDInsight en Vm's worden opgeslagen in een afzonderlijke sleutel kluis die is gekoppeld aan het micro soft-abonnement. Azure Machine Learning slaat geen wacht woorden of sleutels op die door gebruikers worden verschaft. In plaats daarvan worden er eigen SSH-sleutels gegenereerd, geautoriseerd en opgeslagen om verbinding te maken met Vm's en HDInsight om de experimenten uit te voeren.

Aan elke werk ruimte is een door het systeem toegewezen beheerde identiteit gekoppeld die dezelfde naam heeft als de werk ruimte. Deze beheerde identiteit heeft toegang tot alle sleutels, geheimen en certificaten in de sleutel kluis.

## <a name="data-collection-and-handling"></a>Gegevens verzameling en-verwerking

### <a name="microsoft-collected-data"></a>Verzamelde gegevens van micro soft

Micro soft kan gegevens over niet-gebruikers identificeren, zoals resource namen (bijvoorbeeld de naam van de gegevensset of de naam van het machine learning experiment) of taak omgevings variabelen voor diagnostische doel einden. Al deze gegevens worden opgeslagen met behulp van door micro soft beheerde sleutels in de opslag die wordt gehost in micro soft-abonnementen en [de standaard standaarden voor privacybeleid en gegevens verwerking van micro soft](https://privacy.microsoft.com/privacystatement)worden gevolgd.

Micro soft adviseert ook niet om gevoelige informatie (zoals account sleutel geheimen) op te slaan in omgevings variabelen. Omgevings variabelen worden geregistreerd, versleuteld en opgeslagen door ons. Zorg er ook voor dat u een naam [run_id](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)geeft, Vermijd het gebruik van gevoelige gegevens zoals gebruikers namen of geheime project namen. Deze informatie kan worden weer gegeven in telemetrie-logboeken die toegankelijk zijn voor Microsoft Ondersteuning engineers.

U kunt zich afmelden van diagnostische gegevens die worden verzameld door de `hbi_workspace` para meter in te stellen op `TRUE` tijdens het inrichten van de werk ruimte. Deze functionaliteit wordt ondersteund bij het gebruik van de AzureML python SDK, CLI, REST-Api's of Azure Resource Manager sjablonen.

### <a name="microsoft-generated-data"></a>Door micro soft gegenereerde gegevens

Wanneer u services zoals automatische Machine Learning gebruikt, kan micro soft een tijdelijke, vooraf verwerkte gegevens genereren voor het trainen van meerdere modellen. Deze gegevens worden opgeslagen in een gegevens opslag in uw werk ruimte, zodat u op de juiste wijze toegangs controle en versleuteling kunt afdwingen.

U kunt ook [Diagnostische gegevens die vanuit uw geïmplementeerde eind punt zijn geregistreerd](how-to-enable-app-insights.md) , versleutelen in uw Azure-toepassing Insights-exemplaar.

## <a name="monitoring"></a>Bewaking

### <a name="metrics"></a>Metrische gegevens

U kunt Azure Monitor metrische gegevens gebruiken om metrische gegevens voor uw Azure Machine Learning-werk ruimte weer te geven en te controleren. Selecteer in de [Azure Portal](https://portal.azure.com)uw werk ruimte en selecteer vervolgens **metrische gegevens**:

[![Scherm opname van voor beelden van metrische gegevens voor een werk ruimte](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

De metrische gegevens bevatten informatie over uitvoeringen, implementaties en registraties.

Zie [metrische gegevens in azure monitor](/azure/azure-monitor/platform/data-platform-metrics)voor meer informatie.

### <a name="activity-log"></a>Activiteitenlogboek

U kunt het activiteiten logboek van een werk ruimte bekijken om verschillende bewerkingen te bekijken die worden uitgevoerd op de werk ruimte. Het logboek bevat basis informatie, zoals de naam van de bewerking, de gebeurtenis initiator en de tijds tempel.

Deze scherm afbeelding toont het activiteiten logboek van een werk ruimte:

[![Scherm opname van het activiteiten logboek van een werk ruimte](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Details van Score aanvragen worden opgeslagen in Application Insights. Application Insights wordt in uw abonnement gemaakt wanneer u een werk ruimte maakt. Geregistreerde gegevens bevatten onder andere de volgende velden:

* HTTPMethod
* User agent
* ComputeType
* RequestUrl
* Status code
* RequestId
* Duur

> [!IMPORTANT]
> Sommige acties in de Azure Machine Learning-werk ruimte registreren geen gegevens in het activiteiten logboek. Bijvoorbeeld, het begin van een trainings uitvoering en de registratie van een model worden niet geregistreerd.
>
> Sommige van deze acties worden weer gegeven in het gebied **activiteiten** van uw werk ruimte, maar deze meldingen geven niet aan wie de activiteit heeft gestart.

## <a name="data-flow-diagrams"></a>Gegevensstroom diagrammen

### <a name="create-workspace"></a>Werkruimte maken

In het volgende diagram ziet u de werk stroom werk ruimte maken.

* U meldt zich aan bij Azure AD vanaf een van de ondersteunde Azure Machine Learning-clients (Azure CLI, python SDK, Azure Portal) en vraagt het juiste Azure Resource Manager-token op.
* U roept Azure Resource Manager aan om de werk ruimte te maken. 
* Azure Resource Manager neemt contact op met de Azure Machine Learning Resource provider om de werk ruimte in te richten.

Aanvullende resources worden tijdens het maken van de werk ruimte gemaakt in het abonnement van de gebruiker:

* Key Vault (voor het opslaan van geheimen)
* Een Azure-opslag account (inclusief Blob en bestands share)
* Azure Container Registry (voor het opslaan van docker-installatie kopieën voor het afzetten/scoren en experimenteren)
* Application Insights (voor het opslaan van telemetrie)

De gebruiker kan ook andere reken doelen inrichten die zijn gekoppeld aan een werk ruimte (zoals Azure Kubernetes service of Vm's), indien nodig.

[![Werkruimte werk stroom maken](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Bron code opslaan (trainings scripts)

In het volgende diagram ziet u de werk stroom voor code momentopnamen.

Gekoppeld aan een Azure Machine Learning werk ruimte zijn mappen (experimenten) die de bron code (trainings scripts) bevatten. Deze scripts worden opgeslagen op uw lokale machine en in de Cloud (in de Azure Blob-opslag voor uw abonnement). De code momentopnamen worden gebruikt voor uitvoering of inspectie voor historische controle.

[![Workflow voor code momentopname](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Bezig met trainen

In het volgende diagram ziet u de werk stroom training.

* Azure Machine Learning wordt aangeroepen met de moment opname-ID voor de code momentopname die is opgeslagen in de vorige sectie.
* Azure Machine Learning maakt een run-ID (optioneel) en een Machine Learning-service token, dat later wordt gebruikt door Compute-doelen als Machine Learning Compute/Vm's om te communiceren met de Machine Learning-service.
* U kunt kiezen voor een beheerde Compute-doel (zoals Machine Learning Compute) of een niet-beheerd reken doel (zoals Vm's) om trainings taken uit te voeren. Dit zijn de gegevens stromen voor beide scenario's:
   * Vm's/HDInsight, toegankelijk via SSH-referenties in een sleutel kluis in het micro soft-abonnement. Azure Machine Learning voert beheer code uit op het berekenings doel dat:

   1. Bereidt de omgeving voor. (Docker is een optie voor Vm's en lokale computers. Raadpleeg de volgende stappen voor Machine Learning Compute om te begrijpen hoe het uitvoeren van experimenten op docker-containers werkt.)
   1. Hiermee downloadt u de code.
   1. Hiermee stelt u omgevings variabelen en configuraties in.
   1. Voert gebruikers scripts uit (de code momentopname die in de vorige sectie is vermeld).

   * Machine Learning Compute, toegankelijk via een door een werk ruimte beheerde identiteit.
Omdat Machine Learning Compute een beheerd reken doel is (dat wil zeggen, wordt het beheerd door micro soft), wordt het uitgevoerd onder uw micro soft-abonnement.

   1. De externe docker-constructie wordt zo nodig gestart.
   1. De beheer code wordt geschreven naar de Azure Files share van de gebruiker.
   1. De container wordt gestart met een eerste opdracht. Dat wil zeggen, beheer code zoals beschreven in de vorige stap.

#### <a name="querying-runs-and-metrics"></a>Uitvoeringen van query's en metrische gegevens

In het onderstaande stroom diagram treedt deze stap op wanneer het doel voor het berekenen van de training de metrische uitvoerings waarden terugschrijft naar Azure Machine Learning van opslag in de Cosmos DB-Data Base. Clients kunnen Azure Machine Learning aanroepen. Met Machine Learning worden de metrische gegevens van de Cosmos DB-Data Base opgehaald en terug naar de client geretourneerd.

[![Werk stroom voor training](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Webservices maken

In het volgende diagram ziet u de werk stroom voor afwijzen. Defactorion of model Score is de fase waarin het geïmplementeerde model wordt gebruikt voor de voor spelling, meestal op productie gegevens.

Dit zijn de details:

* De gebruiker registreert een model met behulp van een-client zoals de Azure Machine Learning SDK.
* De gebruiker maakt een installatie kopie met behulp van een model, een score bestand en andere model afhankelijkheden.
* De docker-installatie kopie wordt gemaakt en opgeslagen in Azure Container Registry.
* De webservice wordt geïmplementeerd op het Compute-doel (Container Instances/AKS) met behulp van de installatie kopie die u in de vorige stap hebt gemaakt.
* Details van Score aanvragen worden opgeslagen in Application Insights, dat zich in het abonnement van de gebruiker bevindt.
* Telemetrie wordt ook gepusht naar het micro soft/Azure-abonnement.

[![Werk stroom afwijzen](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* [Azure Machine Learning webservices beveiligen met TLS](how-to-secure-web-service.md)
* [Een Machine Learning model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Azure Machine Learning gebruiken met Azure Firewall](how-to-access-azureml-behind-firewall.md)
* [Azure Machine Learning gebruiken met Azure Virtual Network](how-to-enable-virtual-network.md)
* [Aanbevolen procedures voor het bouwen van aanbevelings systemen](https://github.com/Microsoft/Recommenders)
* [Een real-time aanbevelings-API bouwen op Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
