---
title: Opmerkingen bij de release van verkoop inzichten
description: Bevat informatie over wijzigingen in de functie voor het verkrijgen van verkopers.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: pabutler
ms.openlocfilehash: 4f7e461241793b27d838c04311509f43932df291
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808958"
---
# <a name="seller-insights-release-notes"></a>Opmerkingen bij de release van verkoop inzichten 

(Release datum: 1 maart 2019)

Dit artikel bevat informatie over wijzigingen in de functie voor het verkrijgen van de verkoper in de [Cloud Partner-Portal](https://cloudpartner.azure.com/#insights).

## <a name="release-highlights-for-march-1-2019"></a>Release-hooglichten voor 1 maart 2019

* *Trend van klanten* toegevoegd aan samen vatting
* De *belangrijkste vijf klanten* in samen vatting geven alle Azure-abonnementen weer die een klant heeft
* Genormaliseerde trend van het *gebruik & actieve orders trend* bij een overzicht dat is verplaatst onder *maandelijkse bestellingen in één oogopslag*
* *Rapport van uitbetalings afstemming* bijgewerkt
* De *belangrijkste vijf klanten* op de betaling geven alle Azure-abonnementen weer die een klant heeft
* Het *gebruiks rapport* is bijgewerkt met de klant-id
* *Klant dienst* in orders & gebruik alle Azure-abonnementen die een klant heeft


(Release datum: 28 juli 2018)

## <a name="release-highlights-for-july-28-2018"></a>Release-hooglichten voor 28 juli 2018


-   De *geschatte prijzen* bieden een overzicht van de kosten van klanten met gevolgen voor de valuta conversie.
-   *Geprognosticeerde uitbetalingen* bieden een eerdere weer gave in potentiële uitbetalingen.
-  *Gebruiks referentie-id's* bieden gegevens kwaliteit tussen het gebruik van de klant en de orders met uitbetalingen
-   *Gebruik in een dagelijks graan* biedt meer dieper en beter inzicht in het gebruik van de klant.


### <a name="changes-to-data-structure-and-taxonomy"></a>Wijzigingen in de gegevens structuur en de taxonomie

De volgende tabel bevat de metrische gegevens die zijn toegevoegd of ingrijpend zijn gewijzigd met deze release. 

| **Nieuwe term**                   |    **Definitie**                                                             |
|--------------------------------|  ---------------------------------------------------------------------------- |
| Prijs (CC)                     | Prijs voor een gebruiks eenheid voor een bepaalde SKU (in de valuta van de klant).       |
| Geschatte berekende toeslag (CC) | Geschatte berekende kosten voor het aantal gebruiks eenheden voor een bepaalde SKU (in de valuta van de klant). Deze waarde mag niet exact zijn door afronding of Afbrekings fouten.   |
| Uitgever valuta (PC)        | De door de uitgever gewenste valuta voor betaling.                               |
| Geschatte prijs (PC)           | Geschatte prijs voor een eenheid van gebruik voor een bepaalde SKU op basis van deviezen conversie op het datum gebruik wordt berekend (in de valuta van de uitgever). Deze waarde mag niet exact zijn door afronding of Afbrekings fouten.   |
| Geschatte uitgebreide lading (PC) | Geschatte berekende toeslag voor het aantal eenheden van gebruik voor een bepaalde SKU op basis van deviezen conversie op het datum gebruik wordt berekend (in de valuta van de uitgever). Deze waarde mag niet exact zijn door afronding of Afbrekings fouten. |
| Geschatte uitbetaling (PC)          | De geschatte betaling voor het aantal gebruiks eenheden voor een bepaalde SKU op basis van deviezen conversie op de datum waarop het gebruik wordt berekend (in de valuta van de uitgever). Deze waarde mag niet exact zijn door afronding of Afbrekings fouten.   |
| Gebruiks referentie                | De id voor een of meer dagen van klant gebruik voor een bepaalde SKU die is gekoppeld aan een vermelding in het uitbetalings rapport. |
|  |  |
