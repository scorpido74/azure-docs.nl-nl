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
ms.date: 12/17/2019
ms.openlocfilehash: 4a8a548e6a073c38dbc1f5600d721a7cdb97f120
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75762821"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Enter prise Security voor Azure Machine Learning

In dit artikel vindt u informatie over de beschik bare beveiligings functies voor Azure Machine Learning.

Wanneer u een Cloud service gebruikt, is een best practice het beperken van de toegang tot alleen de gebruikers die er behoefte aan hebben. Begin met het verificatie-en autorisatie model dat door de service wordt gebruikt. U kunt ook de netwerk toegang beperken of bronnen veilig samen voegen in uw on-premises netwerk met de Cloud. Gegevens versleuteling is ook cruciaal, zowel op rest als tijdens het verplaatsen van gegevens tussen services. Ten slotte moet u de service kunnen bewaken en een audit logboek maken van alle activiteiten.

## <a name="authentication"></a>Authentication

Multi-factor Authentication wordt ondersteund als Azure Active Directory (Azure AD) is geconfigureerd om het te gebruiken. Dit is het verificatie proces:

1. De client meldt zich aan bij Azure AD en ontvangt een Azure Resource Manager token.  Gebruikers en service-principals worden volledig ondersteund.
1. De client geeft het token aan Azure Resource Manager en alle Azure Machine Learning.
1. De Machine Learning-service levert een Machine Learning-service token aan het gebruikers Compute-doel (bijvoorbeeld Machine Learning Compute). Dit token wordt gebruikt door het gebruikers Compute-doel om terug te bellen naar de Machine Learning-service nadat de uitvoering is voltooid. Het bereik is beperkt tot de werk ruimte.

[Verificatie ![in Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Zie het gedeelte [verificatie procedures instellen](how-to-setup-authentication.md) voor gedetailleerde voor beelden en instructies voor het instellen van verificatie, waaronder Service-Principal-verificatie voor automatische werk stromen.

### <a name="authentication-for-web-service-deployment"></a>Verificatie voor de implementatie van de webservice

Azure Machine Learning ondersteunt twee verificatie vormen voor webservices: sleutel en token. Elke webservice kan per keer slechts één vorm van authenticatie inschakelen.

|Verificatiemethode|Beschrijving|Azure Container Instances|AKS|
|---|---|---|---|
|Sleutel|Sleutels zijn statisch en hoeven niet te worden vernieuwd. Sleutels kunnen hand matig opnieuw worden gegenereerd.|Standaard uitgeschakeld| Standaard ingeschakeld|
|Token|Tokens verlopen na een opgegeven tijds periode en moeten worden vernieuwd.| Niet beschikbaar| Standaard uitgeschakeld |

Zie de [sectie verificatie](how-to-setup-authentication.md#web-service-authentication) van de webservice voor code voorbeelden over het verifiëren van webservices in azure machine learning.

## <a name="authorization"></a>Autorisatie

U kunt meerdere werkruimten maken en elke werkruimte kan worden gedeeld door meerdere personen. Wanneer u een werk ruimte deelt, kunt u de toegang hiertoe beheren door deze rollen toe te wijzen aan gebruikers:

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
| Experiment uit te voeren | ✓ | ✓ | |
| Uitvoeringen/metrische gegevens weer geven | ✓ | ✓ | ✓ |
| Model registreren | ✓ | ✓ | |
| Installatiekopie maken | ✓ | ✓ | |
| Webservice implementeren | ✓ | ✓ | |
| Modellen/afbeeldingen weer geven | ✓ | ✓ | ✓ |
| Webservice aanroepen | ✓ | ✓ | ✓ |

Als de ingebouwde rollen niet aan uw behoeften voldoen, kunt u aangepaste rollen maken. Aangepaste rollen worden alleen ondersteund voor bewerkingen in de werk ruimte en Machine Learning Compute. Aangepaste rollen kunnen Lees-, schrijf-of verwijder machtigingen hebben voor de werk ruimte en op de reken resource in die werk ruimte. U kunt de rol beschikbaar maken op een specifiek werk ruimte niveau, op een specifiek niveau van de resource groep of op een specifiek abonnements niveau. Zie [gebruikers en rollen beheren in een Azure machine learning-werk ruimte](how-to-assign-roles.md)voor meer informatie.

### <a name="securing-compute-targets-and-data"></a>Reken doelen en gegevens beveiligen

Eigen aren en inzenders kunnen alle reken doelen en gegevens archieven gebruiken die aan de werk ruimte zijn gekoppeld.  

Aan elke werk ruimte is ook een aan het systeem toegewezen beheerde identiteit gekoppeld die dezelfde naam heeft als de werk ruimte. De beheerde identiteit heeft de volgende machtigingen op gekoppelde resources die worden gebruikt in de werk ruimte.

Zie [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)voor meer informatie over beheerde identiteiten.

| Bron | Machtigingen |
| ----- | ----- |
| Werkruimte | Inzender |
| Opslagaccount | Inzender voor Storage BLOB-gegevens |
| Key Vault | Toegang tot alle sleutels, geheimen, certificaten |
| Azure Container Registry | Inzender |
| Resource groep die de werk ruimte bevat | Inzender |
| De resource groep die de sleutel kluis bevat (als deze anders is dan de naam die de werk ruimte bevat) | Inzender |

Het is niet raadzaam om beheerders de toegang tot de beheerde identiteit in te trekken voor de resources die in de voor gaande tabel worden vermeld. U kunt de toegang herstellen met behulp van de bewerking voor het opnieuw synchroniseren van sleutels.

Azure Machine Learning maakt een extra toepassing (de naam begint met `aml-` of `Microsoft-AzureML-Support-App-`) met toegang op Inzender niveau in uw abonnement voor elke werkruimte regio. Als u bijvoorbeeld één werk ruimte hebt in VS-Oost en een andere werk ruimte in Europa-noord in hetzelfde abonnement, ziet u twee van deze toepassingen. Met deze toepassingen kunt u Azure Machine Learning helpen bij het beheren van reken resources.

## <a name="network-security"></a>Netwerkbeveiliging

Azure Machine Learning is afhankelijk van andere Azure-Services voor reken resources. Reken bronnen (Compute-doelen) worden gebruikt om modellen te trainen en te implementeren. U kunt deze reken doelen maken in een virtueel netwerk. U kunt bijvoorbeeld Azure Data Science Virtual Machine gebruiken om een model te trainen en het model vervolgens implementeren in AKS.  

Zie [experimenten en interferentie uitvoeren in een virtueel netwerk](how-to-enable-virtual-network.md)voor meer informatie.

## <a name="data-encryption"></a>Data-encryptie

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning slaat moment opnamen, uitvoer en logboeken op in het Azure Blob Storage-account dat is gekoppeld aan de Azure Machine Learning-werk ruimte en uw abonnement. Alle gegevens die zijn opgeslagen in Azure Blob Storage, worden op rest versleuteld met door micro soft beheerde sleutels.

Zie [Azure Storage versleuteling met door de klant beheerde sleutels in azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)voor meer informatie over het gebruik van uw eigen sleutels voor gegevens die zijn opgeslagen in Azure Blob-opslag.

Trainings gegevens worden doorgaans ook opgeslagen in Azure Blob-opslag, zodat deze toegankelijk is voor Compute-doel stellingen. Deze opslag wordt niet beheerd door Azure Machine Learning, maar gekoppeld aan Compute-doelen als een extern bestands systeem.

Zie [toegangs sleutels voor opslag opnieuw genereren](how-to-change-storage-access-key.md)voor meer informatie over het opnieuw genereren van de toegangs sleutels voor de Azure Storage-accounts die worden gebruikt in uw werk ruimte.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning gegevens en meta gegevens opslaat in het Azure Cosmos DB exemplaar dat is gekoppeld aan een micro soft-abonnement dat wordt beheerd door Azure Machine Learning. Alle gegevens die zijn opgeslagen in Azure Cosmos DB, worden op rest versleuteld met door micro soft beheerde sleutels.

#### <a name="azure-container-registry"></a>Azure Container Registry

Alle container installatie kopieën in het REGI ster (Azure Container Registry) worden op rest versleuteld. Azure versleutelt automatisch een afbeelding voordat deze wordt opgeslagen en ontsleuteld wanneer Azure Machine Learning de installatie kopie ophaalt.

#### <a name="machine-learning-compute"></a>Machine Learning Compute

De besturingssysteem schijf voor elk reken knooppunt dat in Azure Storage is opgeslagen, is versleuteld met door micro soft beheerde sleutels in Azure Machine Learning-opslag accounts. Dit Compute-doel is kortstondig en clusters worden meestal omlaag geschaald wanneer er geen uitvoeringen in de wachtrij worden geplaatst. De inrichting van de onderliggende virtuele machine is ongedaan gemaakt en de besturingssysteem schijf wordt verwijderd. Azure Disk Encryption wordt niet ondersteund voor de besturingssysteem schijf.

Elke virtuele machine heeft ook een lokale tijdelijke schijf voor besturingssysteem bewerkingen. Als u wilt, kunt u de trainings gegevens voor de schijf gebruiken. De schijf is niet versleuteld.
Zie [Azure Data Encryption at rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)(Engelstalig) voor meer informatie over de werking van versleuteling bij rest in Azure.

### <a name="encryption-in-transit"></a>Versleuteling 'in transit'

U kunt SSL gebruiken voor het beveiligen van interne communicatie tussen Azure Machine Learning micro Services en het beveiligen van externe aanroepen aan het Score-eind punt. Alle Azure Storage toegang vindt ook plaats via een beveiligd kanaal.

Zie [SSL gebruiken om een webservice te beveiligen via Azure machine learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)voor meer informatie.

### <a name="using-azure-key-vault"></a>Azure Key Vault gebruiken

Azure Machine Learning gebruikt het Azure Key Vault exemplaar dat is gekoppeld aan de werk ruimte om referenties van verschillende soorten op te slaan:

* Het gekoppelde opslag account connection string
* Wacht woorden voor Azure container repository-instanties
* Verbindings reeksen naar gegevens archieven

SSH-wacht woorden en sleutels voor het berekenen van doelen zoals Azure HDInsight en Vm's worden opgeslagen in een afzonderlijke sleutel kluis die is gekoppeld aan het micro soft-abonnement. Azure Machine Learning slaat geen wacht woorden of sleutels op die door gebruikers worden verschaft. In plaats daarvan worden er eigen SSH-sleutels gegenereerd, geautoriseerd en opgeslagen om verbinding te maken met Vm's en HDInsight om de experimenten uit te voeren.

Aan elke werk ruimte is een door het systeem toegewezen beheerde identiteit gekoppeld die dezelfde naam heeft als de werk ruimte. Deze beheerde identiteit heeft toegang tot alle sleutels, geheimen en certificaten in de sleutel kluis.

## <a name="monitoring"></a>Controleren

### <a name="metrics"></a>Metrische gegevens

U kunt Azure Monitor metrische gegevens gebruiken om metrische gegevens voor uw Azure Machine Learning-werk ruimte weer te geven en te controleren. Selecteer in de [Azure Portal](https://portal.azure.com)uw werk ruimte en selecteer vervolgens **metrische gegevens**:

[![scherm opname van voor beelden van metrische gegevens voor een werk ruimte](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

De metrische gegevens bevatten informatie over uitvoeringen, implementaties en registraties.

Zie [metrische gegevens in azure monitor](/azure/azure-monitor/platform/data-platform-metrics)voor meer informatie.

### <a name="activity-log"></a>Activiteitenlogboek

U kunt het activiteiten logboek van een werk ruimte bekijken om verschillende bewerkingen te bekijken die worden uitgevoerd op de werk ruimte. Het logboek bevat basis informatie, zoals de naam van de bewerking, de gebeurtenis initiator en de tijds tempel.

Deze scherm afbeelding toont het activiteiten logboek van een werk ruimte:

[![scherm opname van het activiteiten logboek van een werk ruimte](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Details van Score aanvragen worden opgeslagen in Application Insights. Application Insights wordt in uw abonnement gemaakt wanneer u een werk ruimte maakt. Geregistreerde gegevens zijn onder andere velden als HTTPMethod, User agent, ComputeType, RequestUrl, status code, aanvraag code en duur.

> [!IMPORTANT]
> Sommige acties in de Azure Machine Learning-werk ruimte registreren geen gegevens in het activiteiten logboek. Bijvoorbeeld, het begin van een trainings uitvoering en de registratie van een model worden niet geregistreerd.
>
> Sommige van deze acties worden weer gegeven in het gebied **activiteiten** van uw werk ruimte, maar deze meldingen geven niet aan wie de activiteit heeft gestart.

## <a name="data-flow-diagrams"></a>Gegevensstroom diagrammen

### <a name="create-workspace"></a>Werkruimte maken

In het volgende diagram ziet u de werk stroom werk ruimte maken.

* De gebruiker meldt zich aan bij Azure AD vanaf een van de ondersteunde Azure Machine Learning-clients (Azure CLI, python SDK, Azure Portal) en vraagt het juiste Azure Resource Manager-token op.
* De gebruiker roept Azure Resource Manager op om de werk ruimte te maken. 
* Azure Resource Manager neemt contact op met de Azure Machine Learning Resource provider om de werk ruimte in te richten.

Aanvullende resources worden tijdens het maken van de werk ruimte gemaakt in het abonnement van de gebruiker:

* Key Vault (voor het opslaan van geheimen)
* Een Azure-opslag account (inclusief Blob en bestands share)
* Azure Container Registry (voor het opslaan van docker-installatie kopieën voor het afzetten/scoren en experimenteren)
* Application Insights (voor het opslaan van telemetrie)

De gebruiker kan ook andere reken doelen inrichten die zijn gekoppeld aan een werk ruimte (zoals Azure Kubernetes service of Vm's), indien nodig.

[werk stroom werkruimte ![maken](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Bron code opslaan (trainings scripts)

In het volgende diagram ziet u de werk stroom voor code momentopnamen.

Gekoppeld aan een Azure Machine Learning werk ruimte zijn mappen (experimenten) die de bron code (trainings scripts) bevatten. Deze scripts worden opgeslagen op uw lokale machine en in de Cloud (in de Azure Blob-opslag voor uw abonnement). De code momentopnamen worden gebruikt voor uitvoering of inspectie voor historische controle.

[werk stroom voor moment opnamen van ![code](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Training

In het volgende diagram ziet u de werk stroom training.

* Azure Machine Learning wordt aangeroepen met de moment opname-ID voor de code momentopname die is opgeslagen in de vorige sectie.
* Azure Machine Learning maakt een run-ID (optioneel) en een Machine Learning-service token, dat later wordt gebruikt door Compute-doelen als Machine Learning Compute/Vm's om te communiceren met de Machine Learning-service.
* U kunt kiezen voor een beheerde Compute-doel (zoals Machine Learning Compute) of een niet-beheerd reken doel (zoals Vm's) om uw trainings taken uit te voeren. Dit zijn de gegevens stromen voor beide scenario's:
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

[werk stroom voor ![training](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Webservices maken

In het volgende diagram ziet u de werk stroom voor afwijzen. Defactorion of model Score is de fase waarin het geïmplementeerde model wordt gebruikt voor de voor spelling, meestal op productie gegevens.

Dit zijn de details:

* De gebruiker registreert een model met behulp van een-client zoals de Azure Machine Learning SDK.
* De gebruiker maakt een installatie kopie met behulp van een model, een score bestand en andere model afhankelijkheden.
* De docker-installatie kopie wordt gemaakt en opgeslagen in Azure Container Registry.
* De webservice wordt geïmplementeerd op het Compute-doel (Container Instances/AKS) met behulp van de installatie kopie die u in de vorige stap hebt gemaakt.
* Details van Score aanvragen worden opgeslagen in Application Insights, dat zich in het abonnement van de gebruiker bevindt.
* Telemetrie wordt ook gepusht naar het micro soft/Azure-abonnement.

[werk stroom voor ![afwijzen](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* [Azure Machine Learning-webservices met SSL beveiligde](how-to-secure-web-service.md)
* [Een Machine Learning model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Het uitvoeren van voorspellingen van batch](how-to-run-batch-predictions.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure Machine Learning gebruiken met Azure Virtual Network](how-to-enable-virtual-network.md)
* [Aanbevolen procedures voor het bouwen van aanbevelings systemen](https://github.com/Microsoft/Recommenders)
* [Een real-time aanbevelings-API bouwen op Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
