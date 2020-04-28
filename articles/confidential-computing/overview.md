---
title: Overzicht van Azure vertrouwelijk computing
description: Overzicht van Azure vertrouwelijk (ACC) computing
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: ae98325d98df1ac8a06e0c0bc950d89cc6b77eda
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192260"
---
# <a name="confidential-computing-on-azure"></a>Vertrouwelijk Computing op Azure

Met Azure vertrouwelijk computing kunt u uw gevoelige gegevens isoleren terwijl deze worden verwerkt in de Cloud. Veel branches gebruiken vertrouwelijke computing om hun gegevens te beveiligen. Deze werk belastingen zijn onder andere:

- Financiële gegevens beveiligen
- Informatie over patiënten beveiligen
- machine learning processen uitvoeren op gevoelige informatie
- Algoritmen uitvoeren op versleutelde gegevens sets uit meerdere bronnen


## <a name="overview"></a>Overzicht
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

We weten dat het beveiligen van uw Cloud gegevens belang rijk is. We horen graag van u. Dit zijn slechts enkele vragen die onze klanten kunnen hebben wanneer ze gevoelige werk belastingen naar de Cloud verplaatsen: 

- Hoe kan ik weet u zeker dat micro soft geen toegang kan krijgen tot gegevens die niet zijn versleuteld?
- Hoe kan ik beveiligings Risico's van geprivilegieerde beheerders in mijn bedrijf voor komen?
- Wat zijn meer manieren waarop ik kan voor komen dat derden toegang krijgen tot gevoelige klant gegevens?

Microsoft Azure helpt u om uw kwets baarheid te minimaliseren voor betere gegevens bescherming. Azure biedt al veel hulpprogram ma's om [**gegevens**](../security/fundamentals/encryption-atrest.md) te beschermen via modellen zoals versleuteling aan de client zijde en versleuteling aan de server zijde. Daarnaast biedt Azure mechanismen voor het versleutelen [**van gegevens die onderweg**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) zijn via beveiligde protocollen zoals TLS en HTTPS. Op deze pagina wordt een derde poot van gegevens versleuteling geïntroduceerd: de versleuteling van **gegevens die in gebruik**zijn.


## <a name="introduction-to-confidential-computing"></a>Inleiding tot vertrouwelijke computing<a id="intro to acc"></a>

Vertrouwelijk computing is een branche term gedefinieerd door het CC ( [vertrouwelijk computing consortium](https://confidentialcomputing.io/) ), een basis die is toegewezen aan het definiëren en versnellen van de acceptatie van vertrouwelijke computing. Vertrouwelijk computing is de beveiliging van gegevens die worden gebruikt bij het uitvoeren van berekeningen. De berekeningen worden uitgevoerd in een hardware-gebaseerde Trust Execution Environment (TEE).

Een TEE is een omgeving die de uitvoering afdwingt van alleen geautoriseerde code. Gegevens in het TEE kunnen niet worden gelezen of gemanipuleerd door code buiten die omgeving.

### <a name="enclaves-and-trusted-execution-environments"></a>Enclaves en vertrouwde uitvoerings omgevingen

In de context van vertrouwelijke computing worden TEEs doorgaans aangeduid als *enclaves* of *Secure enclaves*. Enclaves zijn beveiligde delen van de processor en het geheugen van een hardware. Het is niet mogelijk om gegevens of code in de enclave weer te geven, zelfs met een fout opsporingsprogramma. Als niet-vertrouwde code probeert de inhoud in het enclave-geheugen te wijzigen, wordt de omgeving uitgeschakeld en worden de bewerkingen geweigerd.

Bij het ontwikkelen van toepassingen kunt u [Software hulpprogramma's](#oe-sdk) gebruiken om delen van uw code en gegevens in de enclave te afschermen. Met deze hulpprogram ma's kunt u ervoor zorgen dat uw code en gegevens niet kunnen worden weer gegeven of gewijzigd door iemand buiten de vertrouwde omgeving. 

U kunt een enclave als een zwart vakje beschouwen. U plaatst versleutelde code en gegevens in het vak. Vanuit de buiten kant van het vak kunt u niets zien. U geeft de enclave een sleutel voor het ontsleutelen van de gegevens, de gegevens worden vervolgens verwerkt en opnieuw versleuteld voordat ze worden verzonden vanuit de enclave.

### <a name="attestation"></a>Attestation

U wilt verificatie en validatie ontvangen die uw vertrouwde omgeving veilig is. Deze verificatie is het proces van Attestation. 

Met Attestation kan een Relying Party hoger vertrouwen hebben dat hun software is (1) in een enclave en (2) dat de enclave up-to-date is en beveiligd is. Een enclave vraagt bijvoorbeeld de onderliggende hardware om een referentie te genereren die het bewijs levert dat de enclave op het platform bestaat. Het rapport kan vervolgens worden toegewezen aan een tweede enclave die controleert of het rapport op hetzelfde platform is gegenereerd.

Attestation moet worden geïmplementeerd met behulp van een Secure Attestation-service die compatibel is met de systeem software en silicium. [De services van de Attestation-en inrichtings service van Intel](https://software.intel.com/sgx/attestation-services) zijn compatibel met virtuele machines van Azure voor vertrouwelijke computing.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Azure gebruiken voor op de cloud gebaseerde vertrouwelijke computing<a id="cc-on-azure"></a>

Met Azure vertrouwelijk computing kunt u gebruikmaken van de mogelijkheden van vertrouwelijke computer gebruik in een gevirtualiseerde omgeving. U kunt nu hulpprogram ma's, software en Cloud infrastructuur gebruiken om te bouwen op veilige hardware. 

### <a name="virtual-machines"></a>Virtuele machines

Azure is de eerste cloud provider die vertrouwelijke computing biedt in een gevirtualiseerde omgeving. We hebben virtuele machines ontwikkeld die fungeren als een abstractie laag tussen de hardware en uw toepassing. U kunt workloads op schaal uitvoeren en met de opties voor redundantie en beschik baarheid.  

#### <a name="intel-sgx-enabled-virtual-machines"></a>Intel SGX-ingeschakelde Virtual Machines

In azure-machines met vertrouwelijke Computing wordt een deel van de hardware van de CPU gereserveerd voor een deel van code en gegevens in uw toepassing. Dit beperkte gedeelte is het enclave. 

![VM-model](media/overview/hardware-backed-enclave.png)

Azure vertrouwelijk computing-infra structuur bestaat momenteel uit een speciale SKU van virtuele machines (Vm's). Deze Vm's worden uitgevoerd op Intel-processors met software Guard extension (Intel SGX). [Intel SGX](https://intel.com/sgx) is het onderdeel dat de verhoogde beveiliging mogelijk maakt die we met vertrouwelijke computing kunnen belichten. 

Nu biedt Azure de [DCsv2-serie](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) gebaseerd op Intel SGX-technologie voor het maken van op hardware gebaseerde enclave. U kunt veilige enclave toepassingen bouwen om uit te voeren in de DCsv2-serie Vm's om uw toepassings gegevens en code in gebruik te beveiligen. 

[Meer](virtual-machine-solutions.md) informatie over het implementeren van virtuele Azure computing-machines met op hardware gebaseerde vertrouwde enclaves vindt u hier.

## <a name="application-development"></a>Toepassings ontwikkeling<a id="application-development"></a>

Als u gebruik wilt maken van de kracht van enclaves en geïsoleerde omgevingen, moet u hulpprogram ma's gebruiken die ondersteuning bieden voor vertrouwelijke computing. Er zijn verschillende hulpprogram ma's die ondersteuning bieden voor de ontwikkeling van enclave-toepassingen. U kunt deze open source-frameworks bijvoorbeeld gebruiken: 

- [De open enclave Software Development Kit (SDK)](https://github.com/openenclave/openenclave)
- [Het vertrouwelijk consortium Framework (CCF)](https://github.com/Microsoft/CCF)

### <a name="overview"></a>Overzicht

Een toepassing die is gebouwd met enclaves, wordt op twee manieren gepartitioneerd:
1. Een ' niet-vertrouwd ' onderdeel (de host)
1. Een ' vertrouwd ' onderdeel (de enclave)

**De host** is uw enclave-toepassing die wordt uitgevoerd op een niet-vertrouwde omgeving. De code in de host heeft geen toegang tot de code die is geladen in de enclave. 

**De enclave is de** plaats waar code en gegevens worden uitgevoerd in de tee-implementatie. Beveiligde berekeningen moeten zich in de enclave bevinden om ervoor te zorgen dat geheimen en gevoelige gegevens beschermd blijven. 

Wanneer u begint met het ontwikkelen van een enclave-toepassing, moet u bepalen welke code en gegevens moeten worden beveiligd. De code die u in het vertrouwde onderdeel wilt plaatsen, is geïsoleerd van de rest van uw toepassing. Zodra de enclave is geïnitialiseerd en de code in het geheugen wordt geladen, kan deze code niet worden gelezen of gewijzigd vanuit een buiten beveiligde omgeving.

### <a name="open-enclave-software-development-kit-oe-sdk"></a>Open enclave Software Development Kit (OE SDK)<a id="oe-sdk"></a>

Gebruik een bibliotheek of Framework dat door uw provider wordt ondersteund als u code wilt schrijven die in een enclave wordt uitgevoerd. De [Open ENCLAVE SDK](https://github.com/openenclave/openenclave) (OE SDK) is een open-source-SDK waarmee u meer dan verschillende apparaten met vertrouwelijke computer functionaliteit kunt samen vatting. 

De OE SDK is gebouwd om een enkele abstractie laag te zijn op elke wille keurige hardware op elke CSP. De OE-SDK kan worden gebruikt voor virtuele machines van Azure voor vertrouwelijke computing om toepassingen te maken en uit te voeren boven op enclaves.

## <a name="next-steps"></a>Volgende stappen

Implementeer een virtuele machine uit de DCsv2-serie en installeer de OE SDK hierop.

> [!div class="nextstepaction"]
> [Een vertrouwelijke computing-VM implementeren in azure Marketplace](quick-create-marketplace.md)