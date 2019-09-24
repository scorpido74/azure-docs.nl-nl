---
title: Incidenten en waarschuwingen onderzoeken in Azure Security Center | Microsoft Docs
description: In dit document wordt uitgelegd hoe u de onderzoeksfunctie van Azure Security Center gebruikt voor het onderzoeken van beveiligingsincidenten en waarschuwingen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: a8e894a9-8781-4749-ae8f-8c8e01658566
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: memildin
ms.openlocfilehash: 32cc4b01d68ee7a15ae7cfa2259d234cbcd143c4
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202062"
---
# <a name="investigate-incidents-and-alerts-in-azure-security-center-retired"></a>Incidenten en waarschuwingen onderzoeken in Azure Security Center (buiten gebruik gesteld)
Dit document helpt u bij het gebruik van de functie voor onderzoek (preview) in Azure Security Center om beveiligings incidenten en-waarschuwingen te onderzoeken.

> [!NOTE]
> De ervaring voor het onderzoeken van waarschuwingen en incidenten (preview) is op 31 juli 2019 ingetrokken. Zie voor meer informatie en alternatieve Services de [buiten gebruiks telling van Security Center-functies (2019 juli)](security-center-features-retirement-july2019.md#menu_investigate).

## <a name="what-is-investigation-in-security-center"></a>Wat is onderzoek in Security Center?
Met de onderzoeksfunctie in Security Center kunt u potentiële [beveiligingsincidenten](https://docs.microsoft.com/azure/security-center/security-center-incident) sorteren, kijken wat voor gevolgen ze hebben en de hoofdoorzaak van een probleem opsporen.

De bedoeling is om het onderzoek te vereenvoudigen door alle entiteiten ([beveiligingswaarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), gebruikers, computers en incidenten) weer te geven die op een of andere manier betrokken zijn bij het incident dat u onderzoekt.  Dit kan door relevante gegevens te correleren met betrokken entiteiten en deze correlatie inzichtelijk te maken met behulp van een live grafiek waarmee u door de objecten kunt navigeren en relevante informatie kunt visualiseren.


> [!NOTE]
> * [Aangepaste waarschuwingen](security-center-custom-alert.md) worden niet ondersteund in de onderzoek functie van de Security Center.
> * Onderzoek wordt alleen ondersteund voor waarschuwingen op basis van gegevens die zijn verzameld van Windows-servers.


## <a name="how-investigation-works"></a>Hoe werkt de onderzoeksfunctie?
Het onderzoek bestaat uit een grafiek die het centrale gebied van het dashboard inneemt. De grafiek is altijd gericht op een specifieke entiteit en toont de entiteiten die hieraan zijn gerelateerd. Een entiteit kan bestaan uit een beveiligingswaarschuwing, een gebruiker, een computer of een incident.

![Kaart](./media/security-center-investigation/security-center-investigation-fig1.png)

De gebruiker kan schakelen tussen entiteiten door erop te klikken in de grafiek. De grafiek wordt dan automatisch gecentreerd rond de geselecteerde entiteit, met daaromheen de gerelateerde entiteiten. Entiteiten die nu niet meer relevant zijn, worden mogelijk verwijderd uit de grafiek.

### <a name="investigation-path"></a>Onderzoekspad
Terwijl de gebruiker door de verschillende entiteiten navigeert, is het onderzoekspad een handig hulpmiddel om de context van het onderzoek te zien en om snel te navigeren. Het incident dat de onderzoeksresultaten bevat, is altijd het meest linkse incident in het onderzoekspad.

![Pad](./media/security-center-investigation/security-center-investigation-fig2.png)

### <a name="general-information"></a>Algemene informatie
Wanneer een entiteit wordt weergegeven in de grafiek, bevatten de tabbladen aanvullende informatie over deze entiteit. Op het tabblad **Info** ziet u algemene informatie over de entiteit, afkomstig uit de verschillende beschikbare informatiebronnen.

![Algemene informatie](./media/security-center-investigation/security-center-investigation-fig3.png)

Het tabblad Info bevat informatie die relevant is voor het incident dat is geselecteerd op de kaart. Een incident is een container voor de resultaten van een onderzoek. Elk onderzoek gebeurt in de context van een incident.

Er wordt alleen een incident gemaakt wanneer een gebruiker op de knop **Onderzoek starten** klikt voor een bepaalde waarschuwing. De standaardbewerking die een onderzoeker kan uitvoeren, is het markeren van entiteiten zoals gebruiker, computer of waarschuwing. Zodra een entiteit is gemarkeerd als gerelateerd, wordt er een reden weergegeven. Vanaf dit moment wordt deze entiteit direct onder het incident weergegeven in de grafiek en in de lijst met entiteiten voor het incident.

### <a name="entities"></a>Entiteiten

Het tabblad **Entiteiten** bevat alle gerelateerde entiteiten gegroepeerd op type. Dit is handig in twee gevallen: als er te veel entiteiten zijn om weer te geven in de grafiek en wanneer de namen van entiteiten te lang zijn en het eenvoudiger is om ze in een tabel te onderzoeken.

![Entiteiten](./media/security-center-investigation/security-center-investigation-fig4.png)

### <a name="search"></a>Search

Het tabblad **Zoeken** bevat alle typen logboeken die beschikbaar zijn voor de entiteit. Voor elk type logboek kunt u zien hoeveel records er zijn vastgelegd in het logboek. Klik op een type logboek om naar het zoekscherm te gaan. Hier kunt u uw zoekopdracht verfijnen en de verschillende zoekfuncties gebruiken, zoals het instellen van waarschuwingen. In de huidige versie is het tabblad Zoeken alleen beschikbaar voor de entiteiten gebruikers en computers.

![Search](./media/security-center-investigation/security-center-investigation-fig5.png)

### <a name="exploration"></a>Verkenning

Via het tabblad **Verkennen** kan de onderzoeker gegevens onderzoeken die te maken hebben met verschillende aspecten van de entiteit. Als er bijvoorbeeld onderzoek wordt gedaan naar een computer, ziet u op dit tabblad een overzicht van de processen die op de computer zijn uitgevoerd. In sommige gevallen bevat het tabblad Verkennen gegevens die kunnen wijzen op een verdachte activiteit. De onderzoeker kan de gegevens bestuderen op het tabblad of openen in het zoekscherm om grote gegevenssets te onderzoeken en geavanceerde zoekopties te gebruiken zoals filteren en exporteren naar Excel.

![Verkenning](./media/security-center-investigation/security-center-investigation-fig6.png)

### <a name="timeline"></a>Tijdlijn

De meeste gegevens in de grafiek en op de verschillende tabbladen zijn alleen relevant voor een bepaalde periode. Dit tijdsbereik kan worden ingesteld in de linkerbovenhoek van de grafiek. U kunt op verschillende manieren het bereik voor het onderzoek instellen.

![Tijdlijn](./media/security-center-investigation/security-center-investigation-fig7.png)

Het ingestelde tijdsbereik is van belang voor de volgende items:

- De relatie tussen de computer en de gebruiker in de grafiek: alleen gebruikers die zich gedurende deze periode hebben aangemeld bij de computer worden weergegeven.
- Welke waarschuwingen worden weergegeven wanneer u computers en gebruikers onderzoekt: alleen waarschuwingen uit de geselecteerde periode worden weergegeven.
- Voor het tabblad Entiteiten wordt dezelfde logica gevolgd als voor de grafiek.

De volgende items worden weergegeven ongeacht het geselecteerde tijdsbereik:

- Wanneer er een waarschuwing wordt weergegeven, worden alle entiteiten van de waarschuwing, zoals gebruikers en computers, altijd weergegeven.
- Als een incident een entiteit bevat, wordt de entiteit weergegeven.

> [!NOTE]
> De tabbladen **Zoeken** en **Verkennen** bevatten alleen records uit dit tijdsbereik.

## <a name="how-to-perform-an-investigation"></a>Een onderzoek uitvoeren

U kunt een onderzoek starten vanuit een beveiligingsincident of vanuit een waarschuwing, afhankelijk van uw behoeften. De volgende stappen worden gebruikt om een onderzoek vanuit een waarschuwing te starten:

1.  Open het dashboard **Security Center**.
2.  Klik op **Beveiligingswaarschuwingen** en selecteer het incident dat u wilt onderzoeken.
3.  Klik op de pagina van het incident op de knop **Onderzoek starten** om het dashboard **Verkennen** weer te geven.

    ![Waarschuwing](./media/security-center-investigation/security-center-investigation-fig8.png)

4. In dit dashboard kunt u de entiteit selecteren op de kaart, waarna de relevante informatie over deze entiteit aan de rechterkant van het scherm wordt weergegeven.

    ![Het dashboard Verkennen](./media/security-center-investigation/security-center-investigation-fig9.png)

Vanaf hier kunt u de entiteiten onderzoeken die bij dit incident betrokken zijn en voor elk incident details bestuderen.

## <a name="see-also"></a>Zie ook
In dit document hebt u gelezen hoe u de onderzoeksfunctie in Security Center gebruikt. Zie de volgende onderwerpen voor meer informatie over het Beveiligingscentrum:

* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informatie over het beheren van waarschuwingen en het reageren op beveiligingsincidenten in Security Center.
* [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md). Meer informatie over het controleren van de status van uw Azure-resources.
* [Beveiligingswaarschuwingen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Meer informatie over de verschillende typen beveiligingswaarschuwingen.
* [Handleiding voor het oplossen van problemen met Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informatie over het oplossen van veelvoorkomende problemen met Security Center.
* [Veelgestelde vragen over Azure Security Center](security-center-faq.md). Raadpleeg de veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligingsblog](https://blogs.msdn.com/b/azuresecurity/). Raadpleeg de blogberichten over beveiliging en naleving in Azure.
