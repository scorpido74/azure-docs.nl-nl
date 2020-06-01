---
title: SQL Database DAC-pakketten gebruiken-Azure SQL Edge (preview)
description: Meer informatie over het gebruik van dacpacs in Azure SQL Edge (preview)
keywords: SQL-Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 0ddd1544c6a51ff1e2f98a28e40d9eb2ee0b47c7
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233286"
---
# <a name="sql-database-dac-packages-in-sql-edge"></a>SQL Database DAC-pakketten in SQL-rand

Azure SQL Edge (preview) is een geoptimaliseerde relationele data base-engine die is afgestemd op IoT-en Edge-implementaties. Het is gebaseerd op de nieuwste versies van de Microsoft SQL Server data base-engine, waarmee toonaangevende prestaties, beveiliging en verwerkings mogelijkheden voor query's worden geboden. Naast de toonaangevende mogelijkheden voor het beheer van relationele data bases van SQL Server biedt Azure SQL Edge ingebouwde streaming-mogelijkheden voor realtime analyse en complexe gebeurtenis verwerking.

Azure SQL Edge biedt ook een systeem eigen implementatie van SqlPackage. exe, waarmee u een [SQL database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) -pakket kunt implementeren tijdens de implementatie van SQL Edge. SQL Database dacpacs kan worden geïmplementeerd op SQL Edge met behulp van de para meter SqlPackage die wordt weer gegeven via de `module twin's desired properties` optie van de SQL Edge-module:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Veld | Beschrijving |
|------|-------------|
| SqlPackage | Azure Blob-opslag-URI voor het *. zip-bestand dat de SQL Database DAC-pakket bevat.
| ASAJobInfo | Azure Blob-opslag-URI voor de ASA Edge-taak.

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Een SQL Database DAC-pakket gebruiken met SQL Edge

Voer de volgende stappen uit om een SQL Database DAC-pakket (*. dacpac) met SQL Edge te gebruiken:

1. Een SQL Database DAC-pakket maken of extra heren. Zie [een DAC extra heren uit een Data Base](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) voor informatie over het genereren van een DAC-pakket voor een bestaande SQL Server-Data Base.

2. Zip-dacpac en upload het naar een Azure Blob Storage-account. Zie [blobs uploaden, downloaden en weer geven met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)voor meer informatie over het uploaden van bestanden naar Azure Blob-opslag.

3. Genereer een Shared Access Signature voor het zip-bestand met behulp van de Azure Portal. Zie [Toegang delegeren met Shared Access signatures (SAS)](../storage/common/storage-sas-overview.md)voor meer informatie.

4. Werk de configuratie van de SQL Edge-module bij om de gedeelde toegangs-URI voor het DAC-pakket op te geven. Voer de volgende stappen uit om de SQL Edge-module bij te werken:

    1. Ga in het Azure Portal naar de implementatie van IoT Hub.

    2. Selecteer **IoT Edge** in het linkerdeelvenster.

    3. Zoek en selecteer op de pagina **IOT Edge** de IOT Edge waar de SQL Edge-module is geïmplementeerd.

    4. Selecteer op de pagina **apparaat IOT edge apparaten** de optie **module instellen**.

    5. Selecteer op de pagina **modules instellen** de optie **configureren** voor de SQL-rand module.

    6. Selecteer in het deel venster **aangepaste Modules IOT Edge** de **gewenste eigenschappen van module configureren**. Werk de gewenste eigenschappen bij om de URI voor de `SQLPackage` optie op te geven, zoals wordt weer gegeven in het volgende voor beeld.

        > [!NOTE]
        > De SAS-URI in de volgende JSON is slechts een voor beeld. Vervang de URI door de daad werkelijke URI van uw implementatie.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac",
                }
            }
        ```

    7. Selecteer **Opslaan**.

    8. Selecteer op de pagina **modules instellen** de optie **volgende**.

    9. Selecteer op de pagina **modules instellen** de optie **volgende** en vervolgens **verzenden**.

5. Na het bijwerken van de module wordt het DAC-pakket bestand gedownload, uitgepakt en geïmplementeerd op basis van het SQL Edge-exemplaar.

Op elke keer dat de Azure SQL Edge-container opnieuw wordt opgestart, wordt het *. dacpac-bestands pakket gedownload en geëvalueerd op wijzigingen. Als er een nieuwe versie van het dacpac-bestand wordt aangetroffen, worden de wijzigingen geïmplementeerd in de data base in SQL-rand.

## <a name="next-steps"></a>Volgende stappen

- [Implementeer SQL Edge via Azure Portal](deploy-portal.md).
- [Gegevens streamen](stream-data.md)
- [Machine learning en AI met ONNX in SQL Edge (preview-versie)](onnx-overview.md)
