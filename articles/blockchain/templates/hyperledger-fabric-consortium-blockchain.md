---
title: Hyperledger Fabric consortium-oplossings sjabloon implementeren in azure
description: De oplossings sjabloon voor het Hyperledger Fabric consortium-netwerk implementeren en configureren in azure
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: caleteet
ms.openlocfilehash: be35cfa26204b36ad65da91252144b9167cb9e54
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325139"
---
# <a name="hyperledger-fabric-consortium-network"></a>Hyperledger Fabric consortium-netwerk

U kunt de oplossingssjabloon van de Hyperledger Fabric-consortium gebruiken om een consortiumnetwerk van Hyperledger Fabric op Azure te implementeren en configureren.

Wanneer u dit artikel hebt gelezen:

- Beschikt u over kennis van blockchain, Hyperledger Fabric en meer gecompliceerde consortiumnetwerkarchitecturen
- Weet u hoe u Hyperledger Fabric-consortiumnetwerk implementeert en configureert vanuit de Azure-portal

## <a name="about-blockchain"></a>Over Block Chain

Als u nieuw bent bij de blockchain-community, biedt deze oplossingssjabloon een uitstekende gelegenheid om op een eenvoudige en configureerbare manier kennis te maken met de technologie op Azure. Blockchain is de onderliggende technologie achter Bitcoin; het is echter veel meer dan alleen een enabler voor een virtuele valuta. Het is een samenstelling van een bestaande database, gedistribueerd systeem en cryptografische technologieën die veilige multiparty-berekening mogelijk maakt met garanties rond onveranderlijkheid, verifieerbaarheid, controleerbaarheid en weerbaarheid tegen aanvallen. Verschillende protocollen gebruiken verschillende mechanismen om deze eigenschappen in te vullen. [Hyperledger Fabric](https://github.com/hyperledger/fabric) is een dergelijk protocol.

## <a name="consortium-architecture-on-azure"></a>Consortium architectuur op Azure

Om Hyperledger Fabric in Azure in te schakelen, zijn er twee primaire implementatietypes die worden ondersteund. Deze implementaties zijn ontworpen om tegemoet te komen aan verschillende topologieën, op basis van het gewenste doel.

- **Eén virtuele machine, ontwikkelaars server** : dit implementatie type is ontworpen als een ontwikkel omgeving die wordt gebruikt voor het bouwen en testen van oplossingen die zijn gebouwd op Hyperledger-infra structuur.
- **Meerdere virtuele machines, scale-out implementatie** -dit implementatie type is ontworpen voor omgevingen waarin een consortium van verschillende deel nemers gebruikmaakt van een gedeelde omgeving.

In beide implementaties zijn de bouwstenen die de kern vormen van Hyperledger Fabric hetzelfde.  De verschillen in de implementaties zijn hoe deze componenten worden geschaald.

- **CA-knoop punten**: een knoop punt met certificerings instantie die wordt gebruikt voor het genereren van certificaten die worden gebruikt voor identiteiten in het netwerk.
- **Orderer-knoop punten**: een knoop punt met communicatie service dat een leverings garantie implementeert, zoals de totale order verzending of atomische trans acties.
- **Peer knooppunten**: een knoop punt dat trans acties doorvoert en de status en een kopie van het gedistribueerde groot boek onderhoudt.
- **Couchdb-knoop punten**: een knoop punt waarop de couchdb-service kan worden uitgevoerd die de status database kan bevatten en waarmee u uitgebreide query's kunt uitvoeren van chaincode-gegevens, met uitbrei ding van eenvoudige sleutel/waarde naar JSON-type opslag.

### <a name="single-virtual-machine-architecture"></a>Architectuur met één virtuele machine

Zoals eerder vermeld, is de enkele virtuele machine-architectuur gebouwd voor ontwikkelaars om een server met een lage footprint te hebben die wordt gebruikt om applicaties te ontwikkelen. Alle getoonde containers worden uitgevoerd in een enkele virtuele machine. De bestel service maakt gebruik van [solo](https://github.com/hyperledger/fabric/tree/master/orderer) voor deze configuratie. Deze configuratie is *geen* fout tolerante bestel service, maar is ontworpen om licht te worden voor ontwikkeling.

![Architectuur met één virtuele machine](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Architectuur van meerdere virtuele machines

De meervoudige virtuele machine, scale-out architectuur, is gebouwd met hoge beschikbaarheid en kan elk onderdeel in de basis schalen. Deze architectuur is veel beter geschikt voor implementaties van productielocaties.

![Architectuur van meerdere virtuele machines](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Aan de slag

Om te beginnen hebt u een Azure-abonnement nodig dat ondersteuning biedt voor de implementatie van verschillende virtuele machines en standaardopslagaccounts. Als u geen Azure-abonnement hebt, kunt u [een gratis Azure-account maken](https://azure.microsoft.com/free/).

Zodra u een abonnement hebt, gaat u naar de [Azure Portal](https://portal.azure.com). Selecteer **een resource maken > block chain > Hyperledger Fabric consortium**.

![Hyperledger Fabric Block Chain Marketplace-sjabloon voor één lid](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Implementatie

Selecteer in de sjabloon **Hyperledger Fabric consortium** **Create**.

De sjabloon implementatie begeleidt u stapsgewijs door het configureren van het [Hyperledger 1,3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) -netwerk met meerdere knoop punten. De implementatie stroom is onderverdeeld in vier stappen: basis beginselen, consortium netwerk instellingen, infrastructuur configuratie en optionele onderdelen.

### <a name="basics"></a>Basisbeginselen

Geef in **basis principes**waarden op voor de standaard parameters voor elke implementatie. Zoals eigenschappen voor abonnementen, resourcegroepen en virtual machines.

![Basisbeginselen](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Parameternaam | Beschrijving | Toegestane waarden |
|---|---|---|
**Resource voorvoegsel** | Naamvoorvoegsel voor resources die zijn ingericht als onderdeel van de implementatie |Maxi maal 6 tekens |
**Gebruikersnaam** | De gebruikersnaam van de beheerder voor elk van de virtuele machines die voor dit lid zijn geïmplementeerd |1-64 tekens |
**Verificatie type** | De verificatiemethode voor de virtuele machine |Wachtwoord of openbare SSH-sleutel|
**Wacht woord (verificatie type = wacht woord)** |Het wachtwoord voor het beheerdersaccount voor elk van de geïmplementeerde virtuele machines. Het wacht woord moet drie van de volgende teken typen bevatten: 1 hoofd letter, 1 teken, kleine letter, 1 cijfer en 1 speciaal teken<br /><br />Hoewel alle VM's in eerste instantie hetzelfde wachtwoord hebben, kunt u het wachtwoord na de inrichting wijzigen|12-72 tekens|
**SSH-sleutel (verificatie type = open bare SSH-sleutel)** |De beveiligde shell-sleutel die wordt gebruikt voor extern aanmelden ||
**Abonnement** |Het abonnement dat moet worden geïmplementeerd ||
**Resourcegroep** |De resourcegroep waarin het consortiumnetwerk moet worden geïmplementeerd ||
**Locatie** |De Azure-regio waarin het eerste lid moet worden geïmplementeerd ||

Selecteer **OK**.

### <a name="consortium-network-settings"></a>Consortium Netwerkinstellingen

Geef in **netwerk instellingen**de invoer voor het maken of koppelen van een bestaand consortium netwerk op en configureer de instellingen van uw organisatie.

![Consortium Netwerkinstellingen](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Parameternaam | Beschrijving | Toegestane waarden |
|---|---|---|
**Netwerkconfiguratie** |U kunt ervoor kiezen om een nieuw netwerk te maken of lid te worden van een bestaand netwerk. Als u *lid worden van bestaande*, moet u aanvullende waarden opgeven. |Nieuw netwerk <br/> Bestaande koppelen |
**HLF CA-wacht woord** |Een wachtwoord dat wordt gebruikt voor de certificaten die worden gegenereerd door de certificaatautoriteiten en die worden gemaakt als onderdeel van de implementatie. Het wacht woord moet drie van de volgende teken typen bevatten: 1 hoofd letter, 1 teken, kleine letter, 1 cijfer en 1 speciaal teken.<br /><br />Hoewel alle virtuele machines in eerste instantie hetzelfde wachtwoord hebben, kunt u het wachtwoord na de inrichting wijzigen.|1-25 tekens |
**Organisatie-instellingen** |U kunt de naam en het certificaat van uw organisatie aanpassen of standaardwaarden gebruiken.|Standaard <br/> Geavanceerd |
**Instellingen voor VPN-netwerk** | Stel een VPN-tunnelgateway beschikbaar voor toegang tot de VM's | Ja <br/> Nee |

Selecteer **OK**.

### <a name="fabric-specific-settings"></a>Fabric-specifieke instellingen

In **infrastructuur configuratie**configureert u de netwerk grootte en de prestaties en geeft u de invoer voor de beschik baarheid van het netwerk op. Zoals nummersorteerders en peerknooppunten, de persistentie-engine die door elk knooppunt wordt gebruikt, en de VM-grootte.

![Infrastructuur instellingen](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Parameternaam | Beschrijving | Toegestane waarden |
|---|---|---|
**Schaal type** |Het implementatietype van een enkele virtuele machine met meerdere containers of meerdere virtuele machines in een schaalmodel.|Eén VM of meerdere VM'S |
**VM-schijf type** |Het type opslag dat elk van de geïmplementeerde knooppunten ondersteunt. <br/> Ga voor meer informatie over de beschik bare schijf typen naar [een schijf type selecteren](../../virtual-machines/windows/disks-types.md).|Standard - SSD <br/> Premium SSD |

### <a name="multiple-vm-deployment-additional-settings"></a>Implementatie van meerdere VM'S (extra instellingen)

![Infrastructuur instellingen voor meerdere VM-implementaties](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Parameternaam | Beschrijving | Toegestane waarden |
|---|---|---|
**Aantal orderer knooppunten** |Het aantal knooppunten dat transacties in een blok ordent (organiseert). <br />Ga naar de Hyperledger- [documentatie](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) voor meer informatie over de bestel service |1-4 |
**Grootte van de virtuele machine van de orderer-knoop punt** |De grootte van de virtuele machine die wordt gebruikt voor orderknooppunten in het netwerk|Standard BS,<br />Standard DS,<br />Standard FS |
**Aantal peer knooppunten** | Knooppunten die het eigendom zijn van consortiumleden die transacties uitvoeren en de status en een kopie van het grootboek bijhouden.<br />Ga naar de Hyperledger- [documentatie](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html)voor meer informatie over de order service.|1-4 |
**Status persistentie knoop punt** |De persistentie-engine die wordt gebruikt door de peerknooppunten. U kunt deze engine per peerknooppunt configureren. Bekijk de details hieronder voor meerdere peerknooppunten.|CouchDB <br />LevelDB |
**Grootte van virtuele machine van peer-knoop punt** |De grootte van de virtuele machine die wordt gebruikt voor alle knoop punten in het netwerk|Standard BS,<br />Standard DS,<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>Configuratie met meerdere peer-knooppunten

Met deze sjabloon kunt u uw persistentie-engine per peerknooppunt kiezen. Als u bijvoorbeeld drie peerknooppunten hebt, kunt u CouchDB op één knooppunt en LevelDB op de andere twee gebruiken.

![Configuratie met meerdere peer-knooppunten](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Selecteer **OK**.

### <a name="deploy"></a>Implementeren

Controleer in **samen vatting**de ingevoerde invoer en voer een basis validatie vóór de implementatie uit.

![Samenvatting](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Bekijk juridische en privacy-voor waarden en selecteer **kopen** om te implementeren. Afhankelijk van het aantal VM's dat wordt gebruikt, kan de implementatietijd variëren van enkele minuten tot tientallen minuten.

## <a name="next-steps"></a>Volgende stappen

U bent nu klaar om u te richten op de ontwikkeling van applicaties en chaincode op basis van uw Hyperledger consortium blockchain-netwerk.
