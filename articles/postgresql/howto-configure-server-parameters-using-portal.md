---
title: Server parameters configureren-Azure Portal-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u de post gres-para meters in Azure Database for PostgreSQL kunt configureren via de Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: 7a7a98818ba565aad9f2404c77682171514c7573
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86117981"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Server parameters configureren in Azure Database for PostgreSQL-één server via de Azure Portal 
U kunt configuratie parameters voor een Azure Database for PostgreSQL server weer geven, tonen en bijwerken via de Azure Portal.

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- [Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Para meters weer geven en bewerken
1. Open de [Azure Portal](https://portal.azure.com).

2. Selecteer uw Azure Database for PostgreSQL-server.

3. Selecteer in de sectie **instellingen** de optie **server parameters**. Op de pagina ziet u een lijst met para meters, hun waarden en beschrijvingen.
![Overzichts pagina voor para meters](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Selecteer de **vervolg keuzelijst** om de mogelijke waarden voor enumerated-type para meters zoals client_min_messages weer te geven.
![Vervolg keuzelijst opsommen](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Selecteer of Beweeg de muis aanwijzer over de knop met het **i** (informatie) om het bereik van mogelijke waarden voor numerieke para meters zoals cpu_index_tuple_cost weer te geven.
![informatie knop](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Gebruik, indien nodig, het **zoekvak** om te beperken tot een specifieke para meter. De zoek opdracht bevindt zich op de naam en beschrijving van de para meters.
![Zoek resultaten](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Wijzig de parameter waarden die u wilt aanpassen. Alle wijzigingen die u aanbrengt in een sessie, worden in paars gemarkeerd. Wanneer u de waarden hebt gewijzigd, kunt u **Opslaan**selecteren. Of u kunt uw wijzigingen **negeren** .
![Wijzigingen opslaan of negeren](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Als u nieuwe waarden voor de para meters hebt opgeslagen, kunt u altijd terugkeren naar de standaard waarden door **alles opnieuw instellen op de standaard**waarde te selecteren.
![Alles opnieuw instellen op standaard waarden](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over:
- [Overzicht van server parameters in Azure Database for PostgreSQL](concepts-servers.md)
- [Para meters configureren met de Azure CLI](howto-configure-server-parameters-using-cli.md)
