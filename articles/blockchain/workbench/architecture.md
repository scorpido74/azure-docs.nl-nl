---
title: Architectuur van Azure Blockchain Workbench
description: Overzicht van azure blockchain workbench preview-architectuur en de onderdelen daarvan.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: aa972e8ae486d181f0c48df72ec89c925c940451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324904"
---
# <a name="azure-blockchain-workbench-architecture"></a>Architectuur van Azure Blockchain Workbench

Azure Blockchain Workbench Preview vereenvoudigt de ontwikkeling van blockchain-toepassingen door een oplossing te bieden met behulp van verschillende Azure-componenten. Blockchain Workbench kan worden geïmplementeerd met behulp van een oplossingssjabloon in de Azure Marketplace. Met de sjabloon u modules en componenten kiezen om te implementeren, waaronder blockchainstack, type clienttoepassing en ondersteuning voor IoT-integratie. Eenmaal geïmplementeerd biedt Blockchain Workbench toegang tot een web-app, iOS-app en Android-app.

![Blockchain Workbench-architectuur](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identiteit en verificatie

Met Behulp van Blockchain Workbench kan een consortium zijn bedrijfsidentiteiten beoordelen met Azure Active Directory (Azure AD). Workbench genereert nieuwe gebruikersaccounts voor on-chain identiteiten met de bedrijfsidentiteiten die zijn opgeslagen in Azure AD. De identiteitstoewijzing vergemakkelijkt geverifieerde aanmelding bij client-API's en -toepassingen en maakt gebruik van het verificatiebeleid van organisaties. Workbench biedt ook de mogelijkheid om ondernemingsidentiteiten te koppelen aan specifieke rollen binnen een bepaald slim contract. Daarnaast biedt Workbench ook een mechanisme om te bepalen welke acties die rollen kunnen ondernemen en op welk tijdstip.

Nadat Blockchain Workbench is geïmplementeerd, communiceren gebruikers met Blockchain Workbench via de clienttoepassingen, REST-gebaseerde client-API of Messaging API. In alle gevallen moeten interacties worden geverifieerd, hetzij via Azure Ad (Azure AD) of apparaatspecifieke referenties.

Gebruikers geven hun identiteit aan een consortium Azure AD door een e-mailuitnodiging te sturen naar deelnemers op hun e-mailadres. Wanneer u zich aanmeldt, worden deze gebruikers geverifieerd met de naam, het wachtwoord en het beleid. Bijvoorbeeld, twee-factor authenticatie van hun organisatie.

Azure AD wordt gebruikt om alle gebruikers te beheren die toegang hebben tot Blockchain Workbench. Elk apparaat dat verbinding maakt met een slim contract is ook gekoppeld aan Azure AD.

Azure AD wordt ook gebruikt om gebruikers toe te wijzen aan een speciale beheerdersgroep. Gebruikers die zijn gekoppeld aan de beheerdersgroep krijgen toegang tot rechten en acties binnen Blockchain Workbench, waaronder het implementeren van contracten en het geven van machtigingen aan een gebruiker om toegang te krijgen tot een contract. Gebruikers buiten deze groep hebben geen toegang tot beheerdersacties.

## <a name="client-applications"></a>Clienttoepassingen

Workbench biedt automatisch gegenereerde clienttoepassingen voor web en mobiel (iOS, Android), die kunnen worden gebruikt om blockchain-toepassingen te valideren, testen en bekijken. De applicatie-interface wordt dynamisch gegenereerd op basis van slimme contractmetadata en is geschikt voor elke use case. De clienttoepassingen leveren een gebruikersgerichte front-end aan de volledige blockchain-toepassingen die worden gegenereerd door Blockchain Workbench. Clienttoepassingen verifiëren gebruikers via Azure Ad (Azure AD) en presenteren vervolgens een gebruikerservaring die is afgestemd op de zakelijke context van het slimme contract. De gebruikerservaring maakt het mogelijk om nieuwe slimme contractexemplaren te maken door geautoriseerde personen en presenteert vervolgens de mogelijkheid om bepaalde soorten transacties uit te voeren op geschikte punten in het bedrijfsproces dat het slimme contract vertegenwoordigt.

In de webtoepassing hebben geautoriseerde gebruikers toegang tot de administratorconsole. De console is beschikbaar voor gebruikers in de groep Administrator in Azure AD en biedt toegang tot de volgende functionaliteit:

* Implementeren Microsoft heeft slimme contracten voor populaire scenario's geleverd. Bijvoorbeeld een scenario voor assettransfer.
* Upload en implementeer hun eigen slimme contracten.
* Een gebruiker toegang tot het slimme contract toewijzen in de context van een specifieke rol.

Zie voor meer informatie de [Azure Blockchain Workbench-voorbeeldclienttoepassingen op GitHub](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile).

## <a name="gateway-service-api"></a>API voor gatewayservice

Blockchain Workbench bevat een REST-gebaseerde gatewayservice-API. Bij het schrijven naar een blockchain genereert en levert de API berichten aan een gebeurtenismakelaar. Wanneer gegevens door de API worden opgevraagd, worden query's verzonden naar de SQL-database buiten de keten. De SQL-database bevat een replica van on-chain gegevens en metadata die context- en configuratiegegevens biedt voor ondersteunde slimme contracten. Query's retourneren de vereiste gegevens van de off-chain replica in een indeling die wordt geïnformeerd door de metagegevens voor het contract.

Ontwikkelaars hebben toegang tot de API voor gatewayservices om blockchain-oplossingen te bouwen of te integreren zonder te vertrouwen op Blockchain Workbench-client-apps.

> [!NOTE]
> Om geverifieerde toegang tot de API in te schakelen, worden twee clienttoepassingen geregistreerd in Azure Active Directory. Azure Active Directory vereist afzonderlijke toepassingsregistraties voor elk toepassingstype (native en web). 

## <a name="message-broker-for-incoming-messages"></a>Berichtenmakelaar voor binnenkomende berichten

Ontwikkelaars die berichten rechtstreeks naar Blockchain Workbench willen verzenden, kunnen berichten rechtstreeks naar Service Bus sturen. Berichten-API kan bijvoorbeeld worden gebruikt voor system-to-system-integratie of IoT-apparaten.

## <a name="message-broker-for-downstream-consumers"></a>Berichtenmakelaar voor downstreamconsumenten

Tijdens de levenscyclus van de toepassing treden gebeurtenissen op. Gebeurtenissen kunnen worden geactiveerd door de Gateway API of op het grootboek. Gebeurtenismeldingen kunnen downstreamcode starten op basis van de gebeurtenis.

Blockchain Workbench implementeert automatisch twee soorten eventconsumenten. Eén consument wordt getriggerd door blockchain-gebeurtenissen om de off-chain SQL-winkel te vullen. De andere consument is het vastleggen van metadata voor gebeurtenissen gegenereerd door de API met betrekking tot het uploaden en opslaan van documenten.

## <a name="message-consumers"></a>Bericht consumenten

 Berichten consumenten nemen berichten van Service Bus. Het onderliggende eventingmodel voor berichtconsumenten maakt uitbreidingen van aanvullende diensten en systemen mogelijk. U bijvoorbeeld ondersteuning toevoegen om CosmosDB te vullen of berichten evalueren met Azure Streaming Analytics. In de volgende secties wordt het bericht beschreven dat consumenten hebben opgenomen in Blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Gedistribueerde grootboekconsument

DLT-berichten (Distributed ledger technology) bevatten de metagegevens voor transacties die naar de blockchain moeten worden geschreven. De consument haalt de berichten op en duwt de gegevens naar een transactiebouwer, ondertekenaar en router.

### <a name="database-consumer"></a>Database consument

De databaseconsument neemt berichten over van Service Bus en duwt de gegevens naar een bijgevoegde database, zoals SQL-database.

### <a name="storage-consumer"></a>Opslagconsument

De opslagconsument neemt berichten van Service Bus en duwt gegevens naar een bijgevoegde opslag. Bijvoorbeeld het opslaan van gehashte documenten in Azure Storage.

## <a name="transaction-builder-and-signer"></a>Transactiebouwer en ondertekenaar

Als een bericht op de binnenkomende berichtenmakelaar naar de blockchain moet worden geschreven, wordt het verwerkt door de DLT-consument. De DLT-consument is een service die het bericht met metagegevens voor een gewenste transactie ophaalt om uit te voeren en vervolgens de informatie naar de *transactiebouwer en ondertekenaar*stuurt. De *transactiebouwer en ondertekenaar* assembleert een blockchain-transactie op basis van de gegevens en de gewenste blockchain-bestemming. Eenmaal geassembleerd, wordt de transactie ondertekend. Privésleutels worden opgeslagen in Azure Key Vault.

 Blockchain Workbench haalt de juiste privésleutel op uit Key Vault en ondertekent de transactie buiten Key Vault. Na ondertekening wordt de transactie verzonden naar transactierouters en grootboeken.

## <a name="transaction-routers-and-ledgers"></a>Transactierouters en grootboeken

Transactierouters en grootboeken nemen ondertekende transacties en leiden ze door naar de juiste blockchain. Momenteel ondersteunt Blockchain Workbench Ethereum als zijn doelblockchain.

## <a name="dlt-watcher"></a>DLT-watcher

Een DLT-watcher (Distributed Ledger Technology) houdt gebeurtenissen in de gaten die zich voordoen op blokketens die zijn gekoppeld aan Blockchain Workbench.
Evenementen weerspiegelen informatie die relevant is voor individuen en systemen. Bijvoorbeeld het maken van nieuwe contractexemplaren, de uitvoering van transacties en statuswijzigingen. De gebeurtenissen worden vastgelegd en verzonden naar de uitgaande berichtenmakelaar, zodat ze kunnen worden geconsumeerd door downstream-consumenten.

De SQL-consument controleert bijvoorbeeld gebeurtenissen, verbruikt deze en vult de SQL-database met de opgenomen waarden. De kopie maakt het mogelijk recreatie van een replica van on-chain gegevens in een off-chain winkel.

## <a name="azure-sql-database"></a>Azure SQL-database

De Azure SQL-database die is gekoppeld aan Blockchain Workbench slaat contractdefinities, configuratiemetagegevens en een SQL-toegankelijke replica op van gegevens die in de blockchain zijn opgeslagen. Deze gegevens kunnen eenvoudig worden opgevraagd, gevisualiseerd of geanalyseerd door rechtstreeks toegang te krijgen tot de database. Ontwikkelaars en andere gebruikers kunnen de database gebruiken voor rapportage, analyse of andere gegevensgerichte integraties. Gebruikers kunnen bijvoorbeeld transactiegegevens visualiseren met Power BI.

Deze off-chain opslag biedt de mogelijkheid voor bedrijfsorganisaties om gegevens op te vragen in SQL in plaats van in een blockchain-grootboek. Door te standaardiseren op een standaardschema dat agnost is van blockchain-technologiestacks, maakt de off-chain-opslag ook het hergebruik van rapporten en andere artefacten mogelijk in projecten, scenario's en organisaties.

## <a name="azure-storage"></a>Azure Storage

Azure Storage wordt gebruikt om contracten en metagegevens op te slaan die zijn gekoppeld aan contracten.

Van inkooporders en vrachtbrieven tot beelden die in het nieuws en medische beelden worden gebruikt, tot video's afkomstig uit een continuüm met inbegrip van politiebodycamera's en belangrijke films, documenten spelen een rol in veel blockchain-centrische scenario's. Documenten zijn niet geschikt om direct op de blockchain te plaatsen.

Blockchain Workbench ondersteunt de mogelijkheid om documenten of andere media-inhoud toe te voegen met blockchain-bedrijfslogica. Een hash van het document of media-inhoud wordt opgeslagen in de blockchain en de werkelijke document- of media-inhoud wordt opgeslagen in Azure Storage. De bijbehorende transactie-informatie wordt geleverd aan de binnenkomende berichtenmakelaar, verpakt, ondertekend en doorgestuurd naar de blockchain. Dit proces activeert gebeurtenissen die worden gedeeld via de uitgaande berichtenmakelaar. De SQL DB verbruikt deze informatie en stuurt deze naar de DB voor later query's. Downstreamsystemen kunnen deze gebeurtenissen ook gebruiken om naar behoren te handelen.

## <a name="monitoring"></a>Bewaking

Workbench biedt toepassingslogboekregistratie met toepassingsinzichten en Azure Monitor. Application Insights wordt gebruikt om alle geregistreerde informatie van Blockchain Workbench op te slaan en bevat fouten, waarschuwingen en succesvolle bewerkingen. Application Insights kan door ontwikkelaars worden gebruikt om problemen met Blockchain Workbench te debuggen. 

Azure Monitor biedt informatie over de status van het blockchain-netwerk. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench implementeren](../../blockchain-workbench/blockchain-workbench-deploy.md)