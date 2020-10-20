---
title: "& quota's voor resources beheren"
titleSuffix: Azure Machine Learning
description: Meer informatie over de quota's voor Azure Machine Learning en hoe u meer quota kunt aanvragen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: 93cf29651110356023752f2377c9a0fc358982a7
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203042"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>& quota voor resources met Azure Machine Learning beheren

Azure gebruikt limieten en quota's om budget overschrijdingen te voor komen door fraude en om te voldoen aan de Azure-capaciteits beperkingen. Houd rekening met deze beperkingen als u schaalt voor werk belastingen voor productie. In dit artikel leert u het volgende:

> [!div class="checklist"]
> + Standaard limieten op Azure-resources met betrekking tot [Azure machine learning](overview-what-is-azure-ml.md).
> + Uw quota's en limieten weer geven.
> + Quota voor werkruimte niveau maken.
> + Het quotum voor aanvragen is verhoogd.
> + Persoonlijk eind punt en DNS-quota.

Naast het beheren van quota's kunt u ook leren hoe u [& beheer kosten voor Azure machine learning plant](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Speciale overwegingen

+ Een quotum is een krediet limiet, geen capaciteits garantie. Als u grote capaciteits behoeften hebt, [neemt u contact op met de ondersteuning van Azure om uw quotum te verhogen](#request-quota-increases).

+ Quota wordt gedeeld door alle services in uw abonnementen, met inbegrip van Azure Machine Learning. Bereken het gebruik over alle services bij het evalueren van capaciteit.
    + Azure Machine Learning Compute is een uitzonde ring en heeft een afzonderlijke quota van het core Compute-quotum. 

+ De standaard limieten variëren per aanbiedings categorie type, zoals een gratis proef versie, een betalen per gebruik-en VM-serie (Virtual Machine), zoals dv2, F, G, enzovoort.

## <a name="default-resource-quotas"></a>Standaard resource quota

In deze sectie vindt u informatie over de standaard-en maximum quotum limieten voor de volgende resources:

+ Virtuele machines
+ Azure Machine Learning Compute
+ Azure Machine Learning pijp lijnen
+ Container Instances
+ Storage

> [!IMPORTANT]
> Limieten zijn onderhevig aan wijzigingen. De meest recente kan altijd worden gevonden op het quota [document](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) op service niveau voor alle Azure.

### <a name="virtual-machines"></a>Virtuele machines
Elk Azure-abonnement heeft een limiet voor het aantal virtuele machines in alle services. De kernen van virtuele machines hebben een regionale limiet en een regionale limiet per grootte reeks (dv2, F, enz.). Beide limieten worden afzonderlijk afgedwongen.

Neem bijvoorbeeld een abonnement met een limiet van 30 VM-cores voor US - oost, een limiet van 30 cores voor de A-serie en een limiet van 30 cores voor de D-serie. Dit abonnement mag 30 a1-vm's of 30 D1 Vm's implementeren, of een combi natie van de twee die niet meer dan 30 kernen overschrijdt.

Limieten voor virtuele machines kunnen niet worden geactiveerd boven de waarde die in de volgende tabel wordt weer gegeven.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
[Azure machine learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed) heeft een standaard quotum limiet op het aantal kernen en het aantal unieke reken resources dat per regio in een abonnement is toegestaan. Dit quotum is gescheiden van het kern quotum van de virtuele machine uit de vorige sectie.

[Vraag een quotum verhoging](#request-quota-increases) aan om de limieten in deze sectie te verhogen tot de **maximum limiet** die in de tabel wordt weer gegeven.

Beschik bare resources:
+ **Toegewezen kern geheugens per regio** hebben een standaard limiet van 24-300, afhankelijk van het type abonnement.  Het aantal toegewijde kernen per abonnement kan worden verhoogd voor elke VM-serie. Gespecialiseerde VM-families zoals NCv2, NCv3, of ND Series beginnen met een standaard waarde van nul kernen.

+ **Kern geheugens met een lage prioriteit per regio** hebben een standaard limiet van 100-3000, afhankelijk van het type abonnement. Het aantal kernen met lage prioriteit per abonnement kan worden verhoogd en is één waarde voor alle VM-families.

+ **Clusters per regio** hebben een standaard limiet van 200. Deze worden gedeeld tussen een trainings cluster en een reken instantie (die als een cluster met één knoop punt wordt beschouwd voor quotum doeleinden).

De volgende tabel bevat extra limieten die niet kunnen worden overschreden.

| **Resource** | **Maximumlimiet** |
| --- | --- |
| Werk ruimten per resource groep | 800 |
| Knoop punten in één Azure Machine Learning Compute-resource (AmlCompute) | 100 knoop punten |
| GPU MPI-processen per knoop punt | 1-4 |
| GPU-werk nemers per knoop punt | 1-4 |
| Taak levensduur | 21 dagen<sup>1</sup> |
| Levens duur van taken op een Low-Priority knoop punt | 7 dagen<sup>2</sup> |
| Parameter servers per knoop punt | 1 |

de maximale levens duur van <sup>1</sup> verwijst naar de duur tussen het moment waarop een uitvoering begint en eindigt. Voltooide uitvoeringen blijven voor onbepaalde tijd oneindig. Gegevens voor uitvoeringen die niet binnen de maximale levens duur zijn voltooid, zijn niet toegankelijk.
<sup>2</sup> taken op een Low-Priority knoop punt kunnen worden afgebroken wanneer er sprake is van een capaciteits beperking. U wordt aangeraden om in uw taak controle punten te implementeren.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning pijp lijnen
[Azure machine learning pijp lijnen](concept-ml-pipelines.md) hebben de volgende limieten.

| **Resource** | **Limiet** |
| --- | --- |
| Stappen in pijplijn | 30.000 |
| Werk ruimten per resource groep | 800 |

### <a name="container-instances"></a>Containerinstanties

Zie [container instances limieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)voor meer informatie.

### <a name="storage"></a>Storage
Azure Storage-accounts hebben een limiet van 250 opslag accounts per regio, per abonnement. Dit omvat zowel de accounts Standard als Premium Storage.

Als u de limiet wilt verhogen, kunt u een aanvraag indienen via [ondersteuning voor Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Het Azure Storage team controleert uw aanvraag en kan Maxi maal 250 opslag accounts voor een regio goed keuren.


## <a name="workspace-level-quota"></a>Quotum voor werkruimte niveau

Gebruik quota op werkruimte niveau om de toewijzing van Azure Machine Learning Compute-doelen te beheren tussen meerdere [werk ruimten](concept-workspace.md) in hetzelfde abonnement.

Standaard delen alle werk ruimten dezelfde quota als het quotum op abonnements niveau voor VM-families. U kunt echter een maximum quotum instellen voor afzonderlijke VM-families in werk ruimten in een abonnement. Dit biedt u de mogelijkheid capaciteit te delen en problemen met bron conflicten te voor komen:

1. Navigeer naar een werk ruimte in uw abonnement.
1. Selecteer in het linkerdeel venster de optie **gebruik en quota's**.
1. Selecteer het tabblad **Quota's configureren** om de quota's weer te geven.
1. Vouw een VM-serie uit.
1. Stel een quotum limiet in voor elke werk ruimte die wordt vermeld onder die VM-serie.

U kunt geen negatieve waarde of waarde instellen die hoger is dan het quotum van het abonnements niveau.

[![Quota voor Azure Machine Learning-werkruimte niveau](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> U hebt machtigingen op abonnements niveau nodig om quota in te stellen op het niveau van de werk ruimte.

## <a name="view-your-usage-and-quotas"></a>Uw gebruik en quota's weer geven

Als u uw quotum wilt weer geven voor verschillende Azure-resources, zoals Virtual Machines, opslag, netwerk, gebruikt u de Azure Portal:

1. Selecteer in het linkerdeel venster **alle services** en selecteer vervolgens **abonnementen** onder de categorie Algemeen.

2. Selecteer in de lijst met abonnementen het abonnement waarvoor u het quotum zoekt.

3. Selecteer **gebruik + quota's** om uw huidige quotum limieten en gebruik te bekijken. Gebruik de filters om de provider en locaties te selecteren. 

Het Azure Machine Learning Reken quotum voor uw abonnement wordt onafhankelijk van andere Azure-quota's beheerd. 

1. Navigeer naar uw **Azure machine learning** -werk ruimte in de Azure Portal.

2. Selecteer in het linkerdeel venster, onder de **sectie ondersteuning en probleem oplossing** , **gebruik en quota's** om uw huidige quotum limieten en het gebruik te bekijken.

3. Selecteer een abonnement om de quotum limieten weer te geven. Vergeet niet om te filteren op de regio waarin u bent geïnteresseerd.

4. U kunt scha kelen tussen een weer gave op het niveau van het abonnement en de weer gaven op werk ruimte niveau.

## <a name="request-quota-increases"></a>Verhogingen van quotum aanvragen

Als u de limiet of het quotum boven de standaard limiet wilt verhogen, opent u gratis [een online aanvraag voor klant ondersteuning](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) .

De limieten kunnen niet hoger zijn dan de maximum waarde die in de bovenstaande tabellen wordt weer gegeven. Als er geen maximum limiet is, kunt u de limiet voor de resource niet aanpassen.

Wanneer u een quota verhoging wilt aanvragen, selecteert u de service die u aanvraagt om het quotum te verhogen. Bijvoorbeeld Azure Machine Learning, Container Instances, opslag, enzovoort. Voor Azure Machine Learning compute, kunt u de knop voor het **quotum van aanvragen** selecteren tijdens het weer geven van het quotum volgens de bovenstaande stappen.

> [!NOTE]
> [Gratis proef abonnementen](https://azure.microsoft.com/offers/ms-azr-0044p) komen niet in aanmerking voor limiet of quotum verhoging. Als u een [gratis proef abonnement](https://azure.microsoft.com/offers/ms-azr-0044p)hebt, kunt u een upgrade uitvoeren naar een abonnement met [betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) . Zie [gratis proef versie van Azure upgraden naar betalen per gebruik](../billing/billing-upgrade-azure-subscription.md) en  [Veelgestelde vragen over het gratis proef abonnement](https://azure.microsoft.com/free/free-account-faq)voor meer informatie.

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Het persoonlijke eind punt en het privé-DNS-quotum nemen toe

Er gelden beperkingen voor het aantal privé-eind punten en privé-DNS-zones dat kan worden gemaakt in een abonnement.

Terwijl Azure Machine Learning resources maakt in uw abonnement (klant), zijn er enkele scenario's waarin u resources kunt maken in een micro soft-abonnement.

 In de volgende scenario's moet u mogelijk een quotum toelage aanvragen in het micro soft-abonnement:

* __Werk ruimte met persoonlijke koppeling ingeschakeld met een door de klant beheerde sleutel (CMK)__
* __Azure Container Registry voor de werk ruimte achter uw virtuele netwerk__
* __Een persoonlijke koppeling met Azure Kubernetes service-cluster koppelen aan uw werk ruimte__.

Als u een limiet wilt aanvragen voor deze scenario's, gebruikt u de volgende stappen:

1. [Maak een ondersteunings aanvraag voor Azure](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request) en selecteer de volgende opties in de sectie __basis beginselen__ :

    | Veld | Selectie |
    | ----- | ----- |
    | Type probleem | Technisch |
    | Service | Mijn services. Selecteer __machine learning__ in de vervolg keuzelijst. |
    | Probleem type | Werk ruimte instellen, SDK en CLI |
    | Subtype van probleem | Aanvraag voor het privé-eind punt en de Privé-DNS zone |

2. Gebruik in de sectie __Details__ het veld __Beschrijving__ om de Azure-regio op te geven die u wilt gebruiken en het scenario dat u wilt gebruiken. Als u quotum verhogingen moet aanvragen voor meerdere abonnementen, vermeldt u ook de abonnement-Id's in dit veld.

3. Selecteer __maken__ om de aanvraag te maken.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Scherm opname van een persoonlijk eind punt en een verzoek om een privé-DNS-quotum te verhogen":::

## <a name="next-steps"></a>Volgende stappen

+ [Kosten voor Azure Machine Learning plannen & beheren](concept-plan-manage-cost.md)
