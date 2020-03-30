---
title: Resources & quota beheren
titleSuffix: Azure Machine Learning
description: Meer informatie over de quota voor resources voor Azure Machine Learning en hoe u meer quota aanvragen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 03/05/2020
ms.openlocfilehash: 17a4652604c0faa804d24530869aac0848c972b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399152"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Quota voor Azure-resources beheren en aanvragen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel vindt u informatie over vooraf geconfigureerde limieten voor Azure-bronnen voor uw abonnement. Ook zijn instructies opgenomen over het aanvragen van quotaverbeteringen voor elk type resource. Deze limieten worden ingevoerd om te voorkomen dat budgetoverschrijdingen als gevolg van fraude worden uitgevoerd en om azure-capaciteitsbeperkingen te eren.

Net als bij andere Azure-services zijn er beperkingen voor bepaalde resources die zijn gekoppeld aan Azure Machine Learning. Deze limieten variëren van een limiet op het aantal werkruimten tot beperkingen op de werkelijke onderliggende rekenkracht die wordt gebruikt voor modeltraining of gevolgtrekking/score. 

Houd bij het ontwerpen en schalen van uw Azure Machine Learning-resources voor productieworkloads rekening met deze limieten. Als uw cluster bijvoorbeeld het doelaantal knooppunten niet bereikt, hebt u mogelijk een limiet voor Azure Machine Learning Compute-kernen voor uw abonnement bereikt. Als u de limiet of het quotum boven de standaardlimiet wilt verhogen, opent u kosteloos een online verzoek voor klantenondersteuning. De limieten kunnen niet hoger worden dan de maximumlimietwaarde die in de volgende tabellen wordt weergegeven vanwege azure-capaciteitsbeperkingen. Als er geen kolom Maximumlimiet is, heeft de resource geen aanpasbare limieten.

## <a name="special-considerations"></a>Bijzondere overwegingen

+ Een quotum is een kredietlimiet, geen capaciteitsgarantie. Als u op grote schaal capaciteitsbehoeften hebt, neemt u contact op met Azure-ondersteuning.

+ Uw quotum wordt gedeeld voor alle services in uw abonnementen, waaronder Azure Machine Learning. De enige uitzondering is Azure Machine Learning compute, dat een apart quotum heeft van het kerngegevensquotum. Zorg ervoor dat u het quotumgebruik voor alle services berekent bij het evalueren van uw capaciteitsbehoeften.

+ Standaardlimieten variëren per aanbiedingscategorietype, zoals Gratis proefversie, Betalen per gebruik en VM-serie, zoals Dv2, F, G, enzovoort.

## <a name="default-resource-quotas"></a>Standaardresourcequota

Hier ziet u een overzicht van de quotumlimieten door verschillende resourcetypen binnen uw Azure-abonnement.

> [!IMPORTANT]
> Limieten kunnen worden gewijzigd. De laatste is altijd te vinden in het quotumdocument op [serviceniveau](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) voor alle Azure.The latest can always be found at the service-level quota document for all of Azure.

### <a name="virtual-machines"></a>Virtuele machines
Voor elk Azure-abonnement is er een limiet voor het aantal virtuele machines dat u hebben voor uw services of zelfstandige services. Deze limiet ligt op regioniveau, zowel op de totale kernen als per gezin.

Virtuele machinekernen hebben een regionale totale limiet en een regionale per size-serie (Dv2, F, enz.) limiet, die beide afzonderlijk worden afgedwongen. Neem bijvoorbeeld een abonnement met een limiet van 30 VM-cores voor US - oost, een limiet van 30 cores voor de A-serie en een limiet van 30 cores voor de D-serie. Met dit abonnement zouden 30 A1-VM’s of 30 D1-VM’s kunnen worden geïmplementeerd, of een combinatie van deze twee typen die het totaal van 30 cores niet overschrijdt (bijvoorbeeld 10 A1-VM’s en 20 D1-VM’s).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

Voor een meer gedetailleerde en up-to-date lijst met quotalimieten raadpleegt u [hier](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)het azure-brede quotumartikel .

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
Voor Azure Machine Learning Compute geldt een standaardquotumlimiet voor zowel het aantal cores als het aantal unieke rekenresources dat per regio in een abonnement is toegestaan. Dit quotum staat los van het bovenstaande VM-kernquotum en de kernlimieten worden niet gedeeld tussen de twee resourcetypen, omdat AmlCompute een beheerde service is die resources implementeert in een gehost-op-naam-van-model.

Beschikbare middelen:
+ Speciale cores per regio hebben een standaardlimiet van 24 - 300, afhankelijk van het type abonnementsaanbieding met hogere standaardinstellingen voor EA- en CSP-aanbiedingstypen.  Het aantal speciale cores per abonnement kan worden verhoogd en verschilt per VM-familie. Bepaalde gespecialiseerde VM-families zoals NCv2, NCv3 of ND-serie beginnen met een standaard van nulcores. Neem contact op met Azure-ondersteuning door een quotumaanvraag te verhogen om de opties voor het vergroten te bespreken.

+ Kernen met lage prioriteit per regio hebben een standaardlimiet van 100 - 3000, afhankelijk van het type abonnementsaanbieding met hogere standaardwaarden voor EA- en CSP-aanbiedingstypen. Het aantal kernen met lage prioriteit per abonnement kan worden verhoogd en is een enkele waarde voor VM-families. Neem contact op met Azure-ondersteuning om de opties voor het vergroten te bespreken.

+ Clusters per regio hebben een standaardlimiet van 200. Deze worden gedeeld tussen een trainingscluster en een rekeninstantie (die wordt beschouwd als één knooppuntcluster voor quotadoeleinden). Neem contact op met Azure-ondersteuning als u een verhoging wilt aanvragen die verder gaat dan deze limiet.

+ Er zijn ** andere strikte grenzen die niet kunnen worden overschreden eenmaal geraakt.

| **Resource** | **Maximumlimiet** |
| --- | --- |
| Maximale werkruimten per resourcegroep | 800 |
| Maximale knooppunten in één Azure Machine Learning Compute (AmlCompute)-bron | 100 knooppunten |
| Maximale GPU MPI-processen per knooppunt | 1-4 |
| Maximale GPU-werknemers per knooppunt | 1-4 |
| Maximale levensduur van de baan | 90 dagen<sup>1</sup> |
| Maximale levensduur van een taak op een knooppunt met lage prioriteit | 7 dagen<sup>2</sup> |
| Maximale parameterservers per knooppunt | 1 |

<sup>1</sup> De maximale levensduur verwijst naar de tijd dat een run start en wanneer deze is voltooid. Voltooide runs blijven voor onbepaalde tijd bestaan; gegevens voor uitvoeringen die niet binnen de maximale levensduur zijn voltooid, zijn niet toegankelijk.
<sup>2</sup> Taken op een knooppunt met lage prioriteit kunnen worden verlopen wanneer er een capaciteitsbeperking is. We raden je aan checkpointing in je werk te implementeren.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning-pijplijnen
Voor Azure Machine Learning Pipelines geldt een quotumlimiet voor het aantal stappen in een pijplijn en voor het aantal op planning gebaseerde uitvoeringen van gepubliceerde pijplijnen per regio in een abonnement.
- Maximaal aantal stappen toegestaan in een pijplijn is 30.000
- Het maximum aantal op schema's gebaseerde op schema's gebaseerde uitvoeringen en blob-pulls voor door een blog geactiveerde schema's van gepubliceerde pijplijnen per abonnement per maand is 100.000

> [!NOTE]
> Als u deze limiet wilt verhogen, neemt u contact op met [Microsoft Support](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Containerinstanties

Er is ook een limiet aan het aantal containerexemplaren dat u in een bepaalde periode opdraaien (per uur ingesteld) of over uw hele abonnement.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

Voor een meer gedetailleerde en up-to-date lijst met quotalimieten raadpleegt u [hier](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)het azure-brede quotumartikel .

### <a name="storage"></a>Storage
Er is een limiet op het aantal opslagaccounts per regio en in een bepaald abonnement. De standaardlimiet is 250 en omvat zowel standaard- als Premium-opslagaccounts. Als u meer dan 250 opslagaccounts in een bepaalde regio nodig hebt, dient u een aanvraag in via [Azure Support.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) Het Azure Storage-team bekijkt uw bedrijfscase en keurt mogelijk maximaal 250 opslagaccounts voor een bepaalde regio goed.


## <a name="workspace-level-quota"></a>Quotum op werkniveau

Om resources allocatie voor Amlcompute beter te beheren tussen verschillende werkruimten, hebben we een functie geïntroduceerd waarmee u quota op abonnementsniveau (per VM-familie) distribueren en configureren op werkruimteniveau. Het standaardgedrag is dat alle werkruimten hetzelfde quotum hebben als het quotum op abonnementsniveau voor elke VM-familie. Naarmate het aantal werkruimten echter toeneemt en workloads met verschillende prioriteit dezelfde resources gaan delen, willen gebruikers een manier om de capaciteit beter te delen en problemen met resourceconflicten te voorkomen. Azure Machine Learning biedt een oplossing met zijn beheerde compute-aanbod door gebruikers in staat te stellen een maximaal quotum in te stellen voor een bepaalde VM-familie op elke werkruimte. Dit is analoog aan het verdelen van uw capaciteit tussen werkruimten, en de gebruikers kunnen ervoor kiezen om ook over-toewijzen om maximale gebruik rijden. 

Als u quota wilt instellen op werkruimteniveau, gaat u naar elke werkruimte in uw abonnement en klikt u op **Gebruik + quota** in het linkerdeelvenster. Selecteer vervolgens het tabblad **Quota configureren** om de quota weer te geven, vouw een VM-familie uit en stel een quotumlimiet in voor elke werkruimte die onder die VM-familie wordt vermeld. Houd er rekening mee dat u geen negatieve waarde of een waarde instellen die hoger is dan het quotum op abonnementsniveau. Zoals u zou opmerken, krijgen standaard alle werkruimten het volledige abonnementsquotum toegewezen om het toegewezen quotum volledig te kunnen gebruiken.

[![Quotum voor Azure Machine Learning-werkruimteniveau](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Dit is alleen een Functie voor de Enterprise-editie. Als u zowel een werkruimte voor basic- als een Enterprise-editie in uw abonnement hebt, u deze gebruiken om alleen quota in te stellen voor uw Enterprise-werkruimten. Uw Basiswerkruimten blijven het quotum op abonnementsniveau hebben, het standaardgedrag.
>
> U hebt machtigingen op abonnementsniveau nodig om quota in te stellen op werkruimteniveau. Dit wordt afgedwongen zodat eigenaren van afzonderlijke werkruimtes hun quota niet bewerken of verhogen en beginnen inbreuk te maken op resources die zijn gereserveerd voor een andere werkruimte. Een abonnementsbeheerder is dus het meest geschikt om deze quota toe te wijzen en te distribueren over werkruimten.



## <a name="view-your-usage-and-quotas"></a>Uw gebruik en quota weergeven

Het bekijken van uw quotum voor verschillende bronnen, zoals virtuele machines, opslag, netwerk, is eenvoudig via de Azure-portal.

1. Selecteer in het linkerdeelvenster **Alle services** en selecteer **Vervolgens Abonnementen** onder de categorie Algemeen.

1. Selecteer in de lijst met abonnementen het abonnement waarvan u het quotum zoekt.

   **Er is een voorbehoud,** specifiek voor het bekijken van het Azure Machine Learning Compute-quotum. Zoals hierboven vermeld, staat dit quotum los van het rekenquotum voor uw abonnement.

1. Selecteer in het linkerdeelvenster **machine learning-service** en selecteer vervolgens een werkruimte in de weergegeven lijst

1. Selecteer op het volgende blad onder de **sectie Ondersteuning + probleemoplossing** de optie **Gebruik + quota** om uw huidige quotumlimieten en -gebruik weer te geven.

1. Selecteer een abonnement om de quotumlimieten weer te geven. Vergeet niet om te filteren op de regio waarin u geïnteresseerd bent.

1. U nu schakelen tussen een weergave op abonnementsniveau en een weergave op werkruimteniveau:
    + **Abonnementsweergave:** Hiermee u uw gebruik van kernquotum per VM-familie bekijken, uitbreiden per werkruimte en verder uitbreiden met de werkelijke clusternamen. Deze weergave is optimaal om snel in te gaan op de details van het kerngebruik voor een bepaalde VM-familie om de break-up per werkruimte en verder door de onderliggende clusters voor elk van deze werkruimten te zien. De algemene conventie in deze weergave is (gebruik/quotum), waarbij het gebruik het huidige aantal opgeschaalde kernen is en quota het logische maximum aantal kernen is waarop de resource kan schalen. Voor elke **werkruimte**is het quotum het quotum op werkniveau (zoals hierboven uitgelegd) dat het maximum aantal kernen aangeeft waarop u schalen voor een bepaalde VM-familie. Voor een **cluster** op dezelfde manier is het quotum in feite de kernen die overeenkomen met het maximum aantal knooppunten dat het cluster kan schalen om te definiëren door de eigenschap max_nodes.

    + **Werkruimteweergave:** Hiermee u uw gebruik van kernquotum per werkruimte bekijken, uitbreiden per VM-familie en verder uitbreiden met de werkelijke clusternamen. Deze weergave is optimaal om snel in te gaan op de details van het kerngebruik voor een bepaalde werkruimte om de break-up door VM-families en verder door de onderliggende clusters voor elk van deze families te zien.

## <a name="request-quota-increases"></a>Verhogingen van quotum aanvragen

Als u de limiet of het quotum boven de standaardlimiet wilt verhogen, opent u kosteloos [een online verzoek om klantenondersteuning.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)

De limieten kunnen niet hoger worden dan de maximumwaarde in de tabellen. Als er geen maximumlimiet is, heeft de resource geen aanpasbare limieten. [Dit](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artikel behandelt het proces van de quotaverhoging in meer detail.

Wanneer u een quotumverhoging aanvraagt, moet u de service selecteren waarmee u het quotum wilt verhogen, wat services kunnen zijn zoals machine learning-servicequota, containerinstanties of opslagquotum. Bovendien u voor Azure Machine Learning Compute op de knop **Quota aanvragen** klikken terwijl u het quotum bekijkt dat de bovenstaande stappen volgt.

> [!NOTE]
> [Gratis proefabonnementen](https://azure.microsoft.com/offers/ms-azr-0044p) komen niet in aanmerking voor limiet- of quotaverhogingen. Als u een [gratis proefabonnement hebt,](https://azure.microsoft.com/offers/ms-azr-0044p)u upgraden naar een [betalen per gebruik-abonnement.](https://azure.microsoft.com/offers/ms-azr-0003p/) Zie Veelgestelde [vragen](../billing/billing-upgrade-azure-subscription.md) over azure gratis proefversie upgraden naar veelgestelde vragen over een abonnement voor betalen per gebruik en gratis [proefabonnementen](https://azure.microsoft.com/free/free-account-faq).
