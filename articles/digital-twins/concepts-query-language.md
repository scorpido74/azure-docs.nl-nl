---
title: Querytaal
titleSuffix: Azure Digital Twins
description: Meer informatie over de basis principes van de Azure Digital Apparaatdubbels-query taal.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 29e1fa603600e246031f2a86aae3b0876b4910ba
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562465"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Over de query taal voor Azure Digital Apparaatdubbels

Het midden van Azure Digital Apparaatdubbels is het [**dubbele diagram**](concepts-twins-graph.md), dat is geconstrueerd op basis van **digitale apparaatdubbels** en **relaties**. Deze grafiek kan worden opgevraagd om informatie te krijgen over de digitale apparaatdubbels en de relaties hierin. Deze query's worden geschreven in een aangepaste SQL-achtige query taal, waarnaar wordt verwezen als de **Azure Digital apparaatdubbels-query taal**.

Als u een query wilt verzenden naar de service vanuit een client-app, gebruikt u de Azure Digital Apparaatdubbels- [**query-API**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview). Hierdoor kunnen ontwikkel aars query's schrijven en filters toep assen om sets van digitale apparaatdubbels te vinden in de dubbele grafiek, en andere informatie over het Azure Digital Apparaatdubbels-scenario.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het schrijven van query's en het bekijken van voor beelden van client code in [*How-to: Query's uitvoeren op de dubbele grafiek*](how-to-query-graph.md).
