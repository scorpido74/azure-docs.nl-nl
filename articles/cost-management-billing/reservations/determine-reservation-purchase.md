---
title: Bepalen welke Azure-reservering u moet aanschaffen
description: Dit artikel helpt u te bepalen welke reservering u het beste kunt aanschaffen.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 08/04/2020
ms.author: banders
ms.openlocfilehash: d89f890d3e2bb8238a00b4a529b8804a8c38fa6f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684742"
---
# <a name="determine-what-reservation-to-purchase"></a>Bepalen welke reservering u moet aanschaffen

Alle reserveringen, met uitzondering van Azure Databricks, worden per uur toegepast. U moet reserveringen aanschaffen op basis van consistent basisgebruik. Er zijn meerdere manieren om te bepalen wat u moet aanschaffen, en dit artikel helpt u te bepalen welke reservering voor u de beste keuze is.

Als u meer capaciteit aanschaft dan past bij uw historische gebruik, resulteert dit in een onderbenutte reservering. Vermijd onderbenutting zoveel mogelijk. Niet-gebruikte gereserveerde capaciteit kunt u niet meenemen naar het volgende uur. Gebruik dat de gereserveerde hoeveelheid overschrijdt, wordt gefactureerd tegen hogere tarieven voor betalen per gebruik.

## <a name="analyze-usage-data"></a>Gebruiksgegevens analyseren

Gebruik de volgende secties om de gegevens voor uw dagelijkse gebruik te analyseren, zodat u uw basislijngebruik kunt vaststellen en kunt bepalen welke reservering u moet aanschaffen.

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>Gebruik analyseren voor een aankoop van een gereserveerde VM-instantie

Bepaal de juiste VM-grootte voor uw aankoop. Een reservering die is aangeschaft voor de ES-reeks VM’s is bijvoorbeeld niet van toepassing op VM’s uit de E-reeks, en andersom.

Voor VM’s uit de promotiereeks geldt geen reserveringskorting. Verwijder deze daarom uit de analyse.

Als u de analyse wilt beperken tot het gebruik van in aanmerking komende VM’s, past u de volgende filters toe op uw gebruiksgegevens:

- Filter **MeterCategory** op **Virtuele machines**.
- Haal informatie over **ServiceType** op uit **AdditionalInfo**. In de informatie wordt nu de juiste VM-grootte voorgesteld. Bijvoorbeeld Standard E32.
- Gebruik het veld **ResourceLocation** om het gebruiksgegevenscentrum te bepalen.

Negeer resources met een gebruik van minder dan 24 uur per dag.

Als u de instantiegrootte wilt analyseren op familieniveau, kunt u de flexibiliteitswaarden voor de instantiegrootte ophalen uit [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv). Combineer de waarden met uw gegevens om de analyse uit te voeren. Zie [Flexibiliteit van de VM-grootte met gereserveerde VM-instanties](../../virtual-machines/reserved-vm-instance-size-flexibility.md) voor meer informatie over flexibiliteit met instantiegrootten.

### <a name="analyze-usage-for-an-azure-synapse-analytics-reserved-instance-purchase"></a>Gebruik analyseren voor de aankoop van een gereserveerd exemplaar voor Azure Synapse Analytics

Gereserveerde capaciteit is van toepassing op DWU-prijzen voor Azure Synapse Analytics. Deze is niet van toepassing op Azure Synapse Analytics-licentiekosten of enige andere kosten, buiten de rekenkosten.

Als u de analyse wilt beperken tot in aanmerking komend gebruik, past u de volgende filters toe op uw gebruiksgegevens:


- Filter **MeterCategory** voor **SQL Database**.
- Filter **MeterName** voor **vCore**.
- Filter **MeterSubCategory** voor alle gebruiksrecords met _reken_ in de naam.

Haal uit **AdditionalInfo** de waarde **vCores** op. Hiermee wordt aangegeven hoeveel vCores zijn gebruikt. De hoeveelheid is: **vCores** vermenigvuldigd met het aantal uur dat de database is gebruikt.

De gegevens informeren u over het consistente gebruik voor:

- Combinatie van databasetype. Bijvoorbeeld: een beheerd exemplaar of elastische pool per afzonderlijke database.
- Servicelaag. Bijvoorbeeld: algemeen gebruik of bedrijfskritiek.
- Generatie. Bijvoorbeeld: Gen 5.
- Resourcelocatie

### <a name="analysis-for-azure-synapse-analytics"></a>Analyse voor Azure Synapse Analytics

Gereserveerde capaciteit is van toepassing op DWU-gebruik voor Azure Synapse Analytics en wordt aangeschaft in stappen van 100 DWU. Als u de analyse wilt beperken tot in aanmerking komend gebruik, past u de volgende filters toe op uw gebruiksgegevens:

- Filter **MeterName** voor **100 DWU’s**.
- Filter **Metersubcategorie** voor **Berekening geoptimaliseerde Gen2**.

Gebruik het veld **Resourcelocatie** om het gebruik van Azure Synapse Analytics in een regio te bepalen.

Azure Synapse Analytics-gebruik kan gedurende de hele dag omhoog en omlaag worden geschaald. Neem contact op met het team dat de Azure Synapse Analytics-instantie heeft beheerd, voor meer informatie over het basisgebruik.

Ga naar Reserveringen in de Azure-portal en koop gereserveerde Azure Synapse Analytics-capaciteit in veelvouden van 100 DWU's.

## <a name="reservation-purchase-recommendations"></a>Aanbevelingen voor reserveringsaankopen

Aanbevelingen voor reserveringsaankopen worden berekend door uw gebruiksgegevens per uur te analyseren voor de afgelopen 7, 30 of 60 dagen. Azure berekent wat de kosten met een reservering zouden zijn geweest, en vergelijkt dit met uw werkelijke kosten voor betalen per gebruik die in rekening zijn gebracht gedurende deze tijdsperiode. De berekening wordt uitgevoerd voor elk aantal dat u hebt gebruikt tijdens het tijdsbestek. De hoeveelheid waarbij de besparingen het grootst zijn, is de aanbevolen hoeveelheid.

U gebruikt bijvoorbeeld mogelijk gedurende het grootste gedeelte van de tijd 500 VM’s, met af en toe een piek naar 700 VM’s. In dit voorbeeld wordt in Azure uw besparing berekend voor het gebruik van zowel 500 als 700 VM’s. Aangezien het gebruik van 700 VM’s slechts sporadisch voorkomt, wordt met de aanbevelingsberekening bepaald dat de besparingen het grootst zijn bij een aankoop van 500 VM-reserveringen en wordt de aanbeveling gedaan voor een hoeveelheid van 500.

Houd rekening met de volgende punten:

- Aanbevelingen voor reserveringen worden berekend op basis van de gebruikstarieven op aanvraag die voor u van toepassing zijn.
- Aanbevelingen worden berekend voor afzonderlijke grootten, niet voor de familie van instantiegrootten.
- De aanbevolen hoeveelheid voor een bereik wordt op dezelfde dag verminderd waarop u reserveringen voor het bereik aanschaft.
    - Een update van de aanbeveling voor de hoeveelheid reserveringen in meerdere bereiken kan echter maximaal 25 dagen duren. Als uw aankoop bijvoorbeeld is gebaseerd op aanbevelingen voor een gedeeld bereik, duurt het maximaal 25 dagen voordat aanbevelingen voor een bereik van één abonnement omlaag zijn aangepast.

## <a name="recommendations-in-the-azure-portal"></a>Aanbevelingen in de Azure-portal

Reserveringsaankopen die zijn berekend met de aanbevelingsengine worden weergegeven op het tabblad **Aanbevolen** in de [Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs). Hier ziet u een voorbeeldafbeelding.

![Afbeelding met aanbevelingen](./media/determine-reservation-purchase/select-product-ri.png)

Meer informatie over [aanbevelingen](reserved-instance-purchase-recommendations.md#recommendations-in-the-azure-portal).

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>Aanbevelingen in de Cost Management Power BI-app

Enterprise Agreement- en Microsoft Customer Agreement-klanten kunnen de VM RI-dekkingsrapporten gebruiken voor aanbevelingen met betrekking tot VM’s en aankopen. In de dekkingsrapporten ziet u het totale gebruik en het gebruik dat wordt gedekt door de gereserveerde instanties.

1. Download de [Cost Management-app](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
2. Ga naar het VM RI-dekkingsrapport: Gedeeld bereik of Eén bereik, afhankelijk van het bereik waarvoor de aankoop is bedoeld.
3. Selecteer de regio, de familie van instantiegrootten om het gebruik te zien, RI-dekking, en de aankoopaanbeveling voor het geselecteerde filter.

## <a name="recommendations-in-azure-advisor"></a>Aanbevelingen in Azure Advisor

Aankoopaanbevelingen voor reserveringen zijn beschikbaar in [Azure Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview).

- Advisor heeft alleen aanbevelingen voor bereiken van één abonnement.
- Aanbevelingen van Advisor worden berekend op basis van een periode van 30 dagen. De verwachte besparingen gelden voor een reserveringstermijn van 3 jaar.
- Als u een reservering met gedeeld bereik aanschaft, kan het tot 30 dagen duren voordat aankoopaanbelingen voor reserveringen van Advisor zijn verdwenen.

## <a name="recommendations-using-apis"></a>Aanbevelingen voor het gebruik van API's

Gebruik de REST API [Aanbevelingen voor reserveringen](/rest/api/consumption/reservationrecommendations/list) om aanbevelingen programmatisch weer te geven.

## <a name="next-steps"></a>Volgende stappen

- [Azure-reserveringen beheren](manage-reserved-vm-instance.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
- [Kosten van Windows-software zijn niet inbegrepen in reserveringen](reserved-instance-windows-software-costs.md)