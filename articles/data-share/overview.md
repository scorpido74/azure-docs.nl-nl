---
title: Wat is Azure Data Share?
description: Meer informatie over het eenvoudig en veilig delen van gegevens met meerdere klanten en partners met Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.openlocfilehash: 363feda1409d2bb54e60d1b2168cba38f2a8a41c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77621857"
---
# <a name="what-is-azure-data-share"></a>Wat is Azure Data Share?

In de wereld van vandaag worden gegevens gezien als een belangrijke strategische troef die veel organisaties eenvoudig en veilig moeten delen met hun klanten en partners. Er zijn vele manieren waarop klanten dit vandaag doen, onder andere via FTP, e-mail, API's om er een paar te noemen. Organisaties kunnen gemakkelijk uit het oog verliezen met wie ze hun gegevens hebben gedeeld. Het delen van gegevens via FTP of door het opstaan van hun eigen API-infrastructuur is vaak duur om in te richten en te beheren. Er is beheer overhead in verband met het gebruik van deze methoden van delen op grote schaal. 

Veel organisaties moeten verantwoordelijk zijn voor de gegevens die ze hebben gedeeld. Naast verantwoording willen veel organisaties al hun gegevensdelen op een eenvoudige manier kunnen controleren, beheren en bewaken. In de wereld van vandaag, waar gegevens naar verwachting exponentieel zullen blijven groeien, hebben organisaties een eenvoudige manier nodig om big data te delen. Klanten vragen de meest up-to-date gegevens om ervoor te zorgen dat ze tijdig inzichten kunnen afleiden.

Azure Data Share stelt organisaties in staat om eenvoudig en veilig gegevens te delen met meerdere klanten en partners. In slechts een paar klikken u een nieuw account voor gegevensshare inrichten, gegevenssets toevoegen en uw klanten en partners uitnodigen voor uw gegevensaandeel. Gegevensproviders hebben altijd de controle over de gegevens die ze hebben gedeeld. Azure Data Share maakt het eenvoudig om te beheren en te controleren welke gegevens zijn gedeeld, wanneer en door wie. 

Een gegevensprovider kan de controle houden over hoe hun gegevens worden verwerkt door gebruiksvoorwaarden voor hun gegevensaandeel op te geven. De gegevensgebruiker moet deze voorwaarden accepteren voordat deze de gegevens kan ontvangen. Gegevensproviders kunnen de frequentie opgeven waarmee hun gegevensgebruikers updates ontvangen. De toegang tot nieuwe updates kan op elk moment door de gegevensprovider worden ingetrokken. 

Azure Data Share helpt inzichten te verbeteren door het eenvoudig te maken om gegevens van derden te combineren om analytics en AI-scenario's te verrijken. Gebruik eenvoudig de kracht van Azure-analysetools om gegevens die worden gedeeld met Azure Data Share voor te bereiden, te verwerken en te analyseren. 

Zowel de gegevensleverancier als de gegevensverbruiker moeten een Azure-abonnement hebben om gegevens te delen en te ontvangen. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/)aan.

## <a name="scenarios-for-azure-data-share"></a>Scenario's voor Azure-gegevensshare

Azure Data Share kan worden gebruikt in een aantal verschillende bedrijfstakken. Een detailhandelaar kan bijvoorbeeld recente verkoopgegevens delen met zijn leveranciers. Met Azure Data Share kan een detailhandelaar een gegevensaandeel instellen met verkooppunten voor al zijn leveranciers en de verkoop per uur of per dag delen. 

Azure Data Share kan ook worden gebruikt om een datamarktplaats voor een specifieke branche op te zetten. Bijvoorbeeld een overheid of een onderzoeksinstelling die regelmatig geanonimiseerde gegevens over bevolkingsgroei deelt met derden. 

Een andere use case voor Azure Data Share is het opzetten van een dataconsortium. Een aantal verschillende onderzoeksinstellingen kan bijvoorbeeld gegevens delen met één vertrouwd orgaan. Gegevens worden geanalyseerd, samengevoegd of verwerkt met Azure-analysetools en vervolgens gedeeld met geïnteresseerde partijen. 

## <a name="how-it-works"></a>Hoe werkt het?

Azure Data Share biedt momenteel delen op basis van momentopnamen en delen op locatie. 

Bij het delen op basis van momentopnamen worden gegevens verplaatst van het Azure-abonnement van de gegevensprovider en worden ze in het Azure-abonnement van de gegevensconsument verwerkt. Als gegevensprovider indient u een gegevensaandeel en nodigt u ontvangers uit voor het delen van gegevens. Gegevensconsumenten ontvangen een uitnodiging voor uw gegevensaandeel via e-mail. Zodra een gegevensconsument de uitnodiging accepteert, kan deze een volledige momentopname van de gegevens die met hem worden gedeeld, activeren. Deze gegevens worden ontvangen in de opslagaccount van de gegevensconsumenten. Gegevensconsumenten kunnen regelmatig, incrementele updates ontvangen van de gegevens die met hen worden gedeeld, zodat ze altijd de nieuwste versie van de gegevens hebben. 

Gegevensproviders kunnen hun gegevens consumenten incrementele updates voor de gegevens gedeeld met hen door middel van een snapshot schema. Snapshot schema's worden aangeboden op een uur- of dagbasis. Wanneer een gegevensconsument zijn gegevensaandeel accepteert en configureert, kan hij zich abonneren op een momentopnameschema. Dit is gunstig in scenario's waarin de gedeelde gegevens regelmatig worden bijgewerkt en de gegevensdie de consument de meest up-to-date gegevens nodig heeft. 

![gegevenssharestroom](media/data-share-flow.png)

Wanneer een gegevensconsument een gegevensaandeel accepteert, kunnen ze de gegevens ontvangen in een gegevensarchief van zijn of haar keuze. Als de gegevensprovider bijvoorbeeld gegevens deelt met Azure Blob Storage, kan de gegevensconsument deze gegevens ontvangen in Azure Data Lake Store. Als de gegevensprovider gegevens uit een Azure SQL Data Warehouse deelt, kan de gegevensconsument kiezen of hij de gegevens wil ontvangen in een Azure Data Lake Store, een Azure SQL Database of een Azure SQL Data Warehouse. In het geval van delen vanuit SQL-gebaseerde bronnen kan de gegevensconsument ook kiezen of hij gegevens ontvangt in parket of csv. 

Met het delen op de plaats kunnen gegevensproviders gegevens delen waar deze zich bevinden zonder de gegevens te kopiëren. Nadat de relatie met delen is tot stand gekomen via de uitnodigingsstroom, wordt een symbolische koppeling gemaakt tussen het brongegevensarchief van de gegevensprovider en het doelgegevensarchief van de gegevensconsument. Gegevensconsumenten kunnen de gegevens in realtime lezen en opvragen met behulp van een eigen gegevensarchief. Wijzigingen in het brongegevensarchief zijn onmiddellijk beschikbaar voor de gegevensconsument. In-place sharing is momenteel in preview voor Azure Data Explorer.

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Azure Data Share stelt gegevensproviders in staat om:

* Gegevens uit de lijst met [ondersteunde gegevensarchieven delen](supported-data-stores.md) met klanten en partners buiten uw organisatie

* Bijhouden met wie u uw gegevens hebt gedeeld

* Keuze van momentopname of in-place delen

* Hoe vaak uw gegevensconsumenten updates ontvangen voor uw gegevens

* Laat uw klanten de nieuwste versie van uw gegevens naar behoefte opvragen, of hen toestaan om automatisch incrementele wijzigingen in uw gegevens te ontvangen met een door u gedefinieerd interval

Azure Data Share stelt gegevensconsumenten in staat om: 

* Een beschrijving weergeven van het type gegevens dat wordt gedeeld

* Gebruiksvoorwaarden voor de gegevens weergeven

* Een uitnodiging voor Azure Data Share accepteren of weigeren

* Accepteer gegevens die met u worden gedeeld in een [ondersteund gegevensarchief.](supported-data-stores.md)

* Een volledige of incrementele momentopname van een gegevensshare activeren die een organisatie met u heeft gedeeld

* Abonneer u op een gegevensaandeel om de nieuwste kopie van de gegevens te ontvangen via incrementele momentopname

Alle bovenstaande belangrijke mogelijkheden worden ondersteund via de Azure-portal of via REST API's. Bekijk onze referentiedocumentatie voor meer informatie over het gebruik van Azure Data Share via REST API's. 

## <a name="security"></a>Beveiliging

Azure Data Share maakt gebruik van de onderliggende beveiliging die Azure biedt om gegevens in rust en onderweg te beschermen. Gegevens worden in rust versleuteld, waarbij het onderliggende gegevensarchief wordt ondersteund. Gegevens worden ook versleuteld tijdens het transport. Metadata over een gegevensaandeel wordt ook versleuteld in rust en onderweg. 

Toegangsbesturingselementen kunnen worden ingesteld op het resourceniveau azure data share om ervoor te zorgen dat deze worden geopend door de geautoriseerden. 

Azure Data Share maakt gebruik van beheerde identiteiten voor Azure Resources (voorheen bekend als MSI's) voor automatisch identiteitsbeheer in Azure Active Directory. Beheerde identiteiten voor Azure Resources worden gebruikt voor toegang tot de gegevensopslag die worden gebruikt voor het delen van gegevens. Er is geen uitwisseling van referenties tussen een gegevensprovider en een gegevensverbruiker. Raadpleeg de [pagina Beheerde identiteiten voor Azure Resources voor](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)meer informatie. 


## <a name="supported-regions"></a>Ondersteunde regio’s

Raadpleeg de [producten die beschikbaar zijn op regiopagina](https://azure.microsoft.com/global-infrastructure/services/?products=data-share) en zoek naar Azure Data Share voor een lijst met Azure-regio's die Azure Data Share beschikbaar maken. 

Azure Data Share slaat geen kopie van de gegevens zelf op. De gegevens worden opgeslagen in het onderliggende gegevensarchief dat wordt gedeeld. Als een gegevensproducent bijvoorbeeld zijn gegevens opslaat in een Azure Data Lake Store-account in West-VS, worden de gegevens daar opgeslagen. Als ze gegevens delen met een Azure Storage-account in West-Europa via snapshot, worden de gegevens meestal rechtstreeks overgebracht naar het Azure Storage-account in West-Europa.

De Azure Data Share-service hoeft niet beschikbaar te zijn in uw regio om gebruik te kunnen maken van de service. Als u bijvoorbeeld gegevens hebt opgeslagen in een Azure Storage-account in een regio waar Azure Data Share nog niet beschikbaar is, u nog steeds gebruikmaken van de service om uw gegevens te delen. 

## <a name="next-steps"></a>Volgende stappen

Ga door met het [delen van uw gegevenszelfstudie](share-your-data.md) om te leren hoe u gegevens delen.
