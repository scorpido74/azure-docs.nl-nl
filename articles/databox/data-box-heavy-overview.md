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
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437781"
---
# <a name="what-is-azure-data-box-heavy"></a>Wat is Azure Data Box Heavy?

Met Azure Data Box Heavy u op een snelle, goedkope en betrouwbare manier honderden terabytes aan gegevens naar Azure verzenden. De gegevens worden overgedragen naar Azure door u een Data Box Heavy-apparaat met 1-PB-opslagcapaciteit te verzenden, dat u met uw gegevens vult en teruggestuurd naar Microsoft. Het apparaat heeft een robuuste behuizing om uw gegevens tijdens het transport te beschermen en te beveiligen.

Zodra het apparaat in uw datacenter is ontvangen, stelt u het in met behulp van de lokale web-gebruikersinterface. Kopieer de gegevens van uw servers naar het apparaat en stuur het terug naar Azure. In het Azure-datacenter worden uw gegevens geüpload naar uw Azure Storage-account(s). U het hele end-to-end proces volgen in de Azure-portal.


> [!IMPORTANT]
> - Als u een apparaat wilt aanvragen, meldt u zich aan in de [Azure-portal.](https://portal.azure.com)


## <a name="use-cases"></a>Gebruiksvoorbeelden

Data Box Heavy is het meest geschikt voor gegevensformaten in de honderden terabytes, waarbij de netwerkconnectiviteit onvoldoende is om de gegevens naar Azure te uploaden. De gegevensverplaatsing kan eenmalig of periodiek zijn, of eerst een grote gegevensoverdracht, gevolgd door meerdere periodieke overdrachten. Hier volgen de verschillende scenario’s waarin Data Box Heavy kan worden gebruikt voor gegevensoverdracht.

 - **Eenmalige migratie**: wanneer grote aantallen on-premises gegevens worden verplaatst naar Azure.
     - Verplaats een mediabibliotheek van offline tapes naar Azure om een online mediabibliotheek te maken.
     - Migreer uw VM-farm, SQL-server en toepassingen naar Azure.
     - Verplaats historische gegevens naar Azure voor diepgaande analyse en rapport met HDInsight.

 - **Initiële bulkoverdracht**: wanneer eerst een grote overdracht plaatsvindt met Data Box Heavy (seed), gevolgd door incrementele overdrachten via het netwerk.
     - Data Box Heavy en een partner voor back-upoplossingen worden bijvoorbeeld gebruikt om de eerste grote historische back-up naar Azure te verplaatsen. Zodra dat is voltooid, worden gegevens incrementeel via het netwerk overgebracht naar Azure-opslag.

 - **Periodieke uploads**: wanneer er periodiek grote aantallen gegevens worden gegenereerd en verplaatst moeten worden naar Azure. Bijvoorbeeld in de energie-exploitatie, waar er video's worden gegenereerd op olieplatforms en windmolenparken.

## <a name="benefits"></a>Voordelen

Data Box Heavy is ontworpen om enorme hoeveelheden gegevens naar Azure te verplaatsen, met weinig tot geen impact op uw netwerk. Deze oplossing biedt de volgende voordelen:

- **Snelheid** - Data Box Heavy maakt gebruik van high-performance 40-Gbps netwerkinterfaces.

- **Beveiliging** - Data Box Heavy heeft ingebouwde beveiligingsbeveiligingen voor het apparaat, de gegevens en de service.
    - Het apparaat heeft een robuuste behuizing die is beveiligd met fraudebestendige schroeven en verzegelde stickers.
    - De gegevens op het apparaat zijn altijd beveiligd met AES 256-bitsversleuteling.
    - Het apparaat kan alleen worden ontgrendeld met een wachtwoord dat via Azure Portal wordt doorgegeven.
    - De service wordt beschermd door beveiligingsfuncties van Azure.
    - Zodra uw gegevens zijn geüpload naar Azure, worden de schijven op het apparaat schoongeveegd, in overeenstemming met de 800-88r1-normen van het National Institute of Standards and Technology (NIST).


## <a name="features-and-specifications"></a>Functies en specificaties

Het Data Box Heavy-apparaat heeft in deze release de volgende functies.

| Specificaties                                          | Beschrijving              |
|---------------------------------------------------------|--------------------------|
| Gewicht                                                  | ~ 500 pond. <br>Apparaat op vergrendelingswielen voor transport|
| Dimensies                                              | Breedte: 26 inch hoogte: 28 inch lengte: 48 inch |
| Rackruimte                                              | Kan niet worden gemonteerd op een rack|
| Vereiste kabels                                         | 4 geaard 120 V / 10 A netsnoeren (NEMA 5-15) inbegrepen <br> Apparaat ondersteunt maximaal 240 V-vermogen en heeft C-13-vermogensrecipiënten <br> Gebruik netwerkkabels die compatibel zijn met [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Power                                                    | 4 ingebouwde voedingen (PSU's) die worden gedeeld over beide apparaatknooppunten <br> 1.200 watt typische power draw|
| Opslagcapaciteit                                        | ~ 1-PB rauw, 70 schijven van 14 TB per stuk <br> Bruikbare capaciteit van 770 TB|
| Aantal knooppunten                                          | 2 onafhankelijke knooppunten per apparaat (500 TB per stuk) |
| Netwerkinterfaces per knooppunt                             | 4 netwerkinterfaces per knooppunt <br><br> MGMT, DATA3 <ul><li> 2 X 1 GbE-interfaces </li><li> MGMT is voor beheer en initiële setup, niet voor het configureren van de gebruiker </li><li> DATA3 is standaard configureerbaar en DHCP (Dynamic Host Configuration Protocol)</li></ul>DATA1, DATA2 data-interfaces <ul><li>2 X 40 GbE-interfaces </li><li> Gebruiker configureerbaar voor DHCP (standaard) of statisch</li></ul>|


## <a name="components"></a>Onderdelen

Deze Data Box Heavy bevat de volgende onderdelen:

* **Data Box Heavy-apparaat**: Een fysiek apparaat met een stevige behuizing waarop gegevens veilig worden opgeslagen. Dit apparaat heeft een bruikbare opslagcapaciteit van 770 TB.
    
* **Data Box-service**: Een extensie in Azure Portal waarmee u een Data Box Heavy-apparaat kunt beheren via een webinterface waartoe u toegang hebt vanaf verschillende geografische locaties. Gebruik de Data Box-service om uw Data Box Heavy-apparaat te beheren. Met de service kunt u taken uitvoeren zoals orders maken en beheren, waarschuwingen bekijken en beheren en shares beheren.  

* **Lokale webinterface**: een webinterface die wordt gebruikt voor het configureren van het apparaat, zodat u verbinding kunt maken met het lokale netwerk en het apparaat bij de Data Box-service kunt registreren. Gebruik de lokale webinterface om het apparaat uit te schakelen of opnieuw op te starten, logboeken met kopieerbewerkingen te bekijken en contact op te nemen met Microsoft Ondersteuning om een serviceaanvraag in te dienen.


## <a name="the-workflow"></a>De werkstroom

Een typische stroom bestaat uit de volgende stappen:

1. **Bestellen**: plaats een bestelling in Azure Portal en geef de verzendingsgegevens en het Azure-doelopslagaccount voor uw gegevens op. Wanneer het apparaat beschikbaar is, bereidt Azure het voor en verstuurt Azure het met volgnummer.

2. **Ontvangen**: Nadat het apparaat wordt geleverd, sluit u de opgegeven netwerk- en voedingskabels aan. Zet het apparaat aan en sluit het aan. Configureer de netwerkinstellingen voor het apparaat en koppel bestandsshares op de hostcomputer van waar u gegevens wilt kopiëren.

3. **Gegevens kopiëren**: Kopieer gegevens naar de shares van Data Box Heavy.

4. **Retourneren**: Bereid het apparaat voor, zet het uit en stuur het terug naar het Azure-datacenter.

5. **Uploaden**: De gegevens worden automatisch van het apparaat naar Azure gekopieerd. De schijven in het apparaat worden veilig gewist overeenkomstig de richtlijnen van het National Institute of Standards and Technology.

Tijdens dit proces wordt u via e-mail op de hoogte gesteld van alle statuswijzigingen.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Data Box Heavy kan gegevens overdragen op basis van de regio waarin de service wordt geïmplementeerd, land/regio waarnaar het apparaat wordt verzonden en het doelAzure-opslagaccount waar u de gegevens overdraagt.

- **Beschikbaarheid van service**: Voor deze release is de Data Box Heavy-resource beschikbaar in de volgende regio’s:
    - Alle openbare cloud-regio's in de Verenigde Staten: VS - west-centraal, VS - west 2, VS - west, VS - zuid-centraal, VS - centraal, VS - noord-centraal, VS - oost en VS - oost 2.
    - Europese Unie: Europa - west en Europa - noord.
    - Verenigd Koninkrijk: VK - zuid en VK - west.
    - Frankrijk: Frankrijk - centraal en Frankrijk - zuid.

- **Doelopslagaccounts**: De opslagaccounts waarin de gegevens worden opgeslagen, zijn beschikbaar in alle Azure-regio’s waar de service beschikbaar is.

Ga voor de meest recente informatie over de beschikbaarheid van regio's voor Data Box Heavy naar [Azure-producten per regio.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)

## <a name="sign-up"></a>Aanmelden

Volg de volgende stappen om je aan te melden voor Data Box Heavy:

1. [Meld u aan bij de Azure-portal](https://portal.azure.com).
2. Klik **op + Een resource maken** om een nieuwe bron te maken. Zoek naar **Azure Data Box**. Selecteer de **Azure Data Box**-service.
3. Klik **op Maken**.
4. Kies het abonnement dat u wilt gebruiken voor Data Box Heavy. Selecteer de regio waar u de Data Box Heavy-resource wilt implementeren. Klik bij de optie **Data Box Heavy** op **Registreren**.
5. Beantwoord de vragen met betrekking tot land/regio, tijdskader, doelAzure-service voor gegevensoverdracht, netwerkbandbreedte en gegevensoverdrachtsfrequentie. Lees de privacyverklaring en voorwaarden en schakel het selectievakje in zodat Microsoft uw e-mailadres kan gebruiken om contact met u op te nemen.

Zodra u bent aangemeld, u een Data Box Heavy bestellen.

    
