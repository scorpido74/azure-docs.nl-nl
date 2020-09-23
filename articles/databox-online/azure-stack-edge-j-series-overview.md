---
title: Overzicht van Microsoft Azure Stack Edge Pro R | Microsoft Docs
description: Hierin worden Microsoft Azure Stack Edge Pro R-apparaten beschreven, een opslagoplossing voor militaire toepassingen die gebruikmaakt van een fysiek apparaat voor netwerkoverdracht naar Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 265bc1bb86c7fe8424656aa9adb30ddbe847e6fc
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985655"
---
# <a name="what-is-the-azure-stack-edge-pro-r"></a>Wat is de Azure Stack Edge Pro R?

De Azure Stack Edge Pro R is een hardware-as-a-service-oplossing. Microsoft levert u een duurzaam en robuust edge-apparaat van serverklasse voor gegevensoverdracht via het netwerk naar Azure. Deze apparaten zijn uitgerust met een grafische verwerkingseenheid (GPU) en zijn geoptimaliseerd voor AI, analyse en serverloze computing. De robuuste apparaten zijn geschikt voor gebruik in de zwaarste omstandigheden.

Dit artikel biedt een overzicht van de Azure Stack Edge Pro R-oplossing, belangrijkste mogelijkheden en de scenario’s waarin u dit apparaat kunt implementeren.


## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Azure Stack Edge Pro R biedt de volgende mogelijkheden:

|Mogelijkheid |Beschrijving  |
|---------|---------|
|Robuuste hardware| Robuuste hardware van serverklasse, ontworpen voor zware omstandigheden. Apparaat is draagbaar in een draagkoffer voor twee personen. |
|Beheerd via de cloud     |Het apparaat en de service worden beheerd via de Azure-portal.|
|Workloads voor Edge-computing   |Gegevens kunnen worden geanalyseerd, verwerkt of gefilterd. Ondersteunt VM's en workloads in containers.|
|Versnelde AI-deductie| Mogelijk gemaakt door een Nvidia T4 GPU.|
|Hoge prestaties | High Performance compute en gegevensoverdracht.|
|Toegang tot gegevens     | Rechtstreekse gegevenstoegang vanuit Azure Storage Blobs en Azure Files met behulp van cloud-API’s voor aanvullende gegevensverwerking in de cloud. Lokale cache op het apparaat wordt gebruikt voor snelle toegang tot laatst gebruikte bestanden.|
|Niet-verbonden modus| Het apparaat en de service kunnen desgewenst worden beheerd via Azure Stack Hub. Toepassingen implementeren, uitvoeren en beheren in de offline modus. <br> Modus zonder verbinding ondersteunt scenario’s voor offline uploaden.|
|Ondersteunde protocollen     |Ondersteuning voor de standaardprotocollen SMB, NFS en REST voor gegevensopname. <br> Ga naar [Systeemvereisten voor Azure Stack Edge Pro R](azure-stack-edge-gpu-system-requirements.md) voor meer informatie over ondersteunde versies.|
|Gegevensvernieuwing     | Mogelijkheid om lokale bestanden te vernieuwen met de meest recente uit de cloud.|
|Dubbele versleuteling    | Het gebruik van zichzelf versleutelende schijven biedt de eerste versleutelingslaag. VPN biedt de tweede versleutelingslaag. BitLocker-ondersteuning om gegevens lokaal te versleutelen en de gegevensoverdracht naar de cloud via *https* te beveiligen.|
|Bandbreedtebeperking| Beperking om het bandbreedtegebruik tijdens piekuren te verminderen.|

<!--|Scale out file server| Available as 1-node and 4-node cluster configurations|-->

## <a name="use-cases"></a>Gebruiksvoorbeelden

Hier volgen de verschillende scenario's waarbij Azure Stack Edge Pro R kan worden gebruikt voor snelle Machine Learning-deductie aan de rand en het voorbewerken van gegevens voordat deze naar Azure worden verzonden.

- **Deductie met Azure Machine Learning**: met Azure Stack Edge Pro R kunt u ML-modellen uitvoeren om snel resultaten te verkrijgen op basis waarvan actie kan worden ondernomen voordat de gegevens naar de cloud worden verzonden. De volledige gegevensset kan desgewenst worden overgedragen om uw ML-modellen te blijven trainen en verbeteren. Zie [Hardware-versnelde Azure ML-modellen implementeren op Azure Stack Edge Pro R](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server) voor meer informatie over het gebruiken van de met hardware versnelde Azure ML-modellen op het Azure Stack Edge Pro R-apparaat.

- **Gegevens voorbewerken** gegevens transformeren voordat ze naar Azure worden verzonden om een bruikbaardere gegevensset te creëren. Voorverwerking kan worden gebruikt om:

    - Gegevens samen te voegen.
    - Gegevens te wijzigen, bijvoorbeeld om persoonlijke gegevens te verwijderen.
    - Een subset van gegevens maken voor het optimaliseren van opslag en bandbreedte, of voor verdere analyse.
    - IoT-gebeurtenissen te analyseren en erop te reageren.

- **Gegevens via een netwerk naar Azure overdragen**: gebruik Azure Stack Edge Pro R om gegevens gemakkelijk en snel naar Azure over te dragen voor verdere berekeningen en analyses of voor archivering.

## <a name="components"></a>Onderdelen

De Azure Stack Edge Pro R-oplossing bestaat uit een Azure Stack Edge-resource, een robuust, fysiek Azure Stack Edge Pro R-apparaat en een lokale webinterface.

- **Fysiek Azure Stack Edge Pro R-apparaat**: de Azure Stack Edge Pro R is een apparaat met één knooppunt dat kan worden geconfigureerd om gegevens naar Azure te verzenden. Het apparaat is een 1U-server met een robuuste kast en wordt geleverd door Microsoft. De server is optioneel beschikbaar met een UPS (ook 1U).

    ![Het Azure Stack Edge Pro R-apparaat met één knooppunt](media/azure-stack-edge-j-series-overview/device-image-1.png)

- **Azure Stack Edge-resource**: een resource in Azure Portal waarmee u een robuust Azure Stack Edge Pro R-apparaat kunt beheren via een webinterface waartoe u toegang hebt vanaf verschillende geografische locaties. Gebruik de Azure Stack Edge-resource om resources te maken en beheren, apparaten en waarschuwingen te bekijken en beheren, en shares te beheren.  

- **Lokale Azure Stack Edge Pro R-webinterface**: gebruik de lokale webinterface voor initiële apparaatconfiguratie, om diagnoses uit te voeren, het Azure Stack Edge Pro R-apparaat uit te schakelen of opnieuw op te starten, logboeken met kopieerbewerkingen te bekijken en contact op te nemen met Microsoft Ondersteuning om een serviceaanvraag in te dienen.


## <a name="region-availability"></a>Beschikbaarheid in regio’s

Het fysieke Azure Stack Edge Pro R-apparaat, de Azure-resource en het doelopslagaccount waarnaar u gegevens overdraagt hoeven zich niet allemaal in dezelfde regio te bevinden.

- **Beschikbaarheid van resources**: ga voor een lijst van alle regio's waarin de Azure Stack Edge-resource beschikbaar is naar [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Doelopslagaccounts**: De opslagaccounts waarin de gegevens worden opgeslagen, zijn beschikbaar in alle Azure-regio’s. De regio's waarin de opslagaccounts Azure Stack Edge Pro R -gegevens opslaan, moeten zich voor optimale prestaties dicht bij het apparaat bevinden. Een opslagaccount dat zich ver van het apparaat vandaan bevindt, resulteert in lange latenties en tragere prestaties.

## <a name="next-steps"></a>Volgende stappen

- De [Systeemvereisten voor Azure Stack Edge Pro R ](azure-stack-edge-gpu-system-requirements.md) lezen.
<!--- Understand the [Azure Stack Edge Pro R limits](azure-stack-edge-limits.md).-->

