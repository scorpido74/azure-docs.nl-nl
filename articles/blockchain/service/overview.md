---
title: Overzicht van Azure Blockchain Service
description: Overzicht van Azure Blockchain-service
ms.date: 03/12/2020
ms.topic: overview
ms.reviewer: ravastra
ms.openlocfilehash: b9f5deb501fb93327fa5d5cfcfd5bb583ed6135e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79205071"
---
# <a name="what-is-azure-blockchain-service"></a>Wat is Azure Blockchain Service?

Azure Blockchain Service is een volledig beheerde grootboekservice waarmee gebruikers blockchain-netwerken op schaal kunnen laten groeien en beheren in Azure. Door uniforme controle te bieden voor zowel infrastructuurbeheer als blockchain-netwerkbeheer, biedt Azure Blockchain Service:

* Eenvoudige netwerkimplementatie en -bewerkingen
* Ingebouwd consortiumbeheer
* Ontwikkel slimme contracten met vertrouwde ontwikkeltools

Azure Blockchain Service is ontworpen om meerdere grootboekprotocollen te ondersteunen. Momenteel biedt het ondersteuning voor [Quorum](https://www.goquorum.com/) het Ethereum Quorum-grootboek met behulp van het [IBFT-consensusmechanisme.](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)

Deze mogelijkheden vereisen nauwelijks beheer, en alles wordt zonder extra kosten geleverd. U zich richten op app-ontwikkeling en bedrijfslogica in plaats van tijd en resources toe te wijzen aan het beheer van virtuele machines en infrastructuur. Daarnaast u uw applicatie blijven ontwikkelen met de open-source tools en het platform van uw keuze om uw oplossingen te leveren zonder nieuwe vaardigheden te leren.

## <a name="network-deployment-and-operations"></a>Netwerkimplementatie en -bewerkingen

Het implementeren van Azure Blockchain Service gebeurt via de Azure-portal, Azure CLI of via Visual Studio-code met behulp van de Azure Blockchain-extensie. De implementatie wordt vereenvoudigd, inclusief het inrichten van zowel transactie- als validatorknooppunten, Azure Virtual Networks voor beveiligingsisolatie en servicebeheerde opslag.  Bovendien maken gebruikers bij het implementeren van een nieuw blockchain-lid ook een consortium of sluiten ze zich aan.  Met consortiums kunnen meerdere partijen in verschillende Azure-abonnementen veilig met elkaar communiceren op een gedeelde blockchain.  Deze vereenvoudigde implementatie vermindert de implementatie van blockchain-netwerken van dagen tot minuten.

### <a name="performance-and-service-tiers"></a>Prestatie- en servicelagen

Azure Blockchain Service biedt twee servicelagen: *Basic* en *Standard.* Elke laag biedt verschillende prestaties en mogelijkheden om lichtgewicht ontwikkeling te ondersteunen en workloads te testen tot massaal geschaalde productieblockchain-implementaties. Gebruik de *basislaag* voor ontwikkeling, testen en proof of concepts. Gebruik de *standaardlaag* voor implementaties van productiekwaliteit. Beide lagen omvatten ten minste één transactieknooppunt en één validatorknooppunt (Basic) of twee validatorknooppunten (standaard). 

![Prijscategorieën](./media/overview/pricing-tiers.png)

Naast het aanbieden van twee validatorknooppunten biedt de *standaardlaag* 2 *vCores* voor elke transactie en het knooppunt van validator, terwijl de Basic-laag een 1 vCore-configuratie biedt.  Door 2 vCores aan te bieden voor transactie- en validatorknooppunten, kan 1 vCore worden toegewezen aan het Quorum-grootboek, terwijl de resterende 1 vCore kan worden gebruikt voor andere infrastructuurgerelateerde services, wat optimale prestaties voor productieblockchain-workloads garanderen. Zie [Azure Blockchain Service-prijzen](https://azure.microsoft.com/pricing/details/blockchain-service)voor meer informatie over prijsdetails.

### <a name="security-and-maintenance"></a>Beveiliging en onderhoud

Nadat u uw eerste blockchain-lid hebt ingericht, hebt u de mogelijkheid om extra transactieknooppunten toe te voegen aan uw lid.  Transactieknooppunten worden standaard beveiligd via firewallregels en vereisen configuratie voor toegang.  Bovendien versleutelen alle transactieknooppunten gegevens in beweging via TLS.  Er bestaan meerdere opties voor het beveiligen van toegang tot transactieknooppunt, waaronder firewallregels, basisverificatie, toegangssleutels en Azure Active Directory-integratie. Zie [Transactieknooppunten configureren](configure-transaction-nodes.md) en [Azure Active Directory-toegang configureren](configure-aad.md)voor meer informatie.

Als beheerde service zorgt Azure Blockchain Service ervoor dat de knooppunten van uw blockchain-leden worden gepatcht met de nieuwste updates voor host-besturingssysteem en grootboeksoftwarestack, geconfigureerd voor hoge beschikbaarheid (alleen standaardniveau), waardoor veel van de DevOps worden geëlimineerd vereist voor traditionele IaaS blockchain-knooppunten.  Zie [ondersteunde Azure Blockchain Service-grootboekversies](ledger-versions.md)voor meer informatie over patchen en updates.

### <a name="monitoring-and-logging"></a>Bewaking en registratie

Daarnaast biedt Azure Blockchain Service uitgebreide statistieken via Azure Monitor Service en biedt het inzicht in het CPU-, geheugen- en opslaggebruik van knooppunten.  Azure Monitor biedt ook nuttige inzichten in blockchain-netwerkactiviteiten, zoals transacties en -blokkeringen, transactiewachtrijdiepte en actieve verbindingen.  Statistieken kunnen worden aangepast om weergaven te bieden van de inzichten die belangrijk zijn voor uw blockchain-toepassing.  Bovendien kunnen drempels worden gedefinieerd door middel van waarschuwingen waarmee gebruikers acties kunnen activeren, zoals het verzenden van een e-mail of sms, het uitvoeren van een Logic App, Azure-functie of het verzenden naar een aangepaste webhook.

![Metrische gegevens](./media/overview/metrics.png)

Via Azure Log Analytics kunnen gebruikers logboeken weergeven die betrekking hebben op het quorumgrootboek of andere belangrijke informatie, zoals pogingen tot verbindingen met de transactieknooppunten.

## <a name="built-in-consortium-management"></a>Ingebouwd consortiumbeheer

Bij het implementeren van uw eerste blockchain-lid sluit u zich aan bij of maakt u een nieuw consortium.  Een consortium is een logische groep die wordt gebruikt om de governance en connectiviteit te beheren tussen blockchain-leden die transacties voeren in een proces met meerdere partijen.  Azure Blockchain Service biedt ingebouwde governance-controles via vooraf gedefinieerde slimme contracten, die bepalen welke acties leden in het consortium kunnen ondernemen.  Deze governance-controles kunnen zo nodig worden aangepast door de beheerder van het consortium. Wanneer u een nieuw consortium maakt, is uw blockchain-lid de standaardbeheerder van het consortium, waardoor u andere partijen uitnodigen om zich bij uw consortium aan te sluiten.  U alleen lid worden van een consortium als u eerder bent uitgenodigd.  Wanneer je lid wordt van een consortium, is je blockchain-lid onderworpen aan de governance-controles die zijn ingevoerd door de beheerder van het consortium.

![Consortiummanagement](./media/overview/consortium.png)

Consortiumbeheeracties zoals het toevoegen en verwijderen van leden uit een consortium zijn toegankelijk via PowerShell en een REST API. U een consortium programmatisch beheren met behulp van algemene interfaces in plaats van slimme contracten op basis van soliditeit te wijzigen en in te dienen. Zie voor meer informatie [consortiummanagement](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Ontwikkelen met behulp van vertrouwde ontwikkelprogramma's

Op basis van het open-source Quorum Ethereum-grootboek u toepassingen voor Azure Blockchain Service op dezelfde manier ontwikkelen als voor bestaande Ethereum-toepassingen. In samenwerking met toonaangevende branchepartners stelt de Azure Blockchain Development Kit Visual Studio Code-extensie ontwikkelaars in staat om gebruik te maken van bekende tools zoals Truffle Suite om slimme contracten te bouwen. Met de Azure Blockchain Development Kit-extensie kunnen ontwikkelaars uw slimme contracten allemaal vanuit één IDE maken of verbinden met en bestaande consortiums maken, zodat u uw slimme contracten allemaal vanuit één IDE bouwen en implementeren. Met de Azure Blockchain Visual Studio Code-extensie u een bestaand consortium maken of verbinden, zodat u uw slimme contracten allemaal vanuit één IDE bouwen en implementeren. Zie [Azure Blockchain Development Kit in de VS-codemarkt](https://aka.ms/vscodebcextension) en de [gebruikershandleiding azure blockchain-ontwikkelingkit](https://aka.ms/vscodebcextensionwiki)voor meer informatie.

## <a name="publish-blockchain-data"></a>Blockchain-gegevens publiceren

Blockchain Data Manager voor Azure Blockchain Service legt, transformeert en levert Azure Blockchain Service-transactiegegevens aan Azure Event Grid-onderwerpen die betrouwbare en schaalbare blockchain-grootboekintegratie met Azure-services bieden. U Blockchain Data Manager gebruiken om off-chain applicaties en datastores te integreren. Zie [Blockchain Data Manager voor Azure Blockchain Service voor](data-manager.md)meer informatie.

## <a name="support-and-feedback"></a>Ondersteuning en feedback

Ga voor Azure Blockchain-nieuws naar het [Azure Blockchain-blog](https://azure.microsoft.com/blog/topics/blockchain/) om op de hoogte te blijven van blockchain-serviceaanbiedingen en -informatie van het Azure Blockchain-engineeringteam.

Om productfeedback te geven of om nieuwe functies aan te vragen, u stemmen voor een idee via het [Azure-feedbackforum voor blockchain.](https://aka.ms/blockchainuservoice)

### <a name="community-support"></a>Ondersteuning voor community

Neem contact op met Microsoft-technici en Azure Blockchain-community-experts.

* [Azure Blockchain MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/AzureBlockchainService)

## <a name="next-steps"></a>Volgende stappen

Probeer snel aan de slag of ontdek meer details uit deze bronnen om aan de slag te gaan.
* [Een blockchain-lid maken met behulp van de Azure-portal](create-member.md) of [een blockchain-lid maken met Azure CLI](create-member-cli.md)
* Zie de [prijspagina](https://azure.microsoft.com/pricing/details/blockchain-service)voor kostenvergelijkingen en rekenmachines.
* Bouw uw eerste app met de [Azure Blockchain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* Gebruikershandleiding [azure](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) Blockchain VSCode Extension
