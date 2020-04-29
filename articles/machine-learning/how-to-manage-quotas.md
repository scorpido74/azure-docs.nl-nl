---
title: "& quota's voor resources beheren"
titleSuffix: Azure Machine Learning
description: Meer informatie over de quota's voor Azure Machine Learning en hoe u meer quota kunt aanvragen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 03/05/2020
ms.openlocfilehash: 530647c3d32b62f0cac250795ccce580b182fa92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756600"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Quota's voor Azure-resources beheren en aanvragen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel vindt u informatie over vooraf geconfigureerde limieten voor Azure-resources voor uw abonnement. Ook vindt u hier instructies voor het aanvragen van quotum verbeteringen voor elk type resource. Deze limieten worden zodanig ingesteld dat budget overschrijdingen door fraude worden voor komen en om te voldoen aan de Azure-capaciteits beperkingen.

Net als bij andere Azure-Services gelden er limieten voor bepaalde resources die zijn gekoppeld aan Azure Machine Learning. Deze limieten variëren van een kapje op het aantal werk ruimten dat kan worden beperkt tot de werkelijke onderliggende Compute die wordt gebruikt voor model training of het afleiden/scoren. 

Houd bij het ontwerpen en schalen van uw Azure Machine Learning resources voor werk belastingen voor productie de volgende limieten. Als uw cluster bijvoorbeeld niet het doel aantal knoop punten bereikt, hebt u mogelijk een Azure Machine Learning limiet voor reken kernen bereikt voor uw abonnement. Als u de limiet of het quotum boven de standaard limiet wilt verhogen, kunt u gratis een online klant ondersteuning-aanvraag openen. De limieten kunnen niet hoger zijn dan de maximum waarde die in de volgende tabellen wordt weer gegeven vanwege Azure-capaciteits beperkingen. Als er geen kolom maximum limiet is, heeft de resource geen aanpas bare limieten.

## <a name="special-considerations"></a>Speciale overwegingen

+ Een quotum is een krediet limiet, geen capaciteits garantie. Neem contact op met de ondersteuning van Azure als u de capaciteits behoeften op grote schaal hebt.

+ Uw quotum wordt gedeeld door alle services in uw abonnementen, met inbegrip van Azure Machine Learning. De enige uitzonde ring is Azure Machine Learning Compute die een afzonderlijke quota van het core Compute-quotum heeft. Zorg ervoor dat u het quotum gebruik voor alle services berekent bij het evalueren van de capaciteits behoeften.

+ De standaard limieten variëren per aanbiedings categorie type, zoals een gratis proef versie, een betalen per gebruik-serie en een VM-reeks, zoals dv2, F, G, enzovoort.

## <a name="default-resource-quotas"></a>Standaard resource quota

Hier volgt een overzicht van de quotum limieten voor verschillende resource typen binnen uw Azure-abonnement.

> [!IMPORTANT]
> Limieten zijn onderhevig aan wijzigingen. De meest recente kan altijd worden gevonden op het quota [document](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/) op service niveau voor alle Azure.

### <a name="virtual-machines"></a>Virtuele machines
Voor elk Azure-abonnement geldt een limiet voor het aantal virtuele machines dat u voor uw services of zelfstandige kunt hebben. Deze limiet bevindt zich op het niveau van de totale kernen en ook op basis van een familie.

De kern geheugens van de virtuele machine hebben een regionale limiet en een limiet van regionale per grootte reeksen (dv2, F, etc.), die beide afzonderlijk worden afgedwongen. Neem bijvoorbeeld een abonnement met een limiet van 30 VM-cores voor US - oost, een limiet van 30 cores voor de A-serie en een limiet van 30 cores voor de D-serie. Met dit abonnement zouden 30 A1-VM’s of 30 D1-VM’s kunnen worden geïmplementeerd, of een combinatie van deze twee typen die het totaal van 30 cores niet overschrijdt (bijvoorbeeld 10 A1-VM’s en 20 D1-VM’s).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

Raadpleeg [het artikel over](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)Azure-quotum voor een gedetailleerde en actuele lijst met quotum limieten.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
Voor Azure Machine Learning Compute geldt een standaard quotum limiet op het aantal kernen en het aantal unieke reken resources dat per regio in een abonnement is toegestaan. Dit quotum is gescheiden van het bovenstaande VM-kern quotum en de kern limieten worden niet gedeeld tussen de twee resource typen, aangezien AmlCompute een beheerde service is die resources implementeert in een gehoste naam-of model.

Beschik bare resources:
+ Toegewezen kern geheugens per regio hebben een standaard limiet van 24-300, afhankelijk van het type abonnements aanbieding met hogere standaard waarden voor EA-en CSP-aanbiedings typen.  Het aantal toegewijde kernen per abonnement kan worden verhoogd en verschilt voor elke VM-serie. Bepaalde gespecialiseerde VM-families zoals NCv2, NCv3 of ND Series beginnen met een standaard waarde van nul kernen. Neem contact op met de ondersteuning van Azure door een quotum aanvraag voor het bespreken van toename opties te verhogen.

+ Kernen met een lage prioriteit per regio hebben een standaard limiet van 100-3000, afhankelijk van het type abonnements aanbieding met hogere standaard waarden voor EA-en CSP-aanbiedings typen. Het aantal kernen met lage prioriteit per abonnement kan worden verhoogd en is één waarde voor alle VM-families. Neem contact op met de ondersteuning van Azure om toename opties te bespreken.

+ Clusters per regio hebben een standaard limiet van 200. Deze worden gedeeld tussen een trainings cluster en een reken instantie (die als een cluster met één knoop punt wordt beschouwd voor quotum doeleinden). Neem contact op met de ondersteuning van Azure als u een verhoging wilt aanvragen die de limiet overschrijdt.

+ Er zijn andere strikte limieten die niet meer dan een keer kunnen worden overschreden.

| **Resource** | **Maximum limiet** |
| --- | --- |
| Maximum aantal werk ruimten per resource groep | 800 |
| Maximum aantal knoop punten in een enkele Azure Machine Learning Compute-resource (AmlCompute) | 100 knoop punten |
| Maximum aantal GPU-MPI processen per knoop punt | 1-4 |
| Maximum aantal GPU-werk nemers per knoop punt | 1-4 |
| Maximale levens duur van de taak | 90 dagen<sup>1</sup> |
| Maximale levens duur van de taak op een knoop punt met een lage prioriteit | 7 dagen<sup>2</sup> |
| Maximum aantal parameter servers per knoop punt | 1 |

<sup>1</sup> de maximale levens duur verwijst naar het tijdstip waarop een uitvoering wordt gestart en wanneer deze is voltooid. Voltooide uitvoeringen blijven voor onbepaalde tijd oneindig; gegevens voor uitvoeringen die niet binnen de maximale levens duur zijn voltooid, zijn niet toegankelijk.
<sup>2</sup> taken op een knoop punt met een lage prioriteit kunnen worden afgebroken op het moment dat er een capaciteits beperking is. U wordt aangeraden om in uw taak controle punten te implementeren.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning pijp lijnen
Voor Azure Machine Learning pijp lijnen geldt een quotum limiet voor het aantal stappen in een pijp lijn en op basis van het aantal op schema's gebaseerde uitvoeringen van gepubliceerde pijp lijnen per regio in een abonnement.
- Het maximum aantal stappen dat is toegestaan in een pijp lijn is 30.000
- Maximum aantal uitgevoerde uitvoeringen op basis van een schema en BLOB-Pulls voor door een blog geactiveerde planning van gepubliceerde pijp lijnen per abonnement per maand is 100.000

> [!NOTE]
> Als u deze limiet wilt verhogen, neemt u contact op met [Microsoft ondersteuning](https://azure.microsoft.com/support/options/).

### <a name="container-instances"></a>Containerinstanties

Er is ook een limiet voor het aantal container instanties dat u binnen een bepaalde tijds periode kunt opwaarderen (bereik per uur) of binnen uw hele abonnement.

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

Raadpleeg [het artikel over](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)Azure-quotum voor een gedetailleerde en actuele lijst met quotum limieten.

### <a name="storage"></a>Storage
Er geldt een limiet voor het aantal opslag accounts per regio, ook in een bepaald abonnement. De standaard limiet is 250 en bevat zowel de accounts Standard als Premium Storage. Als u meer dan 250 opslag accounts in een bepaalde regio nodig hebt, moet u een aanvraag indienen via [ondersteuning voor Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Het Azure Storage team gaat uw zakelijke Case controleren en kan Maxi maal 250 opslag accounts voor een bepaalde regio goed keuren.


## <a name="workspace-level-quota"></a>Quotum voor werkruimte niveau

Voor een beter beheer van resource toewijzingen voor Amlcompute tussen verschillende werk ruimten, hebben we een functie geïntroduceerd waarmee quota's op abonnements niveau (per VM-serie) kunnen worden gedistribueerd en geconfigureerd op het niveau van de werk ruimte. Het standaard gedrag is dat alle werk ruimten dezelfde quota hebben als het quotum van het abonnements niveau voor een VM-serie. Naarmate het aantal werk ruimten toeneemt en werk belastingen met een verschillende prioriteit het delen van dezelfde bronnen willen starten, hebben gebruikers echter een manier om de capaciteit te delen en bronnen conflicten te voor komen. Azure Machine Learning biedt een oplossing met de beheerde Compute-aanbieding door gebruikers toe te staan een maximum quotum voor een bepaalde VM-serie in elke werk ruimte in te stellen. Dit is vergelijkbaar met het distribueren van uw capaciteit tussen werk ruimten en de gebruikers kunnen ervoor kiezen om het maximum gebruik van het apparaat te verg Roten. 

Als u quota's op het niveau van de werk ruimte wilt instellen, gaat u naar een werk ruimte in uw abonnement en klikt u op **gebruik en quota's** in het linkerdeel venster. Selecteer vervolgens het tabblad **Quota's configureren** om de quota's weer te geven, een VM-serie uit te breiden en een quotum limiet in te stellen voor elke werk ruimte die wordt vermeld onder die VM-serie. Houd er rekening mee dat u geen negatieve waarde of een hogere waarde dan het quotum op abonnements niveau kunt instellen. Net zoals u zou zien, wordt aan alle werk ruimten standaard het volledige abonnements quotum toegewezen, zodat het volledige gebruik van het toegewezen quotum is toegestaan.

[![Quota voor Azure Machine Learning-werkruimte niveau](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> Dit is alleen een Enter prise Edition-functie. Als u in uw abonnement zowel een basis-als een Enter prise Edition-werk ruimte hebt, kunt u deze gebruiken om alleen quota's in te stellen voor uw werk ruimten in uw onderneming. In uw basis werkruimte blijven de quota niveau quotum, de standaard instelling.
>
> U hebt machtigingen op abonnements niveau nodig om quota in te stellen op het niveau van de werk ruimte. Dit wordt afgedwongen zodat afzonderlijke eigen aars van werk ruimten hun quota niet kunnen bewerken of verhogen en encroaching kunnen starten op resources die zijn gereserveerd voor een andere werk ruimte. Een abonnements beheerder is daarom het meest geschikt voor het toewijzen en distribueren van deze quota's in werk ruimten.



## <a name="view-your-usage-and-quotas"></a>Uw gebruik en quota's weer geven

Het weer geven van uw quotum voor verschillende bronnen, zoals Virtual Machines, opslag, netwerk, is eenvoudig via de Azure Portal.

1. Selecteer in het linkerdeel venster **alle services** en selecteer vervolgens **abonnementen** onder de categorie Algemeen.

1. Selecteer in de lijst met abonnementen het abonnement waarvoor u het quotum zoekt.

   **Er is een voor behoud**, specifiek voor het weer geven van de Azure machine learning reken quota. Zoals hierboven vermeld, wordt het quotum gescheiden van het reken quotum van uw abonnement.

1. Selecteer **machine learning service** in het linkerdeel venster en selecteer vervolgens een werk ruimte in de lijst die wordt weer gegeven

1. Selecteer op de volgende Blade onder de **sectie ondersteuning en probleem oplossing** de optie **gebruik en quota's** om uw huidige quotum limieten en het gebruik te bekijken.

1. Selecteer een abonnement om de quotum limieten weer te geven. Vergeet niet om te filteren op de regio waarin u bent geïnteresseerd.

1. U kunt nu scha kelen tussen een weer gave op het niveau van het abonnement en een weer gave op werk ruimte niveau:
    + **Abonnementweergave:** Op deze manier kunt u het gebruik van kern quota per VM-serie weer geven, uitbreiden door de werk ruimte en het uitbreiden door de daad werkelijke cluster namen. Deze weer gave is optimaal voor het snel ophalen van de details van het kern gebruik voor een bepaalde VM-familie om het opsplitsen van werk ruimten en de onderliggende clusters voor elk van deze werk ruimten te bekijken. De algemene conventie in deze weer gave is (gebruik/quotum), waarbij het gebruik het huidige aantal geschaalde kernen is en het quotum het logische maximum aantal kernen is waarmee de resource kan worden geschaald. Voor elke **werk ruimte**zou het quotum de quota voor het niveau van de werk ruimte zijn (zoals hierboven beschreven). Hiermee wordt het maximum aantal kernen aangegeven dat kan worden geschaald naar een bepaalde VM-serie. Voor een **cluster** op dezelfde manier is het quotum feitelijk de kernen die overeenkomen met het maximum aantal knoop punten dat door het cluster kan worden geschaald, zoals gedefinieerd door de eigenschap max_nodes.

    + **Werkruimte weergave:** Zo kunt u het gebruik van kern quota per werk ruimte weer geven, deze uitbreiden door de VM-serie en het uitbreiden door de daad werkelijke cluster namen. Deze weer gave is optimaal voor het snel ophalen van de details van het kern gebruik voor een bepaalde werk ruimte om de opsplitsing van VM-families en de onderliggende clusters voor elk van deze families te bekijken.

## <a name="request-quota-increases"></a>Verhogingen van quotum aanvragen

Als u de limiet of het quotum boven de standaard limiet wilt verhogen, kunt u gratis [een online klant ondersteuning-aanvraag openen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) .

De limieten kunnen niet hoger zijn dan de maximum waarde die in de tabellen wordt weer gegeven. Als er geen maximum limiet is, heeft de resource geen aanpas bare limieten. In [Dit](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) artikel wordt het quotum verhogings proces uitgebreid beschreven.

Wanneer u een quota verhoging wilt aanvragen, moet u de service selecteren die u aanvraagt om het quotum te verhogen. Dit kan services zijn zoals Machine Learning service quotum, container instanties of opslag quotum. Naast Azure Machine Learning compute, kunt u op de knop **aanvraag quotum** klikken terwijl u het quotum bekijkt volgens de bovenstaande stappen.

> [!NOTE]
> [Gratis proef abonnementen](https://azure.microsoft.com/offers/ms-azr-0044p) komen niet in aanmerking voor limiet of quotum verhoging. Als u een [gratis proef abonnement](https://azure.microsoft.com/offers/ms-azr-0044p)hebt, kunt u een upgrade uitvoeren naar een abonnement met [betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) . Zie [gratis proef versie van Azure upgraden naar betalen per gebruik](../billing/billing-upgrade-azure-subscription.md) en [Veelgestelde vragen over het gratis proef abonnement](https://azure.microsoft.com/free/free-account-faq)voor meer informatie.
