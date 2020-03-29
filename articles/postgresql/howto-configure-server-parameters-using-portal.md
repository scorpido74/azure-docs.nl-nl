---
title: Serverparameters configureren - Azure portal - Azure Database for PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u de Postgres-parameters in Azure Database voor PostgreSQL configureert via de Azure-portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a9d078fe9aab12b9044733d17a1437801d5130a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763671"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Serverparameters configureren in Azure Database voor PostgreSQL - Single Server via de Azure-portal 
U configuratieparameters voor een Azure-database voor PostgreSQL-server aanbieden, weergeven en bijwerken via de Azure-portal.

## <a name="prerequisites"></a>Vereisten
Om deze how-to-gids te doorlopen, moet u:
- [Azure-database voor PostgreSQL-server](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Parameters weergeven en bewerken
1. Open de [Azure Portal](https://portal.azure.com).

2. Selecteer uw Azure Database for PostgreSQL-server.

3. Selecteer **serverparameters**onder de sectie **INSTELLINGEN** . De pagina bevat een lijst met parameters, hun waarden en beschrijvingen.
![Overzichtspagina voor parameters](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Selecteer de **vervolgkeuzeknop** om de mogelijke waarden voor opgesomde parameters zoals client_min_messages te bekijken.
![Opsommen daling naar beneden](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Selecteer of zweef over **de** i(informatie)knop om het bereik van mogelijke waarden voor numerieke parameters zoals cpu_index_tuple_cost te zien.
![knop informatie](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Gebruik indien nodig het **zoekvak** om te beperken tot een specifieke parameter. De zoekopdracht is op de naam en beschrijving van de parameters.
![Zoekresultaten](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Wijzig de parameterwaarden die u wilt aanpassen. Alle wijzigingen die u in een sessie aanbrengt, worden in het paars gemarkeerd. Zodra u de waarden hebt gewijzigd, u **Opslaan**selecteren. Of u uw wijzigingen **verwijderen.**
![Wijzigingen opslaan of verwijderen](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Als u nieuwe waarden voor de parameters hebt opgeslagen, u alles altijd terugzetten naar de standaardwaarden door **Alles opnieuw instellen op standaard te selecteren.**
![Alles opnieuw instellen op standaard](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over:
- [Overzicht van serverparameters in Azure Database voor PostgreSQL](concepts-servers.md)
- [Parameters configureren met de Azure CLI](howto-configure-server-parameters-using-cli.md)
