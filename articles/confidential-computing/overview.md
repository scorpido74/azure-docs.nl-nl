---
title: Overzicht van Azure Confidential Computing
description: Overzicht van ACC (Azure Confidential Computing)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 44006bdfd9ffe6e78380adefe9271f42c0a76f84
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773276"
---
# <a name="confidential-computing-on-azure"></a>Confidential Computing op Azure

Met Azure Confidential Computing kunt u uw gevoelige gegevens isoleren terwijl deze worden verwerkt in de cloud. Veel branches gebruiken confidential computing om hun gegevens te beveiligen. Deze werkbelastingen zijn onder andere:

- Beveiliging van financiële gegevens
- Bescherming van patiëntgegevens
- Het uitvoeren van machine learning processen met gevoelige informatie
- Het uitvoeren van algoritmen op versleutelde gegevenssets uit meerdere bronnen


## <a name="overview"></a>Overzicht
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

We weten dat het beveiligen van uw cloudgegevens belangrijk is. We kennen uw zorgen. Dit volgen slechts enkele vragen die onze klanten kunnen hebben wanneer ze gevoelige workloads naar de cloud verplaatsen: 

- Hoe kan ik ervoor zorgen dat Microsoft geen toegang heeft tot gegevens die niet zijn versleuteld?
- Hoe kan ik beveiligingsrisico's voorkomen, afkomstig van bevoegde beheerders in mijn bedrijf?
- Zijn er nog meer manieren waarop ik kan voorkomen dat derden toegang krijgen tot gevoelige klantgegevens?

Microsoft Azure helpt u om de kwetsbaarheid voor aanvallen te beperken voor betere gegevensbescherming. Azure biedt al veel hulpprogramma's om [**data-at-rest-** ](../security/fundamentals/encryption-atrest.md) te beschermen via modellen zoals versleuteling aan clientzijde en versleuteling aan serverzijde. Daarnaast biedt Azure mechanismen voor het versleutelen van [**gegevens in overdracht**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) via beveiligde protocollen zoals TLS en HTTPS. Op deze pagina maakt u kennis met een derde element van gegevensversleuteling: de versleuteling van **gegevens in gebruik**.


## <a name="introduction-to-confidential-computing"></a>Inleiding tot confidential computing<a id="intro to acc"></a>

Confidential computing is een industriële term die is gedefinieerd door het [Confidential Computing Consortium](https://confidentialcomputing.io/) (CCC), een stichting die zich toelegt op het definiëren en versnellen van de acceptatie van confidential computing. Het CCC definieert Confidential Computing als de beveiliging van gegevens in gebruik door berekeningen uit te voeren in een hardware-gebaseerde TEE (Trust Execution Environment).

Een TEE is een omgeving die de uitvoering afdwingt van alleen geautoriseerde code. Gegevens in de TEE kunnen niet worden gelezen of gemanipuleerd door code buiten die omgeving.

### <a name="enclaves"></a>Enclaves

Enclaves zijn beveiligde delen van de processor en het geheugen van een hardware. Gegevens of code in een enclave kunnen niet worden weergegeven, zelfs niet met een foutopsporingsprogramma. Als niet-vertrouwde code probeert de inhoud in een enclavegeheugen te wijzigen, wordt de omgeving uitgeschakeld en worden de bewerkingen geweigerd.

Bij het ontwikkelen van toepassingen kunt u [softwarehulpprogramma's](#oe-sdk) gebruiken om delen van uw code en gegevens in de enclave af te schermen. Met deze hulpprogramma's kunt u ervoor zorgen dat uw code en gegevens niet kunnen worden weergegeven of gewijzigd door iemand buiten de vertrouwde omgeving. 

Eigenlijk kunt u een enclave beschouwen als een soort zwarte doos. U plaatst versleutelde code en gegevens in de doos. Vanaf de buitenkant ziet u niets. U geeft de enclave een sleutel waarmee de gegevens kunnen worden ontsleuteld, waarna de gegevens worden verwerkt en opnieuw worden versleuteld voordat ze vanuit de enclave worden verzonden.

### <a name="attestation"></a>Attestation

U wilt verificatie en validatie ontvangen dat uw vertrouwde omgeving veilig is. Deze verificatie is het proces van Attestation. 

Met Attestation kan een Relying Party meer vertrouwen krijgen dat hun software (1) wordt uitgevoerd in een enclave en (2) dat de enclave up-to-date en beveiligd is. Een enclave vraagt bijvoorbeeld de onderliggende hardware om een referentie te genereren die het bewijs levert dat de enclave op het platform bestaat. Het rapport kan vervolgens worden doorgegeven aan een tweede enclave die controleert of het rapport op hetzelfde platform is gegenereerd.

Attestation moet worden geïmplementeerd met behulp van een beveiligde attestation-service die compatibel is met de systeemsoftware en silicon. [De attestation- en inrichtingsservices van Intel](https://software.intel.com/sgx/attestation-services) zijn compatibel met virtuele machines voor Azure Confidential Computing.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Azure gebruiken voor confidential computing in de cloud<a id="cc-on-azure"></a>

Met Azure Confidential Computing kunt u de mogelijkheden van confidential computing in een gevirtualiseerde omgeving gebruiken. U kunt nu hulpprogramma's, software en cloudinfrastructuur gebruiken om te bouwen op veilige hardware. 

### <a name="virtual-machines"></a>Virtuele machines

Azure is de eerste cloudprovider die confidential computing biedt in een gevirtualiseerde omgeving. We hebben virtuele machines ontwikkeld die als een abstractielaag tussen de hardware en uw toepassing fungeren. U kunt workloads uitvoeren op schaal en met de opties voor redundantie en beschikbaarheid.  

#### <a name="intel-sgx-enabled-virtual-machines"></a>Virtuele machines met Intel SGX

In virtuele machines met Azure Confidential Computing wordt een deel van de hardware van de CPU gereserveerd voor een deel van de code en gegevens in uw toepassing. Dit beperkte gedeelte is het enclave. 

![VM-model](media/overview/hardware-backed-enclave.png)

De infrastructuur van Azure Confidential Computing bestaat momenteel uit een speciale SKU van virtuele machines (VM's). Deze VM's worden uitgevoerd op Intel-processors met Software Guard Extension (Intel SGX). [Intel SGX-](https://intel.com/sgx) is het onderdeel dat de verhoogde beveiliging mogelijk maakt die in confidential computing wordt toegepast. 

Nu biedt Azure de [DCsv2-serie](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) die is gebouwd op Intel SGX-technologie voor het maken van een hardware-enclave. U kunt veilige enclavetoepassingen bouwen om uit te voeren op VM's uit de DCsv2-serie, om uw toepassingsgegevens en -code in gebruik te beveiligen. 

U kunt [meer lezen](virtual-machine-solutions.md) over de implementatie van virtuele machines met Azure Confidential Computing met vertrouwde hardware-enclaves.

## <a name="application-development"></a>Toepassingsontwikkeling<a id="application-development"></a>

Als u de kracht van enclaves en geïsoleerde omgevingen wilt gebruiken, hebt u hulpprogramma's nodig die confidential computing ondersteunen. Er zijn verschillende hulpprogramma's die ondersteuning bieden voor de ontwikkeling van enclavetoepassingen. U kunt bijvoorbeeld deze open-source frameworks gebruiken: 

- [The Open Enclave Software Development Kit (SDK)](https://github.com/openenclave/openenclave)
- [The Confidential Consortium Framework (CCF)](https://github.com/Microsoft/CCF)

### <a name="overview"></a>Overzicht

Een toepassing die is gebouwd met enclaves, wordt op twee manieren gepartitioneerd:
1. Een 'niet-vertrouwd' onderdeel (de host)
1. Een 'vertrouwd' onderdeel (de enclave)

**De host** is waar overheen uw enclavetoepassing wordt uitgevoerd; het is een niet-vertrouwde omgeving. De enclavecode die op de host is geïmplementeerd, kan niet worden geopend door de host. 

**De enclave** is waar de toepassingscode en de gegevens in de cache/het geheugen worden uitgevoerd. Beveiligde berekeningen moeten in de enclaves worden uitgevoerd om ervoor te zorgen dat geheimen en gevoelige gegevens beveiligd blijven. 

Tijdens het toepassingsontwerp is het belangrijk om te identificeren en te bepalen welk gedeelte van de toepassing moet worden uitgevoerd in de enclaves. De code die u in het vertrouwde onderdeel wilt plaatsen, is geïsoleerd van de rest van uw toepassing. Zodra de enclave is geïnitialiseerd en de code in het geheugen wordt geladen, kan de code niet worden gelezen of gewijzigd vanuit de niet-vertrouwde onderdelen. 

### <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave Software Development Kit (OE SDK) <a id="oe-sdk"></a>

Gebruik een bibliotheek die of framework dat door uw provider wordt ondersteund als u code wilt schrijven die in een enclave wordt uitgevoerd. De [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) is een open-source SDK die abstractie mogelijk maakt over verschillende apparaten met confidential computing-functionaliteit. 

De OE SDK is gebouwd als één abstractielaag op willekeurige hardware en willekeurige CSP. De OE SDK kan worden gebruikt over virtuele machines met Azure Confidential Computing heen om toepassingen op enclaves te maken en uit te voeren.

## <a name="next-steps"></a>Volgende stappen

Implementeer een virtuele machine uit de DCsv2-serie en installeer daarop de OE SDK.

> [!div class="nextstepaction"]
> [Een VM met Azure Confidential Computing implementeren in Azure Marketplace](quick-create-marketplace.md)