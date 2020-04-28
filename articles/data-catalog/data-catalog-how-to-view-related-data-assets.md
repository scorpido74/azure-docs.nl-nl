---
title: Gerelateerde gegevensassets weer geven in Azure Data Catalog
description: In dit artikel wordt uitgelegd hoe u gerelateerde gegevensassets van een geselecteerde gegevensasset in Azure Data Catalog kunt weer geven.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 212ba647e6eb44e800a589928620f56fba65107c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "68737020"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Gerelateerde gegevensassets in Azure Data Catalog weer geven?
Met Azure Data Catalog kunt u gegevensassets weer geven die zijn gerelateerd aan een geselecteerd gegevens activum en relaties tussen deze activa weer geven. 

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen 
Wanneer u gegevensassets registreert bij de volgende gegevens bronnen, registreert Azure Data Catalog automatisch meta gegevens over het koppelen van relaties tussen de geselecteerde gegevensassets. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Als Data Catalog relatie tussen twee gegevensassets wilt importeren, moet u beide activa tegelijk registreren. Als u een van deze afzonderlijk hebt toegevoegd, voegt u deze opnieuw toe en de andere gegevensasset om de relatie tussen de gegevens te importeren.

## <a name="view-related-data-assets"></a>Gerelateerde gegevensassets weergeven
Als u gegevensassets wilt weer geven die zijn gerelateerd aan een geselecteerde gegevensset, gebruikt u het tabblad **relaties** zoals wordt weer gegeven in de volgende afbeelding: 

![Gerelateerde gegevensassets Azure Data Catalog weer geven](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

In dit voor beeld zijn er twee relaties voor de geselecteerde **tabel productsubcategory** -gegevens Asset: 

- De kolom ProductSubcategoryID van de product tabel heeft een refererende-sleutel relatie met de kolom ProductSubcategoryID van de geselecteerde tabel productsubcategory-tabel. 
- De kolom ProductCategoryID van de tabel tabel productsubcategory heeft een refererende-sleutel relatie met de kolom ProductCategoryID van de geselecteerde ProductCategory-tabel.

> [!NOTE]
> Let op de richting van de pijl in de structuur weergave relaties.  

Als u meer informatie wilt weer geven, bijvoorbeeld de volledig gekwalificeerde naam van de kolom, plaatst u de muis aanwijzer op en ziet u een pop-upvenster dat lijkt op de volgende afbeelding: 

![Pop-up Azure Data Catalog-relatie](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Als u relaties wilt opnemen tussen activa die al zijn geregistreerd, registreert u deze opnieuw.

## <a name="next-steps"></a>Volgende stappen
- [Gegevensassets beheren](data-catalog-how-to-manage.md)