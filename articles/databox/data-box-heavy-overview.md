---
title: Overzicht van Microsoft Azure Data Heavy | Microsoft Docs in gegevens
description: Beschrijft Azure Data Box, een cloudoplossing waarmee u enorme hoeveelheden gegevens naar Azure kunt overdragen.
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 08/28/2019
ms.author: alkohli
ms.openlocfilehash: 2f1f01a8cfa25c222848e7cc5c86dc3532eba348
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80437781"
---
# <a name="what-is-azure-data-box-heavy"></a>Wat is Azure Data Box Heavy?

Met Azure Data Box Heavy kunt u op een snelle, goedkope en betrouw bare manier honderden terabytes aan gegevens verzenden naar Azure. De gegevens worden overgebracht naar Azure door u een Data Box Heavy apparaat te sturen met een opslag capaciteit van 1 tot PB, die u opvult met uw gegevens en terugsturen naar micro soft. Het apparaat heeft een robuuste behuizing om uw gegevens tijdens de overdracht te beschermen en te beveiligen.

Zodra het apparaat is ontvangen op uw Data Center, stelt u het in met behulp van de lokale webgebruikersinterface. Kopieer de gegevens van uw servers naar het apparaat en stuur het terug naar Azure. In het Azure-Data Center worden uw gegevens geüpload naar uw Azure Storage-account (s). U kunt het volledige end-to-end-proces volgen in de Azure Portal.


> [!IMPORTANT]
> - Als u een apparaat wilt aanvragen, meldt u zich aan bij de [Azure Portal](https://portal.azure.com).


## <a name="use-cases"></a>Gebruiksvoorbeelden

Data Box Heavy is het meest geschikt voor de grootte van gegevens in honderden terabytes, waarbij de netwerk verbinding onvoldoende is om de gegevens te uploaden naar Azure. De gegevensverplaatsing kan eenmalig of periodiek zijn, of eerst een grote gegevensoverdracht, gevolgd door meerdere periodieke overdrachten. Hier volgen de verschillende scenario’s waarin Data Box Heavy kan worden gebruikt voor gegevensoverdracht.

 - **Eenmalige migratie**: wanneer grote aantallen on-premises gegevens worden verplaatst naar Azure.
     - Verplaats een media bibliotheek van offline tapes naar Azure om een bibliotheek voor online media te maken.
     - Migreer uw VM-Farm, SQL Server en toepassingen naar Azure.
     - Verplaats historische gegevens naar Azure voor uitgebreide analyse en rapporten met HDInsight.

 - **Initiële bulkoverdracht**: wanneer eerst een grote overdracht plaatsvindt met Data Box Heavy (seed), gevolgd door incrementele overdrachten via het netwerk.
     - Data Box Heavy en een partner voor back-upoplossing worden bijvoorbeeld gebruikt om de eerste grote historische back-up naar Azure te verplaatsen. Zodra dat is voltooid, worden gegevens incrementeel via het netwerk overgebracht naar Azure-opslag.

 - **Periodieke uploads**: wanneer er periodiek grote aantallen gegevens worden gegenereerd en verplaatst moeten worden naar Azure. Bijvoorbeeld in de energie-exploitatie, waar er video's worden gegenereerd op olieplatforms en windmolenparken.

## <a name="benefits"></a>Voordelen

Data Box Heavy is ontworpen om enorme hoeveel heden gegevens naar Azure te verplaatsen, met weinig gevolgen voor uw netwerk. Deze oplossing biedt de volgende voordelen:

- **Snelle** data Box Heavy maakt gebruik van hoge prestaties van 40-Gbps-netwerk interfaces.

- **Security** -data Box Heavy heeft ingebouwde beveiligings beveiligingen voor het apparaat, de gegevens en de service.
    - Het apparaat heeft een robuuste behuizing die is beveiligd met fraudebestendige schroeven en verzegelde stickers.
    - De gegevens op het apparaat zijn altijd beveiligd met AES 256-bitsversleuteling.
    - Het apparaat kan alleen worden ontgrendeld met een wachtwoord dat via Azure Portal wordt doorgegeven.
    - De service wordt beschermd door beveiligingsfuncties van Azure.
    - Zodra uw gegevens naar Azure zijn geüpload, worden de schijven op het apparaat schoon gewist, in overeenstemming met de normen van het National Institute of Standards and Technology (NIST) 800-88r1.


## <a name="features-and-specifications"></a>Functies en specificaties

Het Data Box Heavy-apparaat heeft de volgende functies in deze release.

| Specificaties                                          | Beschrijving              |
|---------------------------------------------------------|--------------------------|
| Gewicht                                                  | ~ 500 lbs. <br>Apparaat op het vergren delen van wielen voor Trans Port|
| Dimensies                                              | Breedte: 26 inch hoogte: 28 cm lengte: 48 inches |
| Rackruimte                                              | Kan niet worden gekoppeld aan een rek|
| Vereiste kabels                                         | 4 gehuurde 120 V/10 een stroom kabel (NEMA 5-15) inbegrepen <br> Het apparaat ondersteunt een capaciteit van Maxi maal 240 V en heeft C-13 voedings containers <br> Netwerk kabels gebruiken die compatibel [zijn met MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Power                                                    | 4 ingebouwde voedings eenheden (PSUs) die worden gedeeld tussen de knoop punten van het apparaat <br> 1.200 watt typische power draw|
| Opslagcapaciteit                                        | ~ 1-PB RAW, 70 schijven van 14 TB elk <br> 770-TB bruikbare capaciteit|
| Aantal knoop punten                                          | 2 onafhankelijke knoop punten per apparaat (500 TB elk) |
| Netwerk interfaces per knoop punt                             | 4 netwerk interfaces per knoop punt <br><br> BEHEER, DATA3 <ul><li> 2 X 1 GbE-interfaces </li><li> Beheer is voor het beheren van en de eerste installatie, en kan niet door de gebruiker worden geconfigureerd </li><li> DATA3 is standaard door de gebruiker te configureren en Dynamic Host Configuration Protocol (DHCP)</li></ul>BESTAND1, BESTAND2 gegevens interfaces <ul><li>2 X 40-GbE-interfaces </li><li> Gebruiker configureerbaar voor DHCP (standaard) of statisch</li></ul>|


## <a name="components"></a>Onderdelen

Deze Data Box Heavy bevat de volgende onderdelen:

* **Data Box Heavy-apparaat**: Een fysiek apparaat met een stevige behuizing waarop gegevens veilig worden opgeslagen. Dit apparaat heeft een bruikbare opslag capaciteit van 770 TB.
    
* **Data Box-service**: Een extensie in Azure Portal waarmee u een Data Box Heavy-apparaat kunt beheren via een webinterface waartoe u toegang hebt vanaf verschillende geografische locaties. Gebruik de Data Box-Service om uw Data Box Heavy-apparaat te beheren. Met de service kunt u taken uitvoeren zoals orders maken en beheren, waarschuwingen bekijken en beheren en shares beheren.  

* **Lokale webinterface**: een webinterface die wordt gebruikt voor het configureren van het apparaat, zodat u verbinding kunt maken met het lokale netwerk en het apparaat bij de Data Box-service kunt registreren. Gebruik de lokale webinterface om het apparaat uit te schakelen of opnieuw op te starten, logboeken met kopieerbewerkingen te bekijken en contact op te nemen met Microsoft Ondersteuning om een serviceaanvraag in te dienen.


## <a name="the-workflow"></a>De werkstroom

Een typische stroom bestaat uit de volgende stappen:

1. **Bestellen**: plaats een bestelling in Azure Portal en geef de verzendingsgegevens en het Azure-doelopslagaccount voor uw gegevens op. Wanneer het apparaat beschikbaar is, bereidt Azure het voor en verstuurt Azure het met volgnummer.

2. **Ontvangen**: Nadat het apparaat wordt geleverd, sluit u de opgegeven netwerk- en voedingskabels aan. Zet het apparaat aan en sluit het aan. Configureer de netwerkinstellingen voor het apparaat en koppel bestandsshares op de hostcomputer van waar u gegevens wilt kopiëren.

3. **Gegevens kopiëren**: Kopieer gegevens naar de shares van Data Box Heavy.

4. **Retourneren**: Bereid het apparaat voor, zet het uit en stuur het terug naar het Azure-datacenter.

5. **Uploaden**: De gegevens worden automatisch van het apparaat naar Azure gekopieerd. De schijven in het apparaat worden veilig gewist overeenkomstig de richtlijnen van het National Institute of Standards and Technology.

Tijdens dit proces wordt u via e-mail op de hoogte gesteld van alle status wijzigingen.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Data Box Heavy kunt gegevens overdragen op basis van de regio waarin de service is geïmplementeerd, het land/de regio waarnaar het apparaat wordt verzonden en het Azure Storage-doel account waar u de gegevens overdraagt.

- **Beschikbaarheid van service**: Voor deze release is de Data Box Heavy-resource beschikbaar in de volgende regio’s:
    - Alle openbare cloud-regio's in de Verenigde Staten: VS - west-centraal, VS - west 2, VS - west, VS - zuid-centraal, VS - centraal, VS - noord-centraal, VS - oost en VS - oost 2.
    - Europese Unie: Europa - west en Europa - noord.
    - Verenigd Koninkrijk: VK - zuid en VK - west.
    - Frankrijk: Frankrijk - centraal en Frankrijk - zuid.

- **Doelopslagaccounts**: De opslagaccounts waarin de gegevens worden opgeslagen, zijn beschikbaar in alle Azure-regio’s waar de service beschikbaar is.

Ga voor de meest recente informatie over de beschik baarheid van regio's voor Data Box Heavy naar [Azure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Aanmelden

Voer de volgende stappen uit om u aan te melden voor Data Box Heavy:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com).
2. Klik op **+ een resource maken** om een nieuwe resource te maken. Zoek naar **Azure Data Box**. Selecteer de **Azure Data Box**-service.
3. Klik op **maken**.
4. Kies het abonnement dat u wilt gebruiken voor Data Box Heavy. Selecteer de regio waar u de Data Box Heavy-resource wilt implementeren. Klik bij de optie **Data Box Heavy** op **Registreren**.
5. Beantwoord de vragen over het land/de regio, de tijd van de Azure-doel service voor gegevens overdracht, de netwerk bandbreedte en de frequentie van gegevens overdracht. Lees de privacyverklaring en voorwaarden en schakel het selectievakje in zodat Microsoft uw e-mailadres kan gebruiken om contact met u op te nemen.

Zodra u zich hebt geregistreerd, kunt u een Data Box Heavy best Ellen.

    
