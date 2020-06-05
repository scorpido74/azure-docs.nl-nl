---
title: Wat is nieuw? Release opmerkingen-Azure Block Chain Service
description: Meer informatie over wat er nieuw is in azure Block Chain Service, zoals de nieuwste opmerkingen bij de release, versies, bekende problemen en aanstaande wijzigingen.
ms.date: 06/03/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: c5316aa387de28fe1a78b336eb2e9e010c624b02
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435427"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Wat is er nieuw in de Azure Block Chain-Service?

> Ontvang een melding over wanneer u deze pagina voor updates opnieuw moet bezoeken door deze URL te kopiëren en te plakken: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` in uw RSS feed reader- [ ![ pictogram voor RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us)-feed.

De Azure Block Chain-service ontvangt verbeteringen doorlopend. Dit artikel bevat informatie over het volgende om op de hoogte te blijven van de meest recente ontwikkelingen:

- Nieuwe mogelijkheden
- Versie-upgrades
- Bekende problemen

---

## <a name="may-2020"></a>Mei 2020

### <a name="version-upgrades"></a>Versie-upgrades

- Ubuntu-versie-upgrade naar 18,04
- Upgrade van quorum versie naar 2.5.0
- Tessera-versie-upgrade 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>De Azure Block Chain-service ondersteunt het verzenden van rawPrivate-trans acties

**Type:** Hulp

Klanten kunnen persoonlijke trans acties buiten het account op het knoop punt ondertekenen.

### <a name="two-phase-member-provisioning"></a>Inrichting van twee fasen

**Type:** Verbetering

In twee fasen kunt u scenario's voor het maken van een lid in een lang bestaand consortium optimaliseren. De infra structuur van het lid is in de eerste fase ingericht. In de tweede fase wordt het lid gesynchroniseerd met block chain. Bij het inrichten in twee fasen wordt het maken van een lid voor komen vanwege time-outs.

## <a name="known-issues"></a>Bekende problemen

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>De analyse stopt als er minder dan vier validatie knooppunten zijn

Productie netwerken moeten ten minste vier validatie knooppunten hebben. Quorum raadt ten minste vier validatie knooppunten aan om te voldoen aan de IBFT-crash fout tolerantie (3F + 1). Er moeten ten minste twee knoop punten van de *Standard* -laag van Azure Block chain zijn om vier validatie knooppunten te krijgen. Een standaard knooppunt is ingericht met twee validatie knooppunten.  

Als het block chain-netwerk in de Azure Block Chain-service niet vier validatie knooppunten heeft, kan de analyse stoppen op het netwerk. U kunt het detecteren van Mining gestopt door een waarschuwing in te stellen voor verwerkte blokken. In een goed netwerk is het verwerkte blok 60 blokken per knoop punt van vijf minuten.

Als oplossing moet het Azure Block Chain Service-team het knoop punt opnieuw opstarten. Klanten moeten een ondersteunings aanvraag openen om het knoop punt opnieuw op te starten. Het Azure Block Chain Service-team werkt samen met het automatisch detecteren en herstellen van analyse problemen.

De *Standard* -laag gebruiken voor implementaties van productie cijfers. Gebruik de *basis* -laag voor het ontwikkelen, testen en testen van concepten. Het wijzigen van de prijs categorie tussen basis en standaard nadat het maken van een lid is niet ondersteund.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>Voor Block Chain Data Manager is een Standard-laag knooppunt vereist

Gebruik de laag *standaard* als u block chain-Data Manager gebruikt. De laag *Basic* heeft alleen 4 GB geheugen. Dit kan daarom niet worden geschaald naar het gebruik dat is vereist voor Block Chain Data Manager en andere services die erop worden uitgevoerd.

Gebruik de *basis* -laag voor het ontwikkelen, testen en testen van concepten. Het wijzigen van de prijs categorie tussen basis en standaard nadat het maken van een lid is niet ondersteund.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>Grote volume van Unlock-account aanroepen zorgt ervoor dat Geth vastloopt

Een grote hoeveelheid Unlock-account aanroepen tijdens het verzenden van een trans actie kan ertoe leiden dat Geth vastloopt op een transactie knooppunt. Als gevolg hiervan moet u de opname van trans acties stoppen. Anders wordt de transactie wachtrij van de in behandeling genomen.

Geth wordt binnen minder dan een minuut automatisch opnieuw opgestart. Afhankelijk van het knoop punt kan het enige tijd duren voordat de synchronisatie is uitgevoerd. Het Azure Block Chain Service-team schakelt een archiverings functie in waarmee de synchronisatie wordt gereduceerd.

Als u Geth crashes wilt identificeren, kunt u Logboeken voor elk fout bericht in Block Chain-berichten in toepassings logboeken controleren. U kunt ook controleren of verwerkte blokken afnemen terwijl er trans acties in behandeling toenemen.

Om het probleem te verhelpen, moet u ondertekende trans acties verzenden in plaats van niet-ondertekende trans acties te verzenden met een opdracht om het account te ontgrendelen. Voor trans acties die al extern zijn ondertekend, hoeft u het account niet te ontgrendelen.

Als u niet-ondertekende trans acties wilt verzenden, ontgrendelt u het account voor oneindige tijd door het verzenden van 0 als de para meter tijd in de opdracht voor ontgrendelen. U kunt het account weer vergren delen nadat alle trans acties zijn verzonden.  

Hieronder vindt u de Geth-para meters die worden gebruikt door de Azure Block Chain-service. U kunt deze para meters niet aanpassen.

- Istanboel-blokkerings periode: 5 sec.
- Limiet voor vloer gas: 700000000
- Ceil: 800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>Grote hoeveel heden persoonlijke trans acties verminderen de prestaties

Als u Azure Block Chain Service Basic-laag en persoonlijke trans acties gebruikt, kan Tessera vastlopen.

U kunt de crash van de Tessera detecteren door de logboeken van de Block Chain-toepassing te bekijken en te zoeken naar het bericht `Tessera crashed. Restarting Tessera...` .

De Azure Block Chain-service start Tessera opnieuw op wanneer er een crash is. Opnieuw opstarten duurt ongeveer een minuut.

Gebruik de laag *standaard* als u een groot aantal privé transacties verzendt. Gebruik de *basis* -laag voor het ontwikkelen, testen en testen van concepten. Het wijzigen van de prijs categorie tussen basis en standaard nadat het maken van een lid is niet ondersteund.

### <a name="calling-ethestimate-gas-function-reduces-performance"></a>Het aanroepen van ETH. een schatting van de functie gas vermindert de prestaties

Het aanroepen van *Eth. schattings* functie meerdere keer dat trans acties per seconde drastisch worden verminderd. Gebruik niet de functie *Eth. schatting* voor het verzenden van elke trans actie. De functie *Eth. estimate* is geheugen intensief.

Gebruik, indien mogelijk, een conservatieve aardgas waarde voor het verzenden van trans acties en Minimaliseer het gebruik van *Eth. estimate*.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>Ongebonden lussen in slimme contracten verminderen de prestaties

Vermijd ongebonden lussen in slimme contracten, omdat deze de prestaties kunnen verminderen. Zie de volgende bronnen voor meer informatie:

- [Niet-begrensde lussen voor komen](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [Aanbevolen procedures voor de beveiliging van Smart contracten](https://github.com/ConsenSys/smart-contract-best-practices)
- [Richt lijnen voor intelligente contracten van quorum](http://docs.goquorum.com/en/latest/Security/Framework/Decentralized%20Application/Smart%20Contracts%20Security/)
- [Richt lijnen voor grenzen van het gas en lussen die worden verzorgd door middel van volheid](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)