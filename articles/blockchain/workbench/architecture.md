---
title: Azure Blockchain Workbench-architectuur
description: Overzicht van de architectuur van Azure Block Chain Workbench preview en de bijbehorende onderdelen.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: aa972e8ae486d181f0c48df72ec89c925c940451
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324904"
---
# <a name="azure-blockchain-workbench-architecture"></a>Azure Blockchain Workbench-architectuur

Azure Block Chain Workbench preview vereenvoudigt de ontwikkeling van Block Chain toepassingen door een oplossing te bieden met behulp van verschillende Azure-onderdelen. Blockchain Workbench kan worden geïmplementeerd met behulp van een oplossingssjabloon in de Azure Marketplace. De sjabloon stelt u in staat te kiezen voor modules en -onderdelen om te implementeren met inbegrip van een blockchain-stack, type van de clienttoepassing, en ondersteuning voor IoT-integratie. Eenmaal geïmplementeerd biedt Blockchain Workbench toegang tot een web-app, iOS-app, en Android-app.

![Architectuur van Block Chain workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identiteit en verificatie

Via Blockchain Workbench kan een consortium haar ondernemings-id's met behulp van Azure Active Directory (Azure AD) federeren. Workbench genereert nieuwe gebruikersaccounts voor identiteiten in de chain met de ondernemings-id's die zijn opgeslagen in Azure AD. De mapping van identiteiten zorgt voor geverifieerde aanmelding bij client-API's en -toepassingen en maakt gebruik van het verificatiebeleid van organisaties. Workbench biedt ook de mogelijkheid om bedrijfsidentiteiten te koppelen aan specifieke rollen binnen een smart contract. Workbench biedt bovendien een mechanisme om de acties te identificeren die deze rollen kunnen uitvoeren en op welk moment.

Nadat Blockchain Workbench is geïmplementeerd, werken gebruikers met Blockchain Workbench via de clienttoepassingen, een REST-gebaseerde client API of een Messaging API. In alle gevallen moeten interacties worden geverifieerd, via Azure Active Directory (Azure AD) of apparaatspecifieke referenties.

Gebruikers koppelen hun identiteit met Azure AD in een consortium door een e-mailuitnodiging naar deelnemers te sturen op hun e-mailadres. Bij het inloggen worden deze gebruikers geverifieerd met behulp van de naam, het wachtwoord en het toepasselijke beleid. Twee-factor authenticatie van hun organisatie bijvoorbeeld.

Azure AD wordt gebruikt om alle gebruikers te beheren die toegang hebben tot Blockchain Workbench. Elk apparaat dat verbinding maakt met een smart contract, is ook gekoppeld aan Azure AD.

Azure AD wordt ook gebruikt om gebruikers toe te wijzen aan een speciale beheerdersgroep. Gebruikers die aan de beheerdersgroep zijn gekoppeld krijgen toegang tot rechten en acties binnen Blockchain Workbench, waaronder het implementeren van contracten en het verlenen van rechten aan een gebruiker voor toegang tot een contract. Gebruikers buiten deze groep hebben geen toegang tot beheerdersacties.

## <a name="client-applications"></a>Clienttoepassingen

Workbench biedt automatisch gegenereerde clienttoepassingen voor web en mobiel (iOS, Android), die kunnen worden gebruikt voor het valideren, testen en bekijken van blockchain-applicaties. De toepassingsinterface wordt dynamisch gegenereerd op basis van metadata van een smart contract en is geschikt voor elk gebruik. De clienttoepassingen leveren een gebruikersgericht frontend aan alle blockchain-applicaties die door Blockchain Workbench worden gegenereerd. Clienttoepassingen verifiëren gebruikers via Azure Active Directory (Azure AD) en presenteren vervolgens een gebruikerservaring die is afgestemd op de zakelijke context van het smart contract. De gebruikerservaring maakt het mogelijk nieuwe instanties van een smart contract aan te maken door geautoriseerde personen en biedt vervolgens de mogelijkheid om bepaalde soorten transacties uit te voeren op geschikte punten in het bedrijfsproces dat het smart contract vertegenwoordigt.

In de webtoepassing hebben bevoegde gebruikers toegang tot de beheerdersconsole. Deze console is beschikbaar voor gebruikers in de beheerdersgroep in Azure AD en biedt toegang tot de volgende functionaliteit:

* Implementeer door Microsoft verstrekte smarts contracts voor populaire scenario's. Bijvoorbeeld een scenario voor activaoverdracht.
* Upload en implementeer eigen smart contracts.
* Wijs gebruikerstoegang toe aan het smart contract in de context van een specifieke rol.

Zie de voor [beelden van Azure Block Chain Workbench-client toepassingen op github](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile)voor meer informatie.

## <a name="gateway-service-api"></a>Gateway service API

Blockchain Workbench bevat een op REST gebaseerde Gateway service API. Bij het schrijven naar een blockchain genereert en levert de API berichten aan een event broker. Wanneer gegevens worden opgevraagd door de API, worden query's verzonden naar de off-chain SQL-database. De SQL-database bevat een replica van on-chain gegevens en metagegevens die context- en configuratie-informatie bieden voor ondersteunde smart contracts. Query's retourneren de vereiste gegevens uit de off-chainreplica in een indeling die is gebaseerd op de metagegevens voor het contract.

Ontwikkelaars hebben toegang tot de Gateway service API om blockchain-oplossingen te bouwen of te integreren zonder verplicht gebruik van Blockchain Workbench-client-apps.

> [!NOTE]
> Om geverifieerde toegang tot de API in te schakelen, worden twee clienttoepassingen geregistreerd in Azure Active Directory. Azure Active Directory vereist verschillende toepassingsregistraties voor elk type toepassing (native en web). 

## <a name="message-broker-for-incoming-messages"></a>Berichtenbroker voor inkomende berichten

Ontwikkelaars die berichten rechtstreeks naar Blockchain Workbench willen verzenden, kunnen berichten rechtstreeks naar Service Bus verzenden. De berichten-API kan bijvoorbeeld worden gebruikt voor systeem-naar-systeemintegratie of IoT-apparaten.

## <a name="message-broker-for-downstream-consumers"></a>Berichtenbroker voor downstream consumenten

Tijdens de levenscyclus van de toepassing vinden gebeurtenissen plaats. Gebeurtenissen kunnen worden geactiveerd door de Gateway API of in het grootboek. Eventmeldingen kunnen de downstream-code initiëren op basis van de gebeurtenis.

Blockchain Workbench maakt automatisch gebruik van twee soorten gebruikers van gebeurtenissen. Eén consument wordt getriggerd door blockchain-gebeurtenissen om de off-chain SQL-opslag te vullen. De andere consument moet metagegevens vastleggen voor gebeurtenissen die door de API worden gegenereerd met betrekking tot het uploaden en opslaan van documenten.

## <a name="message-consumers"></a>Afnemers van berichten

 Afnemers van berichten halen berichten op uit Service Bus. Het onderliggende eventing-model voor berichtgebruikers maakt uitbreidingen van extra diensten en systemen mogelijk. U kunt bijvoorbeeld ondersteuning toevoegen om CosmosDB te vullen of berichten te evalueren met Azure Streaming Analytics. In de volgende secties worden de berichtgebruikers die deel uitmaken van Blockchain Workbench beschreven.

### <a name="distributed-ledger-consumer"></a>De consument van een gedistribueerd grootboek

Berichten in Distributed ledger technology (DLT) bevatten de metagegevens voor transacties die naar de blockchain moeten worden geschreven. De consument haalt de berichten op en pusht de gegevens naar een transactiebuilder, ondertekenaar en router.

### <a name="database-consumer"></a>Databaseconsument

De databaseconsument neemt berichten van de servicebus en pusht de gegevens naar een gekoppelde database, zoals een SQL-database.

### <a name="storage-consumer"></a>Opslagconsument

De opslagconsument neemt berichten van de Service Bus en pusht gegevens naar een aangesloten opslag. U kunt gehashte documenten bijvoorbeeld opslaan in Azure Storage.

## <a name="transaction-builder-and-signer"></a>Transactiebuilder en ondertekenaar

Als een bericht op de inbound-berichtenmakelaar naar de blockchain moet worden geschreven, wordt dit door de DLT-consument verwerkt. De DLT-Consumer is een service waarmee het bericht wordt opgehaald dat meta gegevens bevat voor een gewenste trans actie om uit te voeren en vervolgens de gegevens naar de *opbouw functie en ondertekenaar van de trans actie*te verzenden. De *trans actie-opbouw functie en ondertekenaar* assembleert een Block Chain-trans actie op basis van de gegevens en de gewenste Block Chain-bestemming. Eenmaal geassembleerd, is de transactie ondertekend. Privésleutels worden opgeslagen in Azure Key Vault.

 Blockchain Workbench haalt de juiste persoonlijke sleutel uit Key Vault en ondertekent de transactie buiten Key Vault. Na ondertekening wordt de transactie verzonden naar transactierouters en grootboeken.

## <a name="transaction-routers-and-ledgers"></a>Transactie-routers en grootboeken

Transactierouters en grootboeken nemen ondertekende transacties en leiden ze naar de juiste blockchain. Momenteel ondersteunt Blockchain Workbench Ethereum als doel-blockchain.

## <a name="dlt-watcher"></a>DLT watcher

Een DLT-watcher (distributed ledger technology) bewaakt gebeurtenissen die zich voordoen op blokketens die zijn verbonden aan Blockchain Workbench.
Gebeurtenissen weerspiegelen informatie die relevant is voor individuen en systemen. Bijvoorbeeld het creëren van nieuwe contractinstanties, uitvoering van transacties en statuswijzigingen. De gebeurtenissen worden vastgelegd en verzonden naar de uitgaande berichtenmakelaar, zodat ze kunnen worden gebruikt door downstream-gebruikers.

De SQL-consument bewaakt bijvoorbeeld gebeurtenissen, gebruikt deze en vult de SQL-database met de ingesloten waarden. De kopie maakt het opnieuw maken van een replica van on-chain data in een off-chain opslag mogelijk.

## <a name="azure-sql-database"></a>Azure SQL-database

De Azure SQL-database die is gekoppeld aan Blockchain Workbench slaat contractdefinities, metadata van de configuratie en een SQL-toegankelijke replica van gegevens die zijn opgeslagen in de blockchain op. Deze gegevens kunnen eenvoudig worden opgevraagd, gevisualiseerd of geanalyseerd door rechtstreeks toegang te krijgen tot de database. Ontwikkelaars en andere gebruikers kunnen de database gebruiken voor rapportage, analyses of andere gegevensgerichte integraties. Gebruikers kunnen bijvoorbeeld transactiegegevens visualiseren met Power BI.

Deze off-chain opslag biedt bedrijven de mogelijkheid om gegevens in SQL op te vragen in plaats van in een blockchain-grootboek. Door de standaardisatie van een standaardschema dat agnostisch is voor stacks van blockchaintechnologie, maakt de off-chainopslag het ook mogelijk om rapporten en andere artefacten te hergebruiken in projecten, scenario's en organisaties.

## <a name="azure-storage"></a>Azure Storage

Azure Storage wordt gebruikt voor het opslaan van contracten en metagegevens die zijn gekoppeld aan contracten.

Van inkooporders en vrachtbrieven tot afbeeldingen in het nieuws en medische beelden, tot video afkomstig uit diverse bronnen, inclusief camera's van de politie en bioscoopfilms, spelen documenten een rol in veel blockchain-gerichte scenario's. Documenten zijn niet geschikt om direct op de blockchain te plaatsen.

Blockchain Workbench ondersteunt de mogelijkheid om documenten of andere media-inhoud toe te voegen aan blockchain-bedrijfslogica. Een hash van het document of de media-inhoud wordt opgeslagen in de blockchain en de daadwerkelijke document- of media-inhoud wordt opgeslagen in Azure Storage. De bijbehorende transactie-informatie wordt aan de inkomende berichtbroker bezorgd, verpakt, ondertekend en naar de blockchain gerouteerd. Dit proces activeert gebeurtenissen die worden gedeeld via de uitgaande berichtenmakelaar. De SQL DB gebruikt deze informatie en verzendt deze naar de database voor later gebruik. Downstream-systemen kunnen deze gebeurtenissen ook consumeren om zo gepast te handelen.

## <a name="monitoring"></a>Bewaking

Workbench biedt logging voor toepassingen met behulp van Application Insights en Azure Monitor. Application Insights wordt gebruikt om alle geregistreerde informatie van Blockchain Workbench op te slaan en bevat foutmeldingen, waarschuwingen en succesvolle bewerkingen. Application Insights kunnen door ontwikkelaars worden gebruikt om problemen met de Blockchain Workbench op te lossen. 

Azure Monitor biedt informatie over de status van de blockchain-netwerk. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench implementeren](../../blockchain-workbench/blockchain-workbench-deploy.md)