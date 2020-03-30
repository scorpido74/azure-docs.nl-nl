---
title: Overzicht van Microsoft Azure Stack Edge | Microsoft Documenten
description: Beschrijft Azure Stack Edge, een opslagoplossing die een fysiek apparaat gebruikt voor netwerkoverdracht naar Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: f463e8883efd5e2dfc4d7fff80912c193665b850
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78399800"
---
# <a name="what-is-azure-stack-edge"></a>Wat is Azure Stack Edge?

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Azure Stack Edge is een Edge Computing-apparaat met AI-functie met mogelijkheden voor netwerkgegevensoverdracht. In dit artikel vindt u een overzicht van de Azure Stack Edge-oplossing, voordelen, sleutelmogelijkheden en de scenario's waarin u dit apparaat implementeren.

Azure Stack Edge is een Hardware-as-a-service-oplossing. Microsoft stuurt u een cloudbeheerd apparaat met een ingebouwde Field Programmeble Gate Array (FPGA) die versnelde AI-inferencing mogelijk maakt en alle mogelijkheden van een netwerkopslaggateway heeft.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Hier volgen de verschillende scenario's waarin Azure Stack Edge kan worden gebruikt voor snelle Machine Learning (ML) conclusies aan de rand en preprocessing gegevens voordat deze naar Azure worden verzonden.

- **Conclusie met Azure Machine Learning** - Met Azure Stack Edge u ML-modellen uitvoeren om snelle resultaten te krijgen waarop kan worden gereageerd voordat de gegevens naar de cloud worden verzonden. De volledige gegevensset kan optioneel worden overgedragen om uw ML-modellen te blijven trainen en verbeteren. Zie [Azure ML-hardware versnelde modellen](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server)implementeren op Azure Stack Edge voor meer informatie over het gebruik van de azure ML-hardwareversnelde modellen op Azure Stack Edge.

- **Gegevens vooraf verwerken** : gegevens transformeren voordat u deze naar Azure verzendt om een meer bruikbare gegevensset te maken. Voorverwerking kan worden gebruikt om: 

    - Gegevens samen te voegen.
    - Gegevens wijzigen, bijvoorbeeld om persoonsgegevens te verwijderen.
    - Subsetgegevens om opslag en bandbreedte te optimaliseren, of voor verdere analyse.
    - IoT-gebeurtenissen te analyseren en erop te reageren. 

- **Gegevens overzetten via het netwerk naar Azure** - Gebruik Azure Stack Edge om eenvoudig en snel gegevens over te dragen naar Azure om verdere rekenkracht en analyse of archiveringsdoeleinden mogelijk te maken. 

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Azure Stack Edge heeft de volgende mogelijkheden:

|Mogelijkheid |Beschrijving  |
|---------|---------|
|Versnelde AI-inferencing| Ingeschakeld door de ingebouwde FPGA.|
|Berekenen       |Gegevens kunnen worden geanalyseerd, verwerkt of gefilterd.|
|Hoge prestaties | Hoge prestaties compute en gegevensoverdrachten.|
|Toegang tot gegevens     | Rechtstreekse gegevenstoegang vanuit Azure Storage Blobs en Azure Files met behulp van cloud-API’s voor aanvullende gegevensverwerking in de cloud. Lokale cache op het apparaat wordt gebruikt voor snelle toegang tot de meest recent gebruikte bestanden.|
|Cloud-beheerd     |Apparaat en service worden beheerd via de Azure-portal.  |
|Offline upload     | Modus zonder verbinding ondersteunt scenario’s voor offline uploaden.|
|Ondersteunde protocollen     | Ondersteuning voor het standaard SMB- en NFS-protocol voor gegevensopname. <br> Zie [Azure Stack Edge-systeemvereisten](data-box-edge-system-requirements.md)voor meer informatie over ondersteunde versies.|
|Gegevensvernieuwing     | Mogelijkheid om lokale bestanden te vernieuwen met de meest recente uit de cloud.|
|Versleuteling    | BitLocker-ondersteuning om gegevens lokaal te versleutelen en de gegevensoverdracht naar de cloud via *HTTPS* te beveiligen.|
|Bandbreedtebeperking| Gashendel om het bandbreedtegebruik tijdens piekuren te beperken.|
|ExpressRoute | Extra beveiliging via ExpressRoute. Gebruik peeringconfiguratie waarbij verkeer van lokale apparaten naar de eindpunten voor cloudopslag via de ExpressRoute wordt verplaatst. Raadpleeg [Overzicht van ExpressRoute](../expressroute/expressroute-introduction.md) voor meer informatie.

## <a name="components"></a>Onderdelen

De Azure Stack Edge-oplossing bestaat uit Azure Stack Edge-bron, een fysiek azure stackedge-apparaat en een lokale web-gebruikersinterface.

* **Azure Stack Edge fysiek apparaat** - Een 1U rack-mounted server geleverd door Microsoft die kan worden geconfigureerd om gegevens te verzenden naar Azure.
    
* **Azure Stack Edge-bron** : een bron in de Azure-portal waarmee u een Azure Stack Edge-apparaat beheren vanuit een webinterface die u vanaf verschillende geografische locaties openen. Gebruik de Azure Stack Edge-bron om resources te maken en te beheren, apparaten en waarschuwingen weer te geven en te beheren en shares te beheren.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Ga voor meer informatie naar [Een bestelling maken voor uw Azure Stack Edge-apparaat.](data-box-edge-deploy-prep.md#create-a-new-resource)

* **Lokale webgebruikers van Azure Stack Edge** - Gebruik de lokale web-gebruikersinterface om diagnoses uit te voeren, het Azure Stack Edge-apparaat af te sluiten en opnieuw te starten, kopieerlogboeken weer te geven en contact op te nemen met Microsoft Support om een serviceaanvraag in te dienen.

    <!--![The Azure Stack Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Ga voor informatie over het gebruik van de webgebaseerde gebruikersinterface naar [De webgebaseerde gebruikersinterface gebruiken om uw Azure Stack Edge te beheren.](data-box-edge-manage-access-power-connectivity-mode.md)

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Het fysieke apparaat, de Azure-bron en het doelopslagaccount van Azure Stack Edge waarnaar u gegevens overdraagt, hoeven zich niet allemaal in dezelfde regio te bevinden.

- **Beschikbaarheid van resources** - Zie [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)voor een lijst met alle regio's waar de Azure Stack Edge-bron beschikbaar is. Azure Stack Edge kan ook worden geïmplementeerd in de Azure Government Cloud. Zie [Wat is Azure-overheid voor](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)meer informatie?
    
- **Doelopslagaccounts**: De opslagaccounts waarin de gegevens worden opgeslagen, zijn beschikbaar in alle Azure-regio’s. De regio's waar de opslagaccounts Azure Stack Edge-gegevens opslaan, moeten zich in de buurt van de plaats bevinden waar het apparaat zich bevindt voor optimale prestaties. Een opslagaccount dat zich ver van het apparaat vandaan bevindt, resulteert in lange latenties en tragere prestaties.

## <a name="next-steps"></a>Volgende stappen

- Controleer de [systeemvereisten van Azure Stack Edge](data-box-edge-system-requirements.md).
- Begrijp de [Azure Stack Edge-limieten](data-box-edge-limits.md).
- [Azure Azure Stack Edge implementeren](data-box-edge-deploy-prep.md) in Azure-portal.
