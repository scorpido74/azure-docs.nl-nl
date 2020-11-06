---
title: Een door de gebruiker gedefinieerd herstel punt maken voor een toegewezen SQL-groep
description: Een herstel punt voor een toegewezen SQL-groep maken.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0835fb48d00fe5277732f34fd6b0de1448f6a78
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332079"
---
# <a name="user-defined-restore-points"></a>Door de gebruiker gedefinieerde herstel punten

In dit artikel leert u hoe u een nieuw, door de gebruiker gedefinieerd herstel punt maakt voor een toegewezen SQL-groep in azure Synapse Analytics met behulp van de Azure Portal.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Door de gebruiker gedefinieerde herstel punten maken via de Azure Portal

Door de gebruiker gedefinieerde herstel punten kunnen ook worden gemaakt via Azure Portal.

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com/) -account.

2. Navigeer naar de exclusieve SQL-groep waarvoor u een herstel punt wilt maken.

3. Selecteer **overzicht** in het linkerdeel venster en selecteer **+ Nieuw herstel punt**. Als de knop Nieuw herstel punt niet is ingeschakeld, moet u ervoor zorgen dat de toegewezen SQL-groep niet wordt onderbroken.

    ![Nieuw herstel punt](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Geef een naam op voor het herstel punt dat door de gebruiker is gedefinieerd en klik op **Toep assen**. Door de gebruiker gedefinieerde herstel punten hebben een standaard Bewaar periode van zeven dagen.

    ![Naam van herstel punt](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande toegewezen SQL-groep herstellen](restore-sql-pool.md)

