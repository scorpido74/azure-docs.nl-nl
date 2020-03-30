---
title: Gerelateerde gegevenselementen weergeven in Azure-gegevenscatalogus
description: In dit artikel wordt uitgelegd hoe u gerelateerde gegevenselementen van een geselecteerd gegevenselement weergeven in Azure-gegevenscatalogus.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 212ba647e6eb44e800a589928620f56fba65107c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68737020"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Hoe u gerelateerde gegevenselementen weergeven in Azure Data Catalog?
Met Azure Data Catalog u gegevenselementen met betrekking tot een geselecteerd gegevensitem bekijken en relaties tussen deze gegevens bekijken. 

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen 
Wanneer u gegevenselementen registreert uit de volgende gegevensbronnen, registreert Azure Data Catalog automatisch metagegevens over joinrelaties tussen de geselecteerde gegevenselementen. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Als u de relatie tussen twee gegevenselementen wilt importeren, moet u beide elementen tegelijkertijd registreren. Als u een van deze afzonderlijk had toegevoegd, voegt u deze opnieuw toe en het andere gegevenselement om de relatie tussen deze gegevens te importeren.

## <a name="view-related-data-assets"></a>Gerelateerde gegevensassets weergeven
Als u gegevenselementen wilt weergeven die gerelateerd zijn aan een geselecteerde gegevensset, gebruikt u het tabblad **Relaties** zoals weergegeven in de volgende afbeelding: 

![Azure-gegevenscatalogus - Gerelateerde gegevenselementen weergeven](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

In dit voorbeeld zijn er twee relaties voor het geselecteerde **gegevensitem ProductSubcategorie:** 

- De kolom ProductSubcategoryID van de producttabel heeft een buitenlandse sleutelrelatie met de productsubcategorieID-kolom van de geselecteerde tabel ProductSubcategorie. 
- De kolom ProductCategoryID van de tabel ProductSubCategorie heeft een buitenlandse sleutelrelatie met de kolom ProductCategoryID van de geselecteerde tabel Productcategorie.

> [!NOTE]
> Let op de richting van de pijl in de structuurweergave relaties.  

Als u meer details wilt zien, zoals de volledig gekwalificeerde naam van de kolom, beweegt u de muis omen ziet u een pop-up die vergelijkbaar is met de volgende afbeelding: 

![Azure-gegevenscatalogus - pop-up relatie](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Als u relaties wilt opnemen tussen activa die al zijn geregistreerd, moet u deze activa opnieuw registreren.

## <a name="next-steps"></a>Volgende stappen
- [Gegevensassets beheren](data-catalog-how-to-manage.md)