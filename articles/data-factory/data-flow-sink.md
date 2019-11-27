---
title: Een Sink-trans formatie instellen in de functie gegevens stroom toewijzen
description: Meer informatie over het instellen van een Sink-trans formatie in de stroom voor het toewijzen van gegevens.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/03/2019
ms.openlocfilehash: 707c0e93b88f34d4663d3dbe20bb2e9e4991a332
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217932"
---
# <a name="sink-transformation-for-a-data-flow"></a>Sink-trans formatie voor een gegevens stroom

Nadat u uw gegevens stroom hebt getransformeerd, kunt u de gegevens in een doel-gegevensset opvangen. Kies in de Sink-trans formatie een definitie van de gegevensset voor de doel uitvoer gegevens. U kunt zoveel Sink-trans formaties hebben als uw gegevens stroom vereist.

Om rekening te houden met schema drift en wijzigingen in binnenkomende gegevens, moet u de uitvoer gegevens opvangen naar een map zonder een gedefinieerd schema in de uitvoer gegevensset. U kunt ook account voor kolom wijzigingen in uw bronnen instellen door **schema-drift toestaan** in de bron te selecteren. Vervolgens worden alle velden in de Sink Automap.

![Opties op het tabblad sink, met inbegrip van de optie automatisch toewijzen](media/data-flow/sink1.png "Sink 1")

Als u alle binnenkomende velden wilt opvangen, schakelt u **automatische toewijzing**in. Als u de velden wilt selecteren die moeten worden gefilterd op de bestemming, of als u de namen van de velden op de bestemming wilt wijzigen, schakelt u **automatisch toewijzen**uit. Open vervolgens het tabblad **toewijzing** om uitvoer velden toe te wijzen.

![Opties op het tabblad toewijzing](media/data-flow/sink2.png "Sink 2")

## <a name="output"></a>Uitvoer 
Voor Azure Blob Storage-of Data Lake Storage Sink-typen voert u de getransformeerde gegevens uit naar een map. Spark genereert gepartitioneerde uitvoer gegevens bestanden op basis van het partitie schema dat door de Sink-trans formatie wordt gebruikt. 

U kunt het partitie schema instellen op het tabblad **optimaliseren** . Als u wilt dat Data Factory uw uitvoer in één bestand samenvoegt, selecteert u **één partitie**. Als u gepartitioneerde mappen wilt onderhouden of maken, gebruikt u **sleutel partities** en stelt u de sleutels in die u wilt gebruiken voor gepartitioneerde mappen structuren.

![Opties op het tabblad optimaliseren](media/data-flow/opt001.png "Sink-opties")

## <a name="field-mapping"></a>Veld toewijzing
Op het tabblad **toewijzing** van de Sink-trans formatie kunt u de binnenkomende kolommen aan de linkerkant toewijzen aan de doelen aan de rechter kant. Wanneer u gegevens stromen naar bestanden sinkt, worden er door Data Factory altijd nieuwe bestanden naar een map geschreven. Wanneer u toewijst aan een Data Base-gegevensset, kiest u bewerkings opties voor database tabellen om in te voegen, bij te werken, te upsert of te verwijderen.

![Het tabblad toewijzing](media/data-flow/sink2.png "Wastafel")

U kunt in de tabel toewijzing meerdere kolommen koppelen, meerdere kolommen ontkoppelen of meerdere rijen aan dezelfde kolom naam toewijzen.

Selecteer **schema-drift toestaan**om de binnenkomende set van velden altijd toe te wijzen aan een doel, en om flexibele schema definities volledig te accepteren.

![Het tabblad toewijzing met de velden die zijn toegewezen aan de kolommen in de gegevensset](media/data-flow/multi1.png "meerdere opties")

Als u de kolom toewijzingen opnieuw wilt instellen, selecteert u **opnieuw toewijzen**.

![Het tabblad Sink](media/data-flow/sink1.png "Eén Sink")

Selecteer **schema valideren** om de sink te laten mislukken als het schema wordt gewijzigd.

Selecteer **de map wissen** om de inhoud van de map Sink af te kappen voordat u de doel bestanden in die doelmap schrijft.

## <a name="fixed-mapping-vs-rule-based-mapping"></a>Vaste toewijzing versus op regels gebaseerde toewijzing
Wanneer u automatische toewijzing uitschakelt, hebt u de mogelijkheid om een op kolom gebaseerde toewijzing (vaste toewijzing) of toewijzing op basis van een regel toe te voegen. Met op regels gebaseerde toewijzing kunt u expressies met patroon overeenkomsten schrijven, terwijl vaste toewijzing logische en fysieke kolom namen toewijst.

![Toewijzing op basis van een regel](media/data-flow/rules4.png "Toewijzing op basis van een regel")

Wanneer u op regels gebaseerde toewijzing kiest, geeft u de ADF de opdracht om de overeenkomende expressie te evalueren zodat deze overeenkomt met de regels voor binnenkomende patronen en de uitgaande veld namen te definiëren. U kunt een combi natie van zowel veld-als op regels gebaseerde toewijzingen toevoegen. Veld namen worden vervolgens gegenereerd tijdens runtime via ADF op basis van binnenkomende meta gegevens van de bron. U kunt de namen van de gegenereerde velden weer geven tijdens fout opsporing en het deel venster gegevens voorbeeld gebruiken.

Meer informatie over patroon vergelijking vindt u in de documentatie van het [kolom patroon](concepts-data-flow-column-pattern.md).

U kunt ook reguliere expressie patronen invoeren wanneer u op basis van een regel vergelijking gebruikt door de rij uit te vouwen en een reguliere expressie op te geven naast ' naam overeenkomsten: '.

![Regex-toewijzing](media/data-flow/scdt1g4.png "Regex-toewijzing")

Een eenvoudig algemeen voor beeld voor een op regels gebaseerde toewijzing versus vaste toewijzing is het geval dat u alle binnenkomende velden wilt toewijzen aan dezelfde naam in uw doel. In het geval van vaste toewijzingen zou u elke afzonderlijke kolom in de tabel vermelden. Voor toewijzing op basis van een regel hebt u één regel waarmee alle velden met ```true()``` worden toegewezen aan dezelfde binnenkomende veld naam die door ```$$```wordt weer gegeven.

### <a name="sink-association-with-dataset"></a>Sink-koppeling met gegevensset

De gegevensset die u voor uw Sink selecteert, heeft mogelijk een schema dat niet is gedefinieerd in de definitie van de gegevensset. Als er geen schema is gedefinieerd, moet u schema-drift toestaan. Wanneer u een vaste toewijzing hebt gedefinieerd, blijft de toewijzing van logische naar fysieke naam aanwezig in de Sink-trans formatie. Als u de schema definitie van de gegevensset wijzigt, wordt uw Sink-toewijzing mogelijk verbroken. Als u dit wilt voor komen, gebruikt u toewijzing op basis van een regel. Op regels gebaseerde toewijzingen worden gegeneraliseerd, wat inhoudt dat schema wijzigingen in uw gegevensset de toewijzing niet zullen verstoren.

## <a name="file-name-options"></a>Opties voor bestands namen

Bestands namen instellen: 

   * **Standaard**: Spark toestaan om bestanden een naam te geven op basis van de standaard waarden van een deel.
   * **Patroon**: Voer een patroon in voor uw uitvoer bestanden. Bijvoorbeeld: **leningen [n]** maken loans1. CSV, loans2. CSV, enzovoort.
   * **Per partitie**: Voer één bestands naam per partitie in.
   * **Als gegevens in kolom**: Stel het uitvoer bestand in op de waarde van een kolom.
   * **Uitvoer naar één bestand**: met deze optie worden de gepartitioneerde uitvoer bestanden in een enkel bestand met de naam ADF gecombineerd. Als u deze optie wilt gebruiken, moet uw gegevensset worden omgezet in een mapnaam. Houd er ook rekening mee dat deze samenvoeg bewerking mogelijk niet kan worden uitgevoerd op basis van de knooppunt grootte.

> [!NOTE]
> Bestands bewerkingen worden alleen gestart wanneer u de activiteit gegevens stroom uitvoeren uitvoert. Ze worden niet gestart in de modus voor fout opsporing van gegevens stromen.

## <a name="database-options"></a>Database opties

Data base-instellingen kiezen:

![Het tabblad instellingen met de SQL-Sink-opties](media/data-flow/alter-row2.png "SQL-opties")

* **Update methode**: de standaard instelling is invoegen toestaan. Schakel **Invoegen toestaan** uit als u wilt stoppen met het invoegen van nieuwe rijen uit uw bron. Als u rijen wilt bijwerken, upsert of verwijderen, moet u eerst een trans formatie met gewijzigde rijen toevoegen aan de labels voor deze acties. 
* **Tabel opnieuw maken**: drop of maak uw doel tabel voordat de gegevens stroom is voltooid.
* **Tabel afkappen**: Verwijder alle rijen uit de doel tabel voordat de gegevens stroom is voltooid.
* **Batch grootte**: Voer een getal in om schrijf bewerkingen naar segmenten te buckets. Gebruik deze optie voor het laden van grote hoeveel heden gegevens. 
* **Fase ring inschakelen**: gebruik poly base wanneer u Azure data warehouse als uw Sink-gegevensset laadt.
* **SQL-scripts vooraf en post**: Voer SQL-scripts met meerdere regels in die moeten worden uitgevoerd vóór (vóór verwerking) en nadat (na verwerking) gegevens naar uw Sink-Data Base worden geschreven

![scripts voor SQL-verwerking vooraf en na](media/data-flow/prepost1.png "SQL-verwerkings scripts")

> [!NOTE]
> In gegevens stroom kunt u Data Factory omleiden om een nieuwe tabel definitie in de doel database te maken. Als u de tabel definitie wilt maken, stelt u een gegevensset in de Sink-trans formatie in die een nieuwe tabel naam heeft. Selecteer in de SQL-gegevensset onder de tabel naam **bewerken** en voer een nieuwe tabel naam in. Schakel vervolgens in de Sink-trans formatie **Allow schema drift toestaan**in. Stel **import schema** in op **geen**.

![Instellingen van SQL-gegevensset, waarmee wordt aangegeven waar de tabel naam moet worden bewerkt](media/data-flow/dataset2.png "SQL-schema")

> [!NOTE]
> Wanneer u rijen in de data base-Sink bijwerkt of verwijdert, moet u de sleutel kolom instellen. Met deze instelling kan de Alter-Row trans formatie de unieke rij in de gegevens verplaatsings bibliotheek (DML) bepalen.

### <a name="cosmosdb-specific-settings"></a>Specifieke instellingen voor CosmosDB

Wanneer u gegevens in CosmosDB, moet u rekening houden met de volgende extra opties:

* Partitie sleutel: dit is een verplicht veld. Voer een teken reeks in die de partitie sleutel voor uw verzameling vertegenwoordigt. Voorbeeld: ```/movies/title```
* Door Voer: Stel een optionele waarde voor het aantal RUs in dat u wilt Toep assen op uw CosmosDB-verzameling voor elke uitvoering van deze gegevens stroom. Minimum is 400.

## <a name="next-steps"></a>Volgende stappen
Nu u de gegevens stroom hebt gemaakt, voegt u een [gegevens stroom activiteit toe aan uw pijp lijn](concepts-data-flow-overview.md).
