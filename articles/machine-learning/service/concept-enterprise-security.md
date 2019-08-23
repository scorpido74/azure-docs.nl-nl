---
title: Beveiliging voor bedrijven
titleSuffix: Azure Machine Learning service
description: 'Veilig gebruik van Azure Machine Learning-service: verificatie, autorisatie, netwerk beveiliging, gegevens versleuteling en bewaking.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/07/2019
ms.openlocfilehash: 510f58cc0b71fb75ac6f5e15fc883c3caf4a8f9a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897889"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Enter prise Security voor Azure Machine Learning-service

In dit artikel vindt u informatie over de beveiligings functies die beschikbaar zijn in de Azure machine learning-service.

Wanneer u een Cloud service gebruikt, is dit een best practice om de toegang alleen te beperken tot de gebruikers die het nodig hebben. Dit begint met het verificatie-en autorisatie model dat door de service wordt gebruikt. U kunt ook de netwerk toegang beperken of bronnen veilig samen voegen in uw on-premises netwerk met de Cloud. Gegevens versleuteling is ook cruciaal, zowel bij rust als tijdens het verplaatsen van gegevens tussen services. Ten slotte moet u de service kunnen bewaken en een audit logboek maken van alle activiteiten.

## <a name="authentication"></a>Authentication

Multi factor Authentication wordt ondersteund als Azure Active Directory (Azure AD) voor hetzelfde is geconfigureerd.

* Client meldt zich aan bij Azure AD en ontvangt Azure Resource Manager token.  Gebruikers en service-principals worden volledig ondersteund.
* Client presenteert token om alle Azure Machine Learning Services te Azure Resource Manager &
* Azure Machine Learning-service levert een Azure Machine Learning-token aan de gebruiker die wordt berekend. Bijvoorbeeld Machine Learning Compute. Dit token wordt gebruikt door de gebruikers Compute om terug te bellen naar Azure Machine Learning service (limieten voor de werk ruimte) nadat de uitvoering is voltooid.

[![Scherm afbeelding die laat zien hoe verificatie werkt in Azure Machine Learning service](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Verificatie voor de implementatie van de webservice

Azure Machine Learning ondersteunt twee vormen van verificatie voor webservices, sleutels en tokens. Elke webservice kan slechts één vorm van verificatie tegelijk inschakelen.

|Verificatiemethode|ACI|AKS|
|---|---|---|
|Sleutel|Standaard uitgeschakeld| Standaard ingeschakeld|
|Token| Niet beschikbaar| Standaard uitgeschakeld |

#### <a name="authentication-with-keys"></a>Verificatie met sleutels

Wanneer u sleutel verificatie inschakelt voor een implementatie, maakt u automatisch verificatie sleutels.

* Verificatie is standaard ingeschakeld wanneer u naar de Azure Kubernetes-service implementeert.
* Verificatie is standaard uitgeschakeld wanneer u implementeert naar Azure Container Instances.

Als u sleutel verificatie wilt inschakelen, `auth_enabled` gebruikt u de para meter bij het maken of bijwerken van een implementatie.

Als sleutel verificatie is ingeschakeld, kunt u de `get_keys` methode gebruiken om een primaire en secundaire verificatie sleutel op te halen:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Als u een sleutel opnieuw moet genereren, gebruikt u[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

#### <a name="authentication-with-tokens"></a>Verificatie met tokens

Wanneer u token verificatie inschakelt voor een webservice, moet een gebruiker een Azure Machine Learning JSON Web Token aan de webservice geven om deze te openen.

* Token verificatie is standaard uitgeschakeld wanneer u implementeert in azure Kubernetes service.
* Token authenticatie wordt niet ondersteund wanneer u implementeert naar Azure Container Instances.

Als u de verificatie van tokens `token_auth_enabled` wilt beheren, gebruikt u de para meter bij het maken of bijwerken van een implementatie.

Als token verificatie is ingeschakeld, kunt u de `get_token` methode gebruiken om een JWT-token op te halen en de verval tijd van dat token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> U moet een nieuw token aanvragen na de tijd van `refresh_by` de token.
>
> We raden u ten zeerste aan om uw Azure Machine Learning-werk ruimte te maken in dezelfde regio als uw Azure Kubernetes service-cluster. Als u wilt verifiëren met een token, wordt er door de webservice een aanroep naar de regio waarin uw Azure Machine Learning-werk ruimte is gemaakt. Als de regio van uw werk ruimte niet beschikbaar is, kunt u geen token voor uw webservice ophalen, zelfs als uw cluster zich in een andere regio bevindt dan uw werk ruimte. Dit leidt ertoe dat Azure AD-verificatie niet beschikbaar is totdat de regio van de werk ruimte weer beschikbaar is. Daarnaast is de afstand tussen de regio van uw cluster en de regio van uw werk ruimte groter, hoe langer het duurt om een token op te halen.

## <a name="authorization"></a>Authorization

U kunt meerdere werkruimten maken en elke werkruimte kan worden gedeeld door meerdere personen. Wanneer u een werk ruimte deelt, kunt u de toegang hiertoe beheren door de volgende rollen aan gebruikers toe te wijzen:

* Eigenaar
* Inzender
* Lezer

De volgende tabel bevat enkele van de belangrijkste Azure Machine Learning service bewerkingen en de rollen die deze kunnen uitvoeren:

| Bewerking van Azure Machine Learning-service | Eigenaar | Inzender | Lezer |
| ---- |:----:|:----:|:----:|
| Werkruimte maken | ✓ | ✓ | |
| Werk ruimte delen | ✓ | |  |
| Reken proces maken | ✓ | ✓ | |
| Reken kracht koppelen | ✓ | ✓ | |
| Gegevens opslag koppelen | ✓ | ✓ | |
| Een experiment uitvoeren | ✓ | ✓ | |
| Uitvoeringen/metrische gegevens weer geven | ✓ | ✓ | ✓ |
| Model registreren | ✓ | ✓ | |
| Installatiekopie maken | ✓ | ✓ | |
| Webservice implementeren | ✓ | ✓ | |
| Modellen/afbeeldingen weer geven | ✓ | ✓ | ✓ |
| Webservice aanroepen | ✓ | ✓ | ✓ |

Als de ingebouwde rollen ontoereikend zijn voor uw behoeften, kunt u ook aangepaste rollen maken. De enige aangepaste rollen die worden ondersteund, zijn voor bewerkingen op de werk ruimte en Machine Learning Compute. De aangepaste rollen hebben mogelijk de machtigingen lezen, schrijven of verwijderen voor de werk ruimte en de reken resource in die werk ruimte. U kunt de rol beschikbaar maken op een specifiek werk ruimte niveau, op een specifiek niveau van een resource groep of op een specifiek abonnements niveau. Zie [gebruikers en rollen beheren in een Azure machine learning-werk ruimte](how-to-assign-roles.md) voor meer informatie

### <a name="securing-compute-and-data"></a>Berekenings-en gegevens beveiliging

Eigen aren en inzenders kunnen alle reken doelen en gegevens archieven gebruiken die aan de werk ruimte zijn gekoppeld.  
Elke werk ruimte heeft ook een aan het systeem toegewezen beheerde identiteit (met dezelfde naam als de werk ruimte) met de volgende machtigingen voor gekoppelde resources die worden gebruikt in de werk ruimte:

Zie [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie over beheerde identiteiten

| Resource | Machtigingen |
| ----- | ----- |
| Werkruimte | Inzender |
| Opslagaccount | Bijdrager voor opslagblobgegevens |
| Key Vault | Toegang tot alle sleutels, geheimen, certificaten |
| Azure Container Registry | Inzender |
| Resource groep die de werk ruimte bevat | Inzender |
| De resource groep die de Key Vault bevat (als deze anders is dan die van de werk ruimte) | Inzender |

Het wordt aanbevolen dat beheerders de toegang van de beheerde identiteit niet intrekken voor de hierboven genoemde resources. De toegang kan worden hersteld met de bewerking voor het opnieuw synchroniseren van sleutels.

Er wordt door Azure machine learning service een extra toepassing (naam `aml-`begint met) gemaakt met de toegang op Inzender niveau in uw abonnement voor elke werkruimte regio. Voor ex. Als u een werk ruimte in VS-Oost en een andere werk ruimte in Europa-noord in hetzelfde abonnement hebt, ziet u twee dergelijke toepassingen. Dit is nodig om ervoor te zorgen dat Azure Machine Learning-service reken bronnen kan beheren.

## <a name="network-security"></a>Netwerkbeveiliging

De Azure Machine Learning-service is afhankelijk van andere Azure-Services voor reken resources. Reken bronnen (Compute-doelen) worden gebruikt om modellen te trainen en te implementeren. Deze reken doelen kunnen binnen een virtueel netwerk worden gemaakt. U kunt bijvoorbeeld de micro soft-Data Science Virtual Machine gebruiken om een model te trainen en het model vervolgens te implementeren in azure Kubernetes service (AKS).  

Zie [experimenten en interferentie uitvoeren in een virtueel netwerk](how-to-enable-virtual-network.md)voor meer informatie.

## <a name="data-encryption"></a>Gegevensversleuteling

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning-service slaat moment opnamen, uitvoer en logboeken op in het Azure Blob Storage-account dat is gekoppeld aan de Azure Machine Learning service-werk ruimte en zich in het abonnement van de gebruiker bevindt. Alle gegevens die zijn opgeslagen in Azure Blob Storage, worden op rest versleuteld met door micro soft beheerde sleutels.

Zie [Storage service Encryption met door de klant beheerde sleutels in azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)voor meer informatie over het nemen van uw eigen sleutels voor de gegevens die zijn opgeslagen in Azure Blob Storage.

Trainings gegevens worden doorgaans ook opgeslagen in Azure Blob-opslag, zodat deze toegankelijk is voor trainings berekeningen. Deze opslag wordt niet beheerd door Azure Machine Learning, maar is gekoppeld aan Compute als een extern bestands systeem.

Zie het artikel [toegangs sleutels voor opslag opnieuw genereren](how-to-change-storage-access-key.md) voor meer informatie over het opnieuw genereren van de toegangs sleutels voor de Azure Storage-accounts die worden gebruikt in uw werk ruimte.

#### <a name="cosmos-db"></a>Cosmos DB

Azure Machine Learning-service slaat metrische gegevens en meta data op voor de Cosmos DB die in een micro soft-abonnement vallen dat wordt beheerd door Azure Machine Learning service. Alle gegevens die zijn opgeslagen in Cosmos DB, worden op rest versleuteld met door micro soft beheerde sleutels.

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)

Alle container installatie kopieën in uw REGI ster (ACR) worden op rest versleuteld. Azure versleutelt automatisch een afbeelding voordat deze wordt opgeslagen en ontsleuteld wanneer Azure Machine Learning-service de installatie kopie ophaalt.

#### <a name="machine-learning-compute"></a>Machine Learning Compute

De besturingssysteem schijf voor elk reken knooppunt wordt opgeslagen in Azure Storage is versleuteld met door micro soft beheerde sleutels in Azure Machine Learning service-opslag accounts. Dit Compute-doel is kortstondig en clusters worden meestal omlaag geschaald wanneer er geen uitvoeringen in de wachtrij worden geplaatst. De onderliggende virtuele machine is ongedaan gemaakt en de besturingssysteem schijf is verwijderd. Azure Disk Encryption wordt niet ondersteund voor de besturingssysteem schijf.
Elke virtuele machine heeft ook een lokale tijdelijke schijf voor besturingssysteem bewerkingen. De schijf kan eventueel ook worden gebruikt voor het faseren van de trainings gegevens. De schijf is niet versleuteld.
Zie [Azure Data Encryption-at-rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)voor meer informatie over de werking van versleuteling bij rest in Azure.

### <a name="encryption-in-transit"></a>Versleuteling in transit

Zowel interne communicatie tussen diverse Azure Machine Learning micro Services als externe communicatie van het aanroepen van het Score-eind punt worden ondersteund met SSL. Alle Azure Storage toegang is ook via een beveiligd kanaal.
Zie [Secure Azure machine learning Web Services using SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service)(Engelstalig) voor meer informatie.

### <a name="using-azure-key-vault"></a>Azure Key Vault gebruiken

Key Vault exemplaar dat is gekoppeld aan de werk ruimte, wordt door Azure Machine Learning service gebruikt voor het opslaan van referenties van verschillende soorten:

* Het gekoppelde opslag account connection string
* Wacht woorden voor Azure container repository-instanties
* Verbindings reeksen voor gegevens archieven.

SSH-wacht woorden en sleutels voor het berekenen van doelen zoals HDI HDInsight en VM worden opgeslagen in een afzonderlijke Key Vault die aan micro soft-abonnement is gekoppeld. Azure Machine Learning-service slaat geen wacht woorden of sleutels op die door de gebruiker worden verschaft, in plaats daarvan zijn eigen SSH-sleutels gegenereerd, geautoriseerd en opgeslagen om verbinding te maken met VM/HDInsight om de experimenten uit te voeren.
Elke werk ruimte heeft een aan het systeem toegewezen beheerde identiteit (met dezelfde naam als de werk ruimte) die toegang heeft tot alle sleutels, geheimen en certificaten in de Key Vault.

## <a name="monitoring"></a>Bewaking

### <a name="metrics"></a>Metrische gegevens

Azure Monitor metrieken kunnen worden gebruikt om metrische gegevens weer te geven en te bewaken voor uw Azure Machine Learning service-werk ruimte. Selecteer in de [Azure Portal](https://portal.azure.com)uw werk ruimte en gebruik vervolgens de koppeling __metrische gegevens__ .

[![Scherm opname van voor beelden van metrische gegevens voor een werk ruimte](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

Metrische gegevens bevatten informatie over uitvoeringen, implementaties en registraties.

Zie [metrische gegevens in azure monitor](/azure/azure-monitor/platform/data-platform-metrics)voor meer informatie.

### <a name="activity-log"></a>Activiteitenlogboek

U ziet het activiteiten logboek onder de werk ruimte om verschillende bewerkingen te bekijken die worden uitgevoerd op de werk ruimte en de basis informatie op te halen, zoals de bewerkings naam, de gebeurtenis gestart door, de tijds tempel enzovoort.

De volgende scherm afbeelding toont het activiteiten logboek voor een werk ruimte:

[![Scherm opname met activiteiten logboek onder een werk ruimte](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

Details van Score aanvragen worden opgeslagen in Application Insight, dat wordt gemaakt in het abonnement van de gebruiker tijdens het maken van de werk ruimte. Geregistreerde gegevens zijn onder andere velden als HTTPMethod, User agent, ComputeType, RequestUrl, status code, aanvraag code, duur, enzovoort.

> [!IMPORTANT]
> Sommige acties in de Azure Machine Learning-werk ruimte registreren geen gegevens in het activiteiten logboek. U kunt bijvoorbeeld een training starten of een model registreren.
>
> Sommige van deze acties worden weer gegeven in het gebied __activiteiten__ van uw werk ruimte, maar ze geven niet aan wie de activiteit heeft gestart.

## <a name="data-flow-diagram"></a>Gegevens stroom diagram

### <a name="create-workspace"></a>Werkruimte maken

In het volgende diagram ziet u de werk stroom werk ruimte maken.
Gebruiker meldt zich aan bij Azure AD vanuit een van de ondersteunde Azure Machine Learning-serviceclient (CLI, python SDK, Azure Portal) en vraagt het juiste Azure Resource Manager-token op. De gebruiker roept vervolgens Azure Resource Manager aan om de werk ruimte te maken.  Azure Resource Manager contact op met de resource provider van Azure Machine Learning service om de werk ruimte in te richten.  Aanvullende resources worden tijdens het maken van de werk ruimte gemaakt in het abonnement van de klant:

* Sleutel kluis (voor het opslaan van geheimen)
* Een Azure Storage account (inclusief BLOB & file share)
* Azure Container Registry (voor het opslaan van docker-installatie kopieën voor het afzetten/scoren en experimenteren)
* Application Insights (voor het opslaan van telemetrie)

Andere reken processen die zijn gekoppeld aan een werk ruimte (Azure Kubernetes service, VM enzovoort) kunnen ook worden ingericht door klanten als dat nodig is.

[![Scherm opname van werk stroom werk ruimte maken](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>Bron code opslaan (trainings scripts)

In het volgende diagram ziet u de werk stroom voor code momentopnamen.
Gekoppeld aan een Azure Machine Learning service werkruimte zijn mappen (experimenten), die de bron code (trainings scripts) bevatten.  Deze scripts worden opgeslagen op de lokale computer van de klant en in de Cloud (in de Azure-Blob Storage onder het abonnement van de klant). De code momentopnamen worden gebruikt voor uitvoering of inspectie voor historische controle.

[![Scherm opname van werk stroom werk ruimte maken](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>Training

In het volgende diagram ziet u de werk stroom training.

* Azure Machine Learning-service wordt aangeroepen met de moment opname-ID voor de code momentopname die hierboven is opgeslagen
* Azure Machine Learning-service maakt een run-ID (optioneel) & Azure Machine Learning-service token, dat later wordt gebruikt door de reken doelen als Machine Learning Compute/VM om terug te praten naar Azure Machine Learning service
* U kunt kiezen voor een beheerde Compute (bijvoorbeeld Machine Learning Compute) of onbeheerde reken kracht (bijvoorbeeld VM) om uw trainings taken uit te voeren. De gegevens stroom wordt uitgelegd voor de volgende scenario's:
* (VM/HDInsight – toegankelijk via SSH-referenties in Key Vault in het micro soft-abonnement) Azure Machine Learning-service voert beheer code uit op reken doel die:

   1. Bereidt de omgeving voor. (Docker is ook een optie voor VM en lokaal. Raadpleeg de volgende stappen voor Machine Learning Compute om te begrijpen hoe het uitvoeren van experiment op docker-container werkt.)
   1. Hiermee downloadt u de code.
   1. Hiermee stelt u omgevings variabelen en configuraties in.
   1. Hiermee wordt het gebruikers script uitgevoerd (code momentopname hierboven genoemd).

* (Machine Learning Compute: toegankelijk via door werk ruimte beheerde identiteit) Omdat Machine Learning Compute een beheerde Compute is, wordt deze beheerd door micro soft, omdat deze wordt uitgevoerd onder het micro soft-abonnement.

   1. De externe docker-constructie wordt zo nodig gestart.
   1. Hiermee schrijft u de beheer code naar de gebruiker Azure file share.
   1. Start de container met een eerste opdracht, dat wil zeggen, beheer code zoals beschreven in de vorige stap.

#### <a name="querying-runs-and-metrics"></a>Uitvoeringen van query's en metrische gegevens

Deze stap wordt weer gegeven in de stroom waar de cursus Compute de *metrische gegevens* van de uitvoering naar de Azure machine learning-service schrijft, waar deze wordt opgeslagen in de Cosmos db. Clients kunnen Azure Machine Learning-service aanroepen die op hun beurt de metrische gegevens van de Cosmos DB ophaalt en weer terugstuurt naar de client.

[![Scherm opname van werk stroom werk ruimte maken](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>Webservices maken

In het volgende diagram ziet u de werk stroom voor afwijzen. Defactorion of model Score is de fase waarin het geïmplementeerde model wordt gebruikt voor de voor spelling, meestal op productie gegevens.
Zie de details hieronder:

* Gebruiker registreert een model met een client als Azure ML SDK
* Gebruiker maakt installatie kopie met behulp van model, Score bestand en andere model afhankelijkheden
* De docker-installatie kopie wordt gemaakt en opgeslagen in ACR
* De webservice wordt geïmplementeerd op het Compute-doel (ACI/AKS) met behulp van de installatie kopie die hierboven is gemaakt
* Details van Score aanvragen worden opgeslagen in Application Insight, dat zich in het abonnement van de gebruiker bevindt
* Telemetrie wordt ook gepusht naar micro soft/Azure-abonnement

[![Scherm opname van werk stroom werk ruimte maken](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>Volgende stappen

* [Azure Machine Learning-webservices met SSL beveiligde](how-to-secure-web-service.md)
* [Een ML-Model dat is geïmplementeerd als een webservice gebruiken](how-to-consume-web-service.md)
* [Het uitvoeren van voorspellingen van batch](how-to-run-batch-predictions.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Azure Machine Learning Service-SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure Machine Learning-service gebruiken met virtuele Azure-netwerken](how-to-enable-virtual-network.md)
* [Aanbevolen procedures voor het bouwen van aanbevelings systemen](https://github.com/Microsoft/Recommenders)
* [Een real-time aanbevelings-API bouwen op Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
