---
title: Overzicht van de Azure Block Chain-Service
description: Overzicht van de Azure Block Chain-Service
ms.date: 11/21/2019
ms.topic: overview
ms.reviewer: janders
ms.openlocfilehash: 02cc955822987e3be6f5a2184fc49e5510b29626
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455908"
---
# <a name="what-is-azure-blockchain-service"></a>Wat is Azure Blockchain Service?

De Azure Block Chain-service is een volledig beheerde grootboek service waarmee gebruikers de mogelijkheid hebben om Block chain netwerken op schaal te verg Roten of te bedienen in Azure. Door Unified Control te bieden voor zowel infrastructuur beheer als Block Chain Network governance, biedt de Azure Block Chain-service de volgende mogelijkheden:

* Eenvoudige netwerk implementatie en-bewerkingen
* Ingebouwd consortiumbeheer
* Slimme contracten ontwikkelen met vertrouwde ontwikkel Programma's

De Azure Block Chain-service is ontworpen om meerdere grootboek protocollen te ondersteunen. Dit biedt momenteel ondersteuning voor het Ethereum- [quorum](https://www.jpmorgan.com/Quorum) grootboek met behulp van het [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) consensus-mechanisme.

Deze mogelijkheden vereisen nauwelijks beheer, en alles wordt zonder extra kosten geleverd. U kunt zich richten op het ontwikkelen van apps en bedrijfs logica in plaats van het toewijzen van tijd en resources voor het beheren van virtuele machines en infra structuur. Daarnaast kunt u uw toepassing blijven ontwikkelen met de open source-hulpprogram ma's en het platform van uw keuze om uw oplossingen te leveren zonder dat u nieuwe vaardig heden hoeft te leren.

## <a name="network-deployment-and-operations"></a>Netwerk implementatie en-bewerkingen

De implementatie van de Azure Block Chain-service wordt uitgevoerd via de Azure Portal, Azure CLI of Visual Studio code met behulp van de Azure Block Chain-extensie. Implementatie is vereenvoudigd, inclusief het inrichten van zowel trans actie-als validatie knooppunten, virtuele Azure-netwerken voor beveiligings isolatie en door service beheerde opslag.  Daarnaast maken gebruikers bij het implementeren van een nieuw Block Chain-lid ook een consortium.  Met consortiums kunnen meerdere partijen in verschillende Azure-abonnementen veilig communiceren met elkaar op een gedeelde Block chain.  Deze vereenvoudigde implementatie reduceert de Block chain-netwerk implementatie van dagen naar minuten.

### <a name="performance-and-service-tiers"></a>Prestatie-en service lagen

De Azure Block Chain-service biedt twee service lagen: *Basic* en *Standard*. Elke laag biedt verschillende prestaties en mogelijkheden voor het ondersteunen van de werk belasting voor lichte ontwikkeling en tests tot een enorme schaal bare productie Block Chain-implementatie. Beide lagen bevatten ten minste één transactie knooppunt en één validator-knoop punt (Basic) of twee validatie knooppunten (standaard).

![Prijscategorieën](./media/overview/pricing-tiers.png)

De laag *standaard* biedt naast twee validator-knoop punten 2 *vCores* voor elke trans actie en een validatie knooppunt, terwijl de Basic-laag een 1 vCore-configuratie biedt.  Door 2 vCores voor trans actie-en validatie knooppunten aan te bieden, kan 1 vCore worden toegewezen aan het quorum grootboek terwijl de resterende 1 vCore kan worden gebruikt voor andere infrastructuur services, waardoor de prestaties van werk belastingen van productie Block Chain optimaal zijn. Zie [prijzen voor Azure Block chain-services](https://azure.microsoft.com/pricing/details/blockchain-service)voor meer informatie over prijs informatie.

### <a name="security-and-maintenance"></a>Beveiliging en onderhoud

Nadat u uw eerste Block Chain-lid hebt ingericht, hebt u de mogelijkheid om extra transactie knooppunten aan uw lid toe te voegen.  Transactie knooppunten worden standaard beveiligd via firewall regels en vereisen configuratie voor toegang.  Daarnaast versleutelen alle transactie knooppunten gegevens in beweging via TLS.  Er bestaan meerdere opties voor het beveiligen van toegang tot transactie knooppunten, zoals firewall regels, basis verificatie, toegangs sleutels en integratie van Azure Active Directory. Zie [Trans Action nodes configureren](configure-transaction-nodes.md) en [Azure Active Directory toegang configureren](configure-aad.md)voor meer informatie.

Als beheerde service zorgt Azure Block Chain-service ervoor dat de knoop punten van uw Block Chain-lid worden bijgewerkt met het nieuwste besturings systeem van de host en de software stack-updates van het groot boek, geconfigureerd voor hoge Beschik baarheid (alleen Standard-laag), waardoor veel van de DevOps vereist voor traditionele IaaS Block Chain-knoop punten.  Zie [ondersteunde Azure Block Chain Service Ledger-versies](ledger-versions.md)voor meer informatie over patches en updates.

### <a name="monitoring-and-logging"></a>Bewaking en registratie

Daarnaast biedt de Azure Block Chain-service uitgebreide metrische gegevens via Azure Monitor service die inzichten biedt in het CPU-, geheugen-en opslag gebruik van knoop punten.  Azure Monitor biedt ook nuttige inzichten in Block chain-netwerk activiteit, zoals trans acties en blokken ook niet gebruikt, de transactie wachtrij diepte en actieve verbindingen.  Metrische gegevens kunnen worden aangepast om weer gaven te bieden in de inzichten die belang rijk zijn voor uw Block Chain-toepassing.  Daarnaast kunnen drempel waarden worden gedefinieerd via waarschuwingen, waardoor gebruikers acties activeren, zoals het verzenden van een e-mail of SMS-bericht, het uitvoeren van een logische app, een Azure function of het verzenden naar een aangepaste webhook.

![Metrische gegevens](./media/overview/metrics.png)

Via Azure Log Analytics kunnen gebruikers logboeken weer geven die betrekking hebben op het quorum grootboek of andere belang rijke informatie, zoals pogingen tot verbinding met de transactie knooppunten.

## <a name="built-in-consortium-management"></a>Ingebouwd consortiumbeheer

Wanneer u uw eerste Block Chain-lid implementeert, moet u lid worden van of een nieuw consortium maken.  Een consortium is een logische groep die wordt gebruikt voor het beheren van het bestuur en de connectiviteit tussen Block Chain-leden die een proces voor meerdere partijen verwerken.  De Azure Block Chain-service biedt ingebouwde besturings elementen voor beheer via vooraf gedefinieerde slimme contracten die bepalen welke acties leden van het consortium kunnen ondernemen.  Deze governance-besturings elementen kunnen zo nodig worden aangepast door de beheerder van het consortium. Wanneer u een nieuw consortium maakt, is uw Block Chain-lid de standaard beheerder van het consortium, waardoor andere partijen kunnen uitnodigen om deel te nemen aan uw consortium.  U kunt alleen lid worden van een consortium als u eerder bent uitgenodigd.  Wanneer u een consortium samenvoegt, is uw Block Chain-lid onderhevig aan de governance-besturings elementen die door de beheerder van het consortium worden geplaatst.

![Consortium beheer](./media/overview/consortium.png)

Acties voor consortium beheer, zoals het toevoegen en verwijderen van leden uit een consortium, kunnen worden geopend via Power shell en een REST API. U kunt een consortium programmatisch beheren met behulp van gemeen schappelijke interfaces, in plaats van op op volheid gebaseerde slimme contracten te wijzigen en te verzenden. Zie [consortium Management](consortium.md)voor meer informatie.

## <a name="develop-using-familiar-development-tools"></a>Ontwikkelen met behulp van vertrouwde ontwikkel hulpprogramma's

Op basis van het open-source quorum Ethereum, kunt u toepassingen voor de Azure Block Chain-service op dezelfde manier ontwikkelen als voor bestaande Ethereum-toepassingen. Met de Visual Studio code extension van Azure Block Chain Development Kit, kunnen ontwikkel aars werken met toonaangevende partners, zoals Truffle Suite, om slimme contracten te bouwen. Ontwikkel aars kunnen met behulp van de Azure Block Chain Development Kit-uitbrei ding een bestaand consortium maken of verbinden, zodat u uw slimme contracten vanuit één IDE kunt bouwen en implementeren. Met behulp van de Azure Block Chain Visual Studio code-extensie kunt u een bestaand consortium maken of verbinden zodat u uw slimme contracten vanuit één IDE kunt bouwen en implementeren. Zie voor meer informatie [Azure Block Chain Development Kit in de VS code Marketplace](https://aka.ms/vscodebcextension) en de [Gebruikers handleiding voor de Azure Block Chain Development Kit](https://aka.ms/vscodebcextensionwiki).

## <a name="publish-blockchain-data"></a>Block Chain-gegevens publiceren

Block Chain Data Manager voor Azure Block Chain service registreert, transformeert en levert informatie over Azure Block Chain-service transacties aan Azure Event Grid onderwerpen met betrouw bare en schaal bare Block Chain grootboek integratie met Azure-Services. U kunt Block Chain Data Manager gebruiken om toepassingen en gegevens archieven uit de keten te integreren. Zie [block chain Data Manager voor de Azure Block Chain-Service](data-manager.md)voor meer informatie.

## <a name="support-and-feedback"></a>Ondersteuning en feedback

Hebt u hulp nodig of hebt u feedback?

* Ga naar de [Azure Block Chain-blog](https://azure.microsoft.com/blog/topics/blockchain/), het [micro soft tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)en het [Azure Block Chain-Forum](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Als u feedback wilt geven of een nieuwe functie wilt aanvragen, maakt u een vermelding via [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan, kunt u een Snelstartgids of meer informatie over deze resources vinden.
* [Een Block Chain-lid maken met behulp van de Azure Portal](create-member.md) of [een Block Chain-lid maken met behulp van Azure cli](create-member-cli.md)
* Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/blockchain-service)voor kosten vergelijkingen en reken machines.
* Uw eerste app bouwen met behulp van de [Azure Block Chain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* [Gebruikers handleiding](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) voor de Azure Block Chain VSCode-extensie
