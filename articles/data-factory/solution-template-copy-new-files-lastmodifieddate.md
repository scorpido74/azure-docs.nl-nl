---
title: Nieuwe en gewijzigde bestanden kopiëren op LastModifiedDate
description: Meer informatie over het gebruik van een oplossingssjabloon om nieuwe en gewijzigde bestanden te kopiëren door LastModifiedDate met Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 979977b2dd2eb5742d4e488623c79cb91427f055
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414802"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Nieuwe en gewijzigde bestanden kopiëren op LastModifiedDate met Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt een oplossingssjabloon beschreven die u gebruiken om alleen nieuwe en gewijzigde bestanden te kopiëren door LastModifiedDate van een archief op basis van bestanden naar een bestemmingsarchief. 

## <a name="about-this-solution-template"></a>Over deze oplossingssjabloon

Deze sjabloon selecteert eerst de nieuwe en gewijzigde bestanden alleen op basis van hun kenmerken **LastModifiedDate**en kopieert deze geselecteerde bestanden vervolgens uit het gegevensbronarchief naar het gegevensdoelarchief.

De sjabloon bevat één activiteit:
- **Kopieer** om nieuwe en gewijzigde bestanden alleen te kopiëren met LastModifiedDate van een bestandsarchief naar een bestemmingsarchief.

De sjabloon definieert zes parameters:
-  *FolderPath_Source* is het mappad waar u de bestanden uit het bronarchief lezen. U moet de standaardwaarde vervangen door uw eigen mappad.
-  *Directory_Source* is het submappad waar u de bestanden uit het bronarchief lezen. U moet de standaardwaarde vervangen door uw eigen submappad.
-  *FolderPath_Destination* is het mappad waar u bestanden naar het doelarchief wilt kopiëren. U moet de standaardwaarde vervangen door uw eigen mappad.
-  *Directory_Destination* is het submappad waar u bestanden naar het doelarchief wilt kopiëren. U moet de standaardwaarde vervangen door uw eigen submappad.
-  *LastModified_From* wordt gebruikt om de bestanden te selecteren waarvan het kenmerk LastModifiedDate na of gelijk is aan deze datumtijdwaarde.  Om alleen de nieuwe bestanden te selecteren, die de vorige keer niet zijn gekopieerd, kan deze datumwaarde de tijdswaarde zijn waarop de pijplijn de vorige keer is geactiveerd. U de standaardwaarde '2019-02-01T00:00:00Z' vervangen door uw verwachte LastModifiedDate in utc-tijdzone. 
-  *LastModified_To* wordt gebruikt om de bestanden te selecteren waarvan het kenmerk LastModifiedDate vóór deze datumwaarde is. Om alleen de nieuwe bestanden te selecteren, die de vorige keer niet zijn gekopieerd, kan deze datumwaarde de huidige tijd zijn.  U de standaardwaarde '2019-02-01T00:00:00Z' vervangen door uw verwachte LastModifiedDate in utc-tijdzone. 

## <a name="how-to-use-this-solution-template"></a>Deze oplossingssjabloon gebruiken

1. Ga naar sjabloon **Nieuwe bestanden kopiëren alleen door LastModifiedDate**. Maak een **nieuwe** verbinding met uw bronopslagarchief. Het bronopslagarchief is waar u bestanden wilt kopiëren.

    ![Een nieuwe verbinding met de bron maken](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Maak een **nieuwe** verbinding met uw bestemmingswinkel. Het doelarchief is waar u bestanden naartoe wilt kopiëren. 

    ![Een nieuwe verbinding met de bestemming maken](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Selecteer **Deze sjabloon gebruiken**.

    ![Deze sjabloon gebruiken](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. U ziet de pijplijn die beschikbaar is in het deelvenster, zoals in het volgende voorbeeld wordt weergegeven:

    ![De pijplijn weergeven](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Selecteer **Foutopsporing,** schrijf de waarde voor de **parameters** en selecteer **Voltooien**.  In de onderstaande afbeelding stellen we de parameters als volgt in.
   - **FolderPath_Source** = bronmap
   - **Directory_Source** = submap
   - **FolderPath_Destination** = bestemmingsmap
   - **Directory_Destination** = submap
   - **LastModified_From** = 2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    Het voorbeeld geeft aan dat de bestanden, die voor het laatst zijn gewijzigd binnen de tijdspanne (**2019-02-01T00:00:00Z** naar **2019-03-01T00:00:00Z**) worden gekopieerd van de **bronmap bronmap bronvan het bronpad/submap** naar de bestemmingspadbestemmingsmap/submap . **destinationfolder/subfolder**  U deze vervangen door uw eigen parameters.

    ![De pijplijn uitvoeren](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Bekijk het resultaat. U ziet dat alleen de bestanden die het laatst zijn gewijzigd binnen de geconfigureerde tijdspanne zijn gekopieerd naar het doelarchief.

    ![Bekijk het resultaat](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Nu u een tuimelende windows trigger toevoegen om deze pijplijn te automatiseren, zodat de pijplijn altijd nieuwe en gewijzigde bestanden alleen door LastModifiedDate periodiek kan kopiëren.  Selecteer **Trigger toevoegen**en selecteer **Nieuw/Bewerken**.

    ![Bekijk het resultaat](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. Selecteer **+** **Nieuw**in het venster Triggers toevoegen .

9. Selecteer **Tumbling Window** voor het triggertype en stel Elke **15 minuten in** als herhaling (u overschakelen naar een intervaltijd). Selecteer **Ja** voor geactiveerd vak en selecteer **OK**.

    ![Trigger maken](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Stel de waarde voor de **parameters triggerrun** als volgt in en selecteer **Voltooien**.
    - **FolderPath_Source** = **sourcemap**.  U uw map vervangen in het brongegevensarchief.
    - **Directory_Source** = **submap**.  U uw submap vervangen in het brongegevensarchief.
    - **FolderPath_Destination** = **map met bestemming**.  U uw map vervangen in het doelgegevensarchief.
    - **Directory_Destination** = **submap**.  U uw submap vervangen in het doelgegevensarchief.
    - **LastModified_From** =  **LastModified_From\@trigger().outputs.windowStartTime**.  Het is een systeemvariabele vanaf de trigger die bepaalt wanneer de pijplijn de vorige keer is geactiveerd.
    - **LastModified_To** = **LastModified_To\@trigger().outputs.windowEndTime**.  Het is een systeemvariabele vanaf de trigger die bepaalt wanneer de pijplijn deze keer wordt geactiveerd.
    
    ![Invoerparameters](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Selecteer **Alles publiceren**.
    
    ![Alles publiceren](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Maak nieuwe bestanden in uw bronmap van het gegevensbronarchief.  U wacht nu tot de pijplijn automatisch wordt geactiveerd en alleen de nieuwe bestanden worden gekopieerd naar het doelarchief.

13. Selecteer het tabblad **Monitor** in het linkernavigatiedeelvenster en wacht ongeveer 15 minuten als de herhaling van de trigger is ingesteld op elke 15 minuten. 

14. Bekijk het resultaat. U ziet dat uw pijplijn elke 15 minuten automatisch wordt geactiveerd en alleen de nieuwe of gewijzigde bestanden uit het bronarchief worden gekopieerd naar het doelarchief in elke pijplijnuitvoering.

    ![Bekijk het resultaat](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Data Factory](introduction.md)
