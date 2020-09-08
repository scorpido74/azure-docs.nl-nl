---
title: Azure Attestation-overzicht
description: Een overzicht van Microsoft Azure Attestation, een oplossing voor het afleiden van Trusted Execution Environments (TEE’s)
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: ad164f8af3e5506ae5ac9121010b99303286dd1f
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320593"
---
# <a name="microsoft-azure-attestation-preview"></a>Microsoft Azure Attestation (preview)

Microsoft Azure Attestation (preview) is een oplossing voor het afleiden van TEEs (Trusted Execution Environment), zoals [Intel® software Guard Extensions](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX) enclaves en [Op virtualisatie gebaseerde Security](/windows-hardware/design/device-experiences/oem-vbs) (VBS) enclaves. Enclave Attestation is een proces voor het controleren of een enclave veilig en betrouwbaar is.

Attestation is een proces voor het demonstreren dat de software-binaire bestanden op de juiste manier op een vertrouwd platform zijn geïnstantieerd. Externe relying party's kunnen dan betrouwbaarheid krijgen dat alleen dergelijke bedoelde software wordt uitgevoerd op vertrouwde hardware. Azure Attestation is een uniforme klantgerichte service en kader voor Attestation.

Met Azure Attestation kunnen geavanceerde beveiligingsmodellen, zoals [Azure Confidential Computing](../confidential-computing/overview.md) en Intelligent Edge-beveiliging. Klanten hebben de mogelijkheid gevraagd om de locatie van een machine onafhankelijk te controleren, het postuur van een virtuele machine (VM) op die computer en de omgeving waarin enclaves op die VM worden uitgevoerd. Met Azure Attestation kunnen deze en vele bijkomende aanvragen van klanten worden gestimuleerd.

Azure Attestation ontvangt bewijs van berekeningsentiteiten, zet ze om in een set claims, valideert ze op basis van configureerbare beleidsregels en produceert cryptografische bewijzen voor op claims gebaseerde toepassingen (bijvoorbeeld relying party's en controle-instanties).

## <a name="use-cases"></a>Gebruiksvoorbeelden

Azure Attestation biedt uitgebreide Attestation-Services voor meerdere omgevingen en aparte use-cases.

### <a name="sgx-attestation"></a>SGX-Attestation

SGX verwijst naar hardwarematige isolatie, die wordt ondersteund op bepaalde Intel CPU-modellen. SGX maakt het mogelijk code uit te voeren in gezuiverde compartimenten, ook wel SGX-enclaves genoemd. De machtigingen voor toegang en geheugen worden vervolgens beheerd door hardware om een minimaal aanvalsoppervlak met de juiste isolatie te garanderen.

Clienttoepassingen kunnen worden ontworpen om gebruik te maken van SGX-enclaves door beveiligingsgevoelige taken te delegeren in deze enclaves. Dergelijke toepassingen kunnen vervolgens gebruik maken van Azure Attestation om regelmatig een vertrouwensrelatie tot stand te brengen in de enclave en de mogelijkheid om toegang te krijgen tot gevoelige gegevens.

### <a name="vbs-attestation"></a>VBS-Attestation

VBS is een architectuur op basis van software voor een enclave-geheugenbeveiliging op basis van Hyper-V. Hiermee wordt voorkomen dat de beheercode van de host en de lokale en cloud service-beheerders toegang krijgen tot de gegevens in een VBS-enclave of invloed hebben op de uitvoering ervan.

Net als bij SGX-technologie ondersteunt Azure Attestation het valideren van VBS-enclaves tegen geconfigureerde beleidsregels en het verlenen van een certificeringsverklaring als bewijs van de geldigheid.

### <a name="open-enclave"></a>Open Enclave
[Open Enclave](https://openenclave.io/sdk/) (OE) is een verzameling van bibliotheken die gericht zijn op het maken van een enkelvoudige enclaving-abstractie voor ontwikkelaars voor het bouwen van op TEE gebaseerde toepassingen. Het biedt een universeel veilig app-model dat platformbijzonderheden minimaliseert. Microsoft beschouwt dit als een essentiële opstap voor democratisering van op hardware gebaseerde enclave-technologieën zoals SGX en het verhogen van hun opname op Azure.

OE standaardiseert specifieke vereisten voor de verificatie van een enclave-bewijs. Hiermee wordt OE gekwalificeerd als een uiterst aanpasbare Attestation-consument van Azure Attestation.

## <a name="azure-attestation-can-run-in-a-tee"></a>Azure Attestation kan worden uitgevoerd in een TEE

Azure Attestation is essentieel voor vertrouwelijke computerscenario's, omdat het de volgende acties uitvoert:

- Controleert of de enclave-bewijzen geldig zijn.
- Evalueert het enclave-bewijs op basis van een door de klant gedefinieerd beleid.
- Beheert en tenant-specifieke beleidsregels op.
- Genereert en ondertekent een token dat wordt gebruikt door relying party's om te communiceren met de enclave.

Azure Attestation is gebouwd om te worden uitgevoerd in twee soorten omgevingen:
- Azure Attestation wordt uitgevoerd in een SGX-ingeschakelde TEE.
- Azure Attestation wordt uitgevoerd in een niet-TEE.

Klanten van Azure Attestation hebben een vereiste voor Microsoft uitgesproken om operationeel vanuit de Trusted Computing Base (TCB) te worden uitgevoerd. Dit is om te voorkomen dat Microsoft-entiteiten, zoals VM-beheerders, hostbeheerders en Microsoft-ontwikkelaars, de Attestation-aanvragen, beleidsregels en door Azure Attestation uitgegeven tokens wijzigen. Azure Attestation is ook ontworpen om te worden uitgevoerd in TEE, waarbij functies van Azure Attestation, zoals offertevalidatie, het genereren van tokens en token-ondertekening, worden verplaatst naar een SGX-enclave.

## <a name="why-use-azure-attestation"></a>Waarom Azure Attestation gebruiken

Azure Attestation is de voorkeurskeuze voor het afleveren van TEEs omdat het de volgende voordelen biedt: 

- Unified Framework voor het bekrachtigen van meerdere TEEs, zoals SGX-enclaves en VBS-enclaves
- Multi-tenant-service waarmee aangepaste Attestation-providers en -beleid kunnen worden geconfigureerd om het genereren van tokens te beperken
- Biedt standaardproviders die kunnen worden verklaard zonder configuratie van gebruikers
- Beveiligt de gegevens die worden gebruikt bij de implementatie in een SGX-enclave
- Maximaal beschikbare service waarmee een Service Level Agreement (SLA) wordt aangeboden

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Ondersteuning voor bedrijfscontinuïteit en herstel na noodgevallen (BCDR)

[Bedrijfscontinuïteit en herstel na noodgevallen](/azure/best-practices-availability-paired-regions) (BCDR) voor Azure Attestation maakt het mogelijk om service-onderbrekingen te beperken die voortkomen uit aanzienlijke beschikbaarheidsproblemen of noodgebeurtenissen in een regio.

Hieronder ziet u de regio's die momenteel worden ondersteund door BCDR
- VS-Oost 2 = > gekoppeld aan VS-Centraal.
- VS-Centraal = > gekoppeld aan VS-Oost 2.

Clusters die in twee regio's zijn geïmplementeerd, werken onder normale omstandigheden onafhankelijk. In het geval van een storing of uitval van één regio vindt het volgende plaats:

- Azure Attestation BCDR zorgt voor een naadloze failover waarbij klanten geen extra stap hoeven uit te voeren om te herstellen
- De [Azure Traffic Manager](../traffic-manager/index.yml) voor de regio detecteert dat de statustest is gedegradeerd en schakelt het eindpunt over naar de gekoppelde regio
- Bestaande verbindingen werken niet en ontvangen een interne serverfout of time-out voor problemen
- Alle besturingsvlak-bewerkingen worden geblokkeerd. Klanten kunnen geen Attestation-providers in de primaire regio maken of beleid bijwerken
- Alle gegevenslaagbewerkingen, inclusief attest, blijven werken in de primaire regio

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Attestation-basisconcepten](basic-concepts.md)
- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Azure Attestation instellen met behulp van PowerShell](quickstart-powershell.md)

