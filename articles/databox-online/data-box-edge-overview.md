---
title: Overzicht van Microsoft Azure Stack Edge | Microsoft Docs
description: Beschrijft Azure Stack Edge, een opslag oplossing die gebruikmaakt van een fysiek apparaat voor overdracht op basis van een netwerk naar Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: 654ca34ae4c0e393920965083567e3d9c71148d0
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863628"
---
# <a name="what-is-azure-stack-edge"></a>Wat is Azure Stack Edge? 

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Azure Stack Edge is een rand computer apparaat met AI-functionaliteit met mogelijkheden voor netwerk gegevens overdracht. In dit artikel vindt u een overzicht van de Azure Stack EDGE-oplossing, de voor delen, de belangrijkste mogelijkheden en de scenario's waarin u dit apparaat kunt implementeren. 

Azure Stack Edge is een hardware-as-a-service-oplossing. Micro soft stuurt u een door een Cloud beheerd apparaat met een ingebouwd veld Programmeer bare Gate-matrix (FPGA) waarmee de AI-interferentie wordt versneld en alle mogelijkheden van een gateway voor netwerk opslag zijn. 

## <a name="use-cases"></a>Use cases

Hier volgen de verschillende scenario's waarbij Azure Stack Edge kan worden gebruikt voor snelle Machine Learning (ML) bij de rand en voor verwerking van gegevens voordat deze naar Azure worden verzonden.

- Demijnen **met Azure machine learning** -met Azure stack Edge kunt u ml-modellen uitvoeren om snelle resultaten te krijgen waarmee kan worden gereageerd voordat de gegevens naar de cloud worden verzonden. De volledige gegevensset kan eventueel worden overgedragen om uw ML-modellen verder te trainen en te verbeteren. Zie voor meer informatie over het gebruik van de met Azure ML versnelde modellen op het Azure Stack edge-apparaat [Azure ml-versnelde modellen implementeren op Azure stack Edge](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Gegevens vooraf verwerken** : transformatie gegevens voordat deze naar Azure worden verzonden om een meer actie bare gegevensset te maken. Voorverwerking kan worden gebruikt om: 

    - Gegevens samen te voegen.
    - Wijzig gegevens, bijvoorbeeld om persoonlijke gegevens te verwijderen.
    - Subset van gegevens voor het optimaliseren van opslag en band breedte, of voor verdere analyse.
    - IoT-gebeurtenissen te analyseren en erop te reageren. 

- **Gegevens overdragen via het netwerk naar Azure** : gebruik Azure stack Edge om snel en eenvoudig gegevens over te dragen naar Azure om verdere Compute en analyses mogelijk te maken of voor archiverings doeleinden. 


## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Azure Stack Edge heeft de volgende mogelijkheden:

|Mogelijkheid |Beschrijving  |
|---------|---------|
|Versneld AI-interferentie| Ingeschakeld door de ingebouwde FPGA.|
|Berekenen       |Gegevens kunnen worden geanalyseerd, verwerkt of gefilterd.|
|Hoge prestaties | High Performance Compute en gegevens overdracht.|
|Gegevenstoegang     | Rechtstreekse gegevenstoegang vanuit Azure Storage Blobs en Azure Files met behulp van cloud-API’s voor aanvullende gegevensverwerking in de cloud. Lokale cache op het apparaat wordt gebruikt voor snelle toegang tot de meest recent gebruikte bestanden.|
|Door de Cloud beheerde     |Het apparaat en de service worden beheerd via de Azure Portal.  |
|Offline upload     | Modus zonder verbinding ondersteunt scenario’s voor offline uploaden.|
|Ondersteunde protocollen     | Ondersteuning voor het standaard SMB- en NFS-protocol voor gegevensopname. <br> Ga naar [Azure stack Edge-systeem vereisten](data-box-edge-system-requirements.md)voor meer informatie over ondersteunde versies.|
|Gegevensvernieuwing     | Mogelijkheid om lokale bestanden te vernieuwen met de meest recente uit de cloud.|
|Versleuteling    | BitLocker-ondersteuning om gegevens lokaal te versleutelen en de gegevensoverdracht naar de cloud via *HTTPS* te beveiligen.|
|Bandbreedte regeling| Beperking voor het beperken van het bandbreedte gebruik tijdens piek uren.|


## <a name="components"></a>Onderdelen

De Azure Stack EDGE-oplossing bestaat uit Azure Stack Edge-resource, Azure Stack Edge-fysiek apparaat en een lokale webgebruikersinterface.

* **Azure stack rand fysiek apparaat** : een door micro soft geleverde 1U-server die kan worden geconfigureerd om gegevens naar Azure te verzenden. 
    
* **Azure stack-rand resource** : een resource in de Azure Portal waarmee u een Azure stack edge-apparaat kunt beheren vanuit een webinterface die u vanaf verschillende geografische locaties kunt openen. Gebruik de Azure Stack Edge-resource om resources te maken en te beheren, apparaten en waarschuwingen weer te geven en te beheren, en om shares te beheren.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Ga voor meer informatie naar [een bestelling maken voor uw Azure stack edge-apparaat](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Azure stack Edge lokale webgebruikersinterface** : gebruik de lokale web-UI om diagnostische gegevens uit te voeren, sluit het Azure stack edge-apparaat af en start het opnieuw op, Bekijk Logboeken kopiëren en neem contact op met Microsoft ondersteuning om een service aanvraag te doen.

    <!--![The Azure Stack Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Voor informatie over het gebruik van de Web-UI, gaat u naar [de webgebaseerde gebruikers interface gebruiken om uw Azure stack Edge te beheren](data-box-edge-manage-access-power-connectivity-mode.md).


## <a name="region-availability"></a>Regionale beschikbaarheid

Azure Stack rand het fysieke apparaat, de Azure-resource en het doel-opslag account waarnaar u de gegevens overdraagt, moeten zich niet allemaal in dezelfde regio bevinden.

- **Beschik baarheid van resources** : als u een lijst wilt weer geven met alle regio's waar de Azure stack Edge-resource beschikbaar is, gaat u naar [Azure-producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Stack Edge kan ook in de Azure Government Cloud worden geïmplementeerd. Zie [Wat is Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)voor meer informatie.
    
- **Doelopslagaccounts**: De opslagaccounts waarin de gegevens worden opgeslagen, zijn beschikbaar in alle Azure-regio’s. De regio's waar het opslag accounts archief Azure Stack Edge-gegevens moeten zich bevinden dicht bij de locatie waar het apparaat zich bevindt voor optimale prestaties. Een opslagaccount dat zich ver van het apparaat vandaan bevindt, resulteert in lange latenties en tragere prestaties. 


## <a name="next-steps"></a>Volgende stappen

- Bekijk de [systeem vereisten voor de Azure stack Edge](data-box-edge-system-requirements.md).
- Meer informatie over de [limieten voor de Azure stack rand](data-box-edge-limits.md).
- [Azure Azure stack Edge](data-box-edge-deploy-prep.md) implementeren in azure Portal.




