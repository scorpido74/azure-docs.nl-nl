---
title: Wat is Azure data share preview?
description: Meer informatie over het eenvoudig en veilig delen van gegevens met meerdere klanten en partners met behulp van de preview-versie van Azure data share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.openlocfilehash: 6ba6e9df7b3fd2992891f4b085581c51fe900744
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169077"
---
# <a name="what-is-azure-data-share-preview"></a>Wat is Azure Data Share Preview?

In de huidige wereld worden gegevens weer gegeven als een belang rijk strategisch activum dat veel organisaties eenvoudig en veilig willen delen met hun klanten en partners. Er zijn veel manieren om dit te doen, met inbegrip van FTP, e-mail, Api's om een paar te noemen. Organisaties kunnen het bijhouden van wie ze hun gegevens hebben gedeeld, gemakkelijk verliezen. Het delen van gegevens via FTP of het door lopen van een eigen API-infra structuur is vaak kostbaar om in te richten en te beheren. Er zijn beheer overhead gekoppeld aan het gebruik van deze methoden voor delen op grote schaal. 

Veel organisaties moeten verantwoordelijk zijn voor de gegevens die ze hebben gedeeld. Afgezien van de verantwoording moeten veel organisaties al hun gegevens delen op een eenvoudige manier kunnen beheren, beheren en bewaken. In de huidige wereld, waar gegevens naar verwachting in een exponentieel tempo zullen blijven groeien, hebben organisaties een eenvoudige manier nodig om big data te delen. Klanten vereisen de meest actuele gegevens om ervoor te zorgen dat ze tijdig inzicht kunnen verkrijgen.

Met Azure data share Preview kunnen organisaties eenvoudig en veilig gegevens delen met meerdere klanten en partners. Met slechts enkele muis klikken kunt u een nieuw gegevens share-account inrichten, gegevens sets toevoegen en uw klanten en partners uitnodigen voor uw gegevens share. Gegevens providers hebben altijd controle over de gegevens die ze hebben gedeeld. Azure data share maakt het eenvoudig om te beheren en te controleren welke gegevens er worden gedeeld, wanneer en door wie. 

Een gegevens provider kan bepalen hoe hun gegevens worden verwerkt door voor waarden voor de gegevens share op te geven. De gegevens verbruiker moet deze voor waarden accepteren voordat ze de gegevens kunnen ontvangen. Gegevens providers kunnen de frequentie opgeven waarmee hun gegevens gebruikers updates ontvangen. De toegang tot nieuwe updates kan op elk gewenst moment worden ingetrokken door de gegevens provider. 

Azure data share helpt inzichten te verbeteren door het eenvoudig te combi neren van gegevens van derden tot uitgebreide analyses en AI-scenario's. Gebruik eenvoudig de hulp middelen van Azure Analytics waarmee u gegevens kunt voorbereiden, verwerken en analyseren met behulp van Azure data share. 

Zowel de gegevens provider als de gegevens verbruiker moeten een Azure-abonnement hebben om gegevens te kunnen delen en ontvangen. Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan.

## <a name="scenarios-for-azure-data-share"></a>Scenario's voor Azure-gegevens share

Een Azure-gegevens share kan worden gebruikt in een aantal verschillende branches. Bijvoorbeeld: een detail handelaar wil een recente verkoop punt van omzet gegevens delen met hun leveranciers. Met behulp van de Azure-gegevens share kan een detail handelaar een gegevens share met een Point of Sales-gegevens instellen voor al hun leveranciers en verkoop per uur of per dag delen. 

Azure data share kan ook worden gebruikt om een gegevens Marketplace te maken voor een specifieke branche. Bijvoorbeeld een overheid of een onderzoek instelling die regel matig geanonimiseerd gegevens deelt over de groei van de populatie met derden. 

Een ander gebruiks voorbeeld voor een Azure-gegevens share is het maken van een Data consortium. Een aantal verschillende onderzoek instituten kan bijvoorbeeld gegevens delen met één vertrouwde hoofd tekst. Gegevens worden geanalyseerd, geaggregeerd of verwerkt met behulp van Azure Analytics-hulpprogram ma's en vervolgens gedeeld met de betrokken partijen. 

## <a name="how-it-works"></a>Het werkt als volgt

Azure data share maakt gebruik van een methode voor het delen op basis van moment opnamen, waarbij gegevens worden verplaatst van het Azure-abonnement en de lands van de gegevens provider in het Azure-abonnement van de gegevens consument. Als gegevens provider kunt u een gegevens share inrichten en ontvangers uitnodigen voor de gegevens share. Gegevens gebruikers ontvangen via e-mail een uitnodiging voor uw gegevens share. Zodra een gegevens verbruiker de uitnodiging heeft geaccepteerd, kunnen ze een volledige moment opname activeren van de gegevens die u hebt gedeeld. Deze gegevens worden ontvangen in het opslag account van de data consumers. Gegevens gebruikers kunnen regel matige, incrementele updates ontvangen van de gegevens die met hen worden gedeeld, zodat ze altijd over de meest recente versie van de gegevens beschikken. 

Gegevens providers kunnen hun klanten incrementele updates bieden voor de gegevens die met hen worden gedeeld via een momentopname schema. Momentopname schema's worden elk uur of dagelijks aangeboden. Wanneer een gegevens verbruiker hun gegevens share accepteert en configureert, kunnen ze zich abonneren op een momentopname schema. Dit is handig in scenario's waarin de gedeelde gegevens regel matig worden bijgewerkt en de gegevens verbruiker de meest actuele gegevens nodig heeft. 

![stroom voor gegevens delen](media/data-share-flow.png)

Wanneer een gegevens verbruiker een gegevens share accepteert, kunnen ze de gegevens in een opslag account van hun keuze ontvangen. Als de gegevens provider bijvoorbeeld gegevens deelt met Azure Blob Storage, kan de gegevens verbruiker deze gegevens in Azure Data Lake Store ontvangen. 

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Met Azure data share kunnen gegevens providers het volgende:

* Gegevens delen van Azure Storage en Azure Data Lake Store met klanten en partners buiten uw organisatie

* Bijhouden wie u uw gegevens hebt gedeeld met

* Hoe vaak uw gegevens gebruikers updates ontvangen van uw gegevens

* Stel uw klanten in staat om de nieuwste versie van uw gegevens naar wens te halen of toe te staan dat ze automatisch incrementele wijzigingen in uw gegevens ontvangen met een interval dat door u is gedefinieerd

Met Azure data share kunnen gegevens gebruikers het volgende doen: 

* Een beschrijving weer geven van het type gegevens dat wordt gedeeld

* Gebruiks voorwaarden voor de gegevens weer geven

* Een uitnodiging voor een Azure-gegevens share accepteren of afwijzen

* Een volledige of incrementele moment opname activeren van een gegevens share die een organisatie met u heeft gedeeld

* Abonneer u op een gegevens share om de nieuwste kopie van de gegevens te ontvangen via een incrementele kopie van de moment opname

* Gegevens die met u zijn gedeeld, accepteren in een Azure Blob Storage-of Azure Data Lake Gen2-account

Alle belang rijke mogelijkheden die hierboven worden vermeld, worden ondersteund via Azure of via REST Api's. Raadpleeg onze referentie documentatie voor meer informatie over het gebruik van Azure data share via REST Api's. 

## <a name="security"></a>Beveiliging

Azure data share maakt gebruik van de onderliggende beveiliging die Azure biedt voor het beveiligen van gegevens in rust en onderweg. Gegevens worden op rest versleuteld, waarbij wordt ondersteund door het onderliggende opslag mechanisme. Gegevens worden ook tijdens de overdracht versleuteld. Meta gegevens over een gegevens share worden ook versleuteld in rust en onderweg. 

Access-besturings elementen kunnen worden ingesteld op het niveau van de Azure-gegevens share-resource om te controleren of het toegankelijk is voor gebruikers die zijn gemachtigd. 

Azure data share maakt gebruik van beheerde identiteiten voor Azure-resources (voorheen bekend als MSIs) voor automatische identiteits beheer in Azure Active Directory. Beheerde identiteiten voor Azure-resources worden gebruikt voor toegang tot de opslag accounts die voor het delen van gegevens zijn bestemd. Er is geen uitwisseling van referenties tussen een gegevens provider en een gegevens verbruiker. Raadpleeg de [pagina beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)voor meer informatie. 


## <a name="supported-regions"></a>Ondersteunde regio’s

Raadpleeg de pagina met [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/) en zoek naar Azure data share voor een lijst met Azure-regio's waarmee de Azure-gegevens share beschikbaar wordt gemaakt. 

De gegevens van een Azure-gegevens share worden niet opgeslagen. De gegevens worden opgeslagen in de onderliggende opslag accounts die worden gedeeld. Als een gegevens producent bijvoorbeeld hun gegevens opslaat in een Azure Data Lake Store account dat zich in VS West bevindt, waar de gegevens zijn opgeslagen. Als ze gegevens delen met een Azure Storage-account dat zich in Europa-west bevindt, worden de gegevens rechtstreeks overgedragen naar het Azure Storage-account dat zich in Europa-west bevindt. 

De Azure data share-service hoeft niet beschikbaar te zijn in uw regio om gebruik te kunnen maken van de service. Als u bijvoorbeeld gegevens hebt opgeslagen in een Azure Storage-account dat zich bevindt in een regio waar Azure data share nog niet beschikbaar is, kunt u de service gebruiken om uw gegevens te delen. 

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelf studie [uw gegevens delen](share-your-data.md) voor meer informatie over het delen van gegevens.
