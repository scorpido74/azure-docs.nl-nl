---
title: Gegevens uit Azure Data Lake Storage Gen1 kopiëren naar Gen2
description: Azure Data Factory gebruiken om gegevens uit Azure Data Lake Storage Gen1 naar Gen2 te kopiëren
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: ea0a9de5dde02b45ae34e3e98e24b31fd01fa713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235827"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Gegevens uit Azure Data Lake Storage Gen1 kopiëren naar Gen2 met Azure Data Factory

Azure Data Lake Storage Gen2 is een reeks mogelijkheden die zijn toegedeeld aan big data-analyses die zijn ingebouwd in [Azure Blob-opslag.](../storage/blobs/storage-blobs-introduction.md) U het gebruiken om te communiceren met uw gegevens met behulp van zowel bestandssysteem en object opslag paradigma's.

Als u momenteel Azure Data Lake Storage Gen1 gebruikt, u Azure Data Lake Storage Gen2 evalueren door gegevens van Data Lake Storage Gen1 naar Gen2 te kopiëren met Azure Data Factory.

Azure Data Factory is een volledig beheerde cloudgebaseerde data-integratieservice. U de service gebruiken om het meer te vullen met gegevens uit een uitgebreide reeks on-premises en cloudgebaseerde gegevensopslag en tijd te besparen wanneer u uw analyseoplossingen bouwt. Zie de tabel [met ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met ondersteunde connectors.

Azure Data Factory biedt een scale-out, beheerde oplossing voor gegevensverplaatsing. Door de scale-out architectuur van Data Factory kan het data opnemen bij een hoge doorvoer. Zie [Activiteitsprestaties kopiëren](copy-activity-performance.md)voor meer informatie .

In dit artikel ziet u hoe u het hulpprogramma Gegevensfabriek kopieergegevens gebruiken om gegevens uit Azure Data Lake Storage Gen1 te kopiëren naar Azure Data Lake Storage Gen2. U vergelijkbare stappen uitvoeren om gegevens uit andere soorten gegevensarchieven te kopiëren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Azure Data Lake Storage Gen1-account met gegevens erin.
* Azure Storage-account met Data Lake Storage Gen2 ingeschakeld. Als je geen Opslagaccount hebt, [maak je een account aan.](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer links in het menu **Een brongegevens** > maken +**Analysegegevensfabriek****Data + Analytics** > .
   
   ![Gegevensfabriekselectie in het deelvenster Nieuw](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geef op de pagina **Nieuwe gegevensfabriek** waarden op voor de velden die in de volgende afbeelding worden weergegeven: 
      
   ![Nieuwe pagina Gegevensfabriek](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Naam:** Voer een wereldwijd unieke naam in voor uw Azure-gegevensfabriek. Als u de fout 'Gegevensfabrieksnaam \"LoadADLSDemo\" is niet beschikbaar' ontvangt, voert u een andere naam in voor de gegevensfabriek. Gebruik bijvoorbeeld de naam _**uwnaam**_**ADFTutorialDataFactory**. Maak de gegevensfabriek opnieuw. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    * **Abonnement**: Selecteer uw Azure-abonnement waarin u de gegevensfabriek wilt maken. 
    * **Resourcegroep:** selecteer een bestaande resourcegroep in de vervolgkeuzelijst. U ook de optie **Nieuwe maken** selecteren en de naam van een resourcegroep invoeren. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 
    * **Versie**: Selecteer **V2**.
    * **Locatie**: Selecteer de locatie voor de gegevensfabriek. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven die door de data factory worden gebruikt, kunnen zich op andere locaties of in andere regio's bevinden. 

3. Selecteer **Maken**.
4. Nadat de creatie is voltooid, gaat u naar uw gegevensfabriek. U ziet de startpagina **van Data Factory** zoals weergegeven in de volgende afbeelding: 
   
   ![Startpagina van de gegevensfactory](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Selecteer de tegel **Auteur & Monitor** om de toepassing Gegevensintegratie op een afzonderlijk tabblad te starten.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Gegevens laden in Azure Data Lake Storage Gen2

1. Selecteer **op de** pagina Aan de slag de tegel **Gegevens kopiëren** om het hulpprogramma voor kopieergegevens te starten. 

   ![Tegel gegevensgereedschap kopiëren](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Geef op de pagina **Eigenschappen** **CopyFromADLSGen1ToGen2** op voor het veld **Taaknaam.** Selecteer **Volgende**.

    ![De pagina Eigenschappen](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Selecteer op de pagina **Brongegevensarchief** de optie **+ Nieuwe verbinding maken**.

    ![De pagina Brongegevensarchief](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Selecteer **Azure Data Lake Storage Gen1** in de connectorgalerie en selecteer **Doorgaan**.
    
    ![Pagina Azure Data Lake Storage Gen1-pagina voor brongegevensopslag](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Voer op de pagina **Azure Data Lake Storage Gen1-verbinding** de volgende stappen uit:

   a. Selecteer uw Data Lake Storage Gen1 voor de accountnaam en geef de tenant op of **valideer**deze.
  
   b. Selecteer **Testverbinding** om de instellingen te valideren. Selecteer vervolgens **Voltooien**.
  
   c. U ziet dat er een nieuwe verbinding is gemaakt. Selecteer **Volgende**.
   
   > [!IMPORTANT]
   > In deze doorloop gebruikt u een beheerde identiteit voor Azure-bronnen om uw Azure Data Lake Storage Gen1 te verifiëren. Volg [de](connector-azure-data-lake-store.md#managed-identity)instructies om de beheerde identiteit de juiste machtigingen in Azure Data Lake Storage Gen1 te geven.
   
   ![Azure Data Lake Storage Gen1-account opgeven](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Blader op de pagina **Het invoerbestand of de map kiezen** naar de map en het bestand dat u wilt kopiëren. Selecteer de map of het bestand en selecteer **Kiezen**.

    ![Het invoerbestand of de invoermap kiezen](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Geef het kopieergedrag op door de **opties Bestanden opnieuw kopiëren** en Binaire **kopieeropties** te selecteren. Selecteer **Volgende**.

    ![Uitvoermap opgeven](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Selecteer op de pagina **Bestemmingsgegevensarchief** **+ Nieuwe verbinding** > maken**Azure Data Lake Storage Gen2** > **Continue**.

    ![De pagina Doelgegevensarchief](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Voer op de pagina **Azure Data Lake Storage Gen2-verbinding** opgeven de volgende stappen uit:

   a. Selecteer uw account met Data Lake Storage Gen2 in de vervolgkeuzelijst **Opslagaccountnaam.**
   
   b. Selecteer **Voltooien** om de verbinding te maken. Selecteer **vervolgens Volgende**.
   
   ![Azure Data Lake Storage Gen2-account opgeven](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Voer op de pagina **Het uitvoerbestand of de map kiezen** de naam **copyfromadlsgen1** in als naam van de uitvoermap en selecteer **Volgende**. Data Factory maakt het bijbehorende Azure Data Lake Storage Gen2-bestandssysteem en submappen tijdens het kopiëren als ze niet bestaan.

    ![Uitvoermap opgeven](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Selecteer op de pagina **Instellingen** de optie **Volgende** om de standaardinstellingen te gebruiken.

12. Controleer **op** de pagina Overzicht de instellingen en selecteer **Volgende**.

    ![Overzichtspagina](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Selecteer **op**de pagina Implementatie de optie **Monitor** om de pijplijn te controleren.

    ![De pagina Implementatie](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. De kolom **Acties** bevat onder andere koppelingen om details van de uitvoering van activiteiten te bekijken en de pijplijn opnieuw uit te voeren.

    ![Pijplijnuitvoeringen controleren](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Als u activiteitsruns wilt weergeven die zijn gekoppeld aan de pijplijnrun, selecteert u de koppeling **Activiteitsuitvoering weergeven** in de kolom **Acties.** Omdat er slechts één activiteit (kopieeractiviteit) in de pijplijn is, ziet u slechts één vermelding in de lijst. Als u wilt teruggaan naar de weergave **pijplijnuitvoeringen,** selecteert u de koppeling Pijplijn en bovenaan. Selecteer **Vernieuwen** om de lijst te vernieuwen. 

    ![Uitvoering van activiteiten controleren](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Als u de uitvoeringsgegevens voor elke kopieeractiviteit wilt controleren, selecteert u de koppeling **Details** (afbeelding van de bril) onder **Acties** in de weergave activiteitsbewaking. U details controleren, zoals het volume van gegevens die van de bron naar de gootsteen zijn gekopieerd, gegevensdoorvoer, uitvoeringsstappen met bijbehorende duur en gebruikte configuraties.

    ![Details van het uitvoeren van activiteiten controleren](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Controleer of de gegevens worden gekopieerd naar uw Azure Data Lake Storage Gen2-account.

## <a name="best-practices"></a>Aanbevolen procedures

Zie [Uw big data analytics-oplossingen upgraden van Azure Data Lake Storage Gen1 naar Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)in het algemeen om de upgrade van Azure Data Lake Storage Gen1 naar Azure Data Lake Storage Gen2 te beoordelen. In de volgende secties worden best practices geïntroduceerd voor het gebruik van Data Factory voor een gegevensupgrade van Data Lake Storage Gen1 naar Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Gegevenspartitie voor historische gegevenskopie

- Als uw totale gegevensgrootte in Data Lake Storage Gen1 minder dan 30 TB bedraagt en het aantal bestanden minder dan 1 miljoen bedraagt, u alle gegevens in één exemplaaractiviteit kopiëren.
- Als u een grotere hoeveelheid gegevens moet kopiëren of als u de flexibiliteit wilt om gegevensmigratie in batches te beheren en elk van deze gegevens binnen een bepaald tijdsbestek te voltooien, partitiet u de gegevens. Partitionering vermindert ook het risico op een onverwacht probleem.

Gebruik een proof of concept om de end-to-end oplossing te verifiëren en de kopieerdoorvoer in uw omgeving te testen. Belangrijke proof-of-concept stappen: 

1. Maak één Data Factory-pijplijn met één kopieeractiviteit om verschillende inhoudsvan gegevens uit Data Lake Storage Gen1 naar Data Lake Storage Gen2 te kopiëren om een basislijn voor kopieerprestaties te krijgen. Begin met [data-integratie-eenheden (DIU's)](copy-activity-performance.md#data-integration-units) als 128. 
2. Op basis van de kopieerdoorvoer die u in stap 1 krijgt, berekent u de geschatte tijd die nodig is voor de volledige gegevensmigratie. 
3. (Optioneel) Maak een besturingselementtabel en definieer het bestandsfilter om de bestanden te partitioneren die moeten worden gemigreerd. De manier om de bestanden te partitioneren is door: 

    - Partitie op mapnaam of mapnaam met een wildcardfilter. Wij raden deze methode aan.
    - Partitie door de laatst gewijzigde tijd van een bestand.

### <a name="network-bandwidth-and-storage-io"></a>Netwerkbandbreedte en opslag I/O 

U de gelijktijdigheid van Gegevensfabriek-kopieertaken beheren die gegevens uit Data Lake Storage Gen1 lezen en gegevens naar Data Lake Storage Gen2 schrijven. Op deze manier u het gebruik op die opslag-I/O beheren om te voorkomen dat het normale zakelijke werk aan Data Lake Storage Gen1 tijdens de migratie wordt beïnvloed.

### <a name="permissions"></a>Machtigingen 

In Data Factory ondersteunt de [Data Lake Storage Gen1-connector](connector-azure-data-lake-store.md) serviceprincipal en beheerde identiteit voor Azure-bronverificaties. De [Data Lake Storage Gen2-connector](connector-azure-data-lake-storage.md) ondersteunt accountsleutel, serviceprincipal en beheerde identiteit voor Azure-bronverificaties. Als u Data Factory in staat wilt stellen om alle bestanden of toegangscontrolelijsten (ACL's) te navigeren en te kopiëren, verleent u voldoende machtigingen voor het account dat u verstrekt om alle bestanden te openen, te lezen of te schrijven en ACL's in te stellen als u dat wilt. Geef het een supergebruiker of eigenaarrol tijdens de migratieperiode. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>ACL's behouden van Data Lake Storage Gen1

Zie ACL's van [Data Lake Storage Gen1 behouden](connector-azure-data-lake-storage.md#preserve-acls)als u de ACL's samen met gegevensbestanden wilt repliceren wanneer u een upgrade uitvoert van Data Lake Storage Gen1 naar Data Lake Storage Gen2. 

### <a name="incremental-copy"></a>Incrementele kopie 

U verschillende benaderingen gebruiken om alleen de nieuwe of bijgewerkte bestanden van Data Lake Storage Gen1 te laden:

- Nieuwe of bijgewerkte bestanden laden op tijd partitiemap of bestandsnaam. Een voorbeeld is /2019/05/13/*.
- Nieuwe of bijgewerkte bestanden laden door LastModifiedDate.
- Identificeer nieuwe of bijgewerkte bestanden op een tool of oplossing van derden. Geef vervolgens de bestands- of mapnaam door aan de pijplijn Gegevensfabriek via een parameter of een tabel of bestand. 

De juiste frequentie voor incrementele belasting is afhankelijk van het totale aantal bestanden in Azure Data Lake Storage Gen1 en het volume van nieuwe of bijgewerkte bestanden die elke keer moeten worden geladen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Overzicht van activiteiten](copy-activity-overview.md)
> kopiëren[Azure Data Lake Storage Gen1-connector](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2-connector](connector-azure-data-lake-storage.md)