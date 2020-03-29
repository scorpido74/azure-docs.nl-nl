---
title: Logboeken beheren - Azure portal - Azure Database for PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u de serverlogboeken (.logbestanden) configureert en opent in Azure Database voor PostgreSQL - Single Server vanuit de Azure-portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763687"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Azure Database configureren en openen voor PostgreSQL - Single Server-logboeken vanuit de Azure-portal

U de [Azure Database voor PostgreSQL-logboeken](concepts-server-logs.md) configureren, aanbieden en downloaden vanuit de Azure-portal.

## <a name="prerequisites"></a>Vereisten
De stappen in dit artikel vereisen dat u [Azure Database voor PostgreSQL-server hebt.](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Logboekregistratie configureren
Configureer de toegang tot de querylogboeken en foutlogboeken. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer uw Azure Database for PostgreSQL-server.

3. Selecteer **Serverlogboeken**onder de sectie **Controle** in de zijbalk . 

   ![Schermafbeelding van serverlogboekenopties](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Als u de serverparameters wilt bekijken, selecteert u **Klik hier om logboeken in te schakelen en logboekparameters te configureren.**

5. Wijzig de parameters die u moet aanpassen. Alle wijzigingen die u in deze sessie aanbrengt, worden in het paars gemarkeerd.

   Nadat u de parameters hebt gewijzigd, selecteert u **Opslaan**. U ook uw wijzigingen verwijderen. 

   ![Schermafbeelding van opties voor serverparameters](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Op de pagina **Serverparameters** u terugkeren naar de lijst met logboeken door de pagina te sluiten.

## <a name="view-list-and-download-logs"></a>Lijst- en downloadlogboeken weergeven
Nadat de logboekregistratie is begonnen, u een lijst met beschikbare logboeken bekijken en afzonderlijke logboekbestanden downloaden. 

1. Open Azure Portal.

2. Selecteer uw Azure Database for PostgreSQL-server.

3. Selecteer **Serverlogboeken**onder de sectie **Controle** in de zijbalk . De pagina toont een lijst met uw logboekbestanden.

   ![Schermafbeelding van de pagina Serverlogboeken, met lijst met logboeken gemarkeerd](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > De naamgevingsconventie van het logboek is **postgresql-yyyyy-mm-dd_hh0000.log**. De datum en tijd die in de bestandsnaam worden gebruikt, is het tijdstip waarop het logboek is uitgegeven. De logbestanden draaien elk uur of 100 MB, wat het eerst komt.

4. Gebruik indien nodig het zoekvak om snel te beperken tot een specifiek logboek, op basis van datum en tijd. De zoekopdracht staat op de naam van het logboek.

   ![Schermafbeelding van de pagina Serverlogboeken, met het zoekvak en de resultaten gemarkeerd](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Als u afzonderlijke logboekbestanden wilt downloaden, selecteert u het pijl-omlaagpictogram naast elk logboekbestand in de tabelrij.

   ![Schermafbeelding van de pagina Serverlogboeken, met pictogram pijl-omlaag gemarkeerd](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Volgende stappen
- Zie [Access-serverlogboeken in CLI](howto-configure-server-logs-using-cli.md) voor meer informatie over het programmatisch downloaden van logboeken.
- Meer informatie over [serverlogboeken](concepts-server-logs.md) in Azure Database voor PostgreSQL. 
- Zie de PostgreSQL-documentatie over [foutrapportage en logboekregistratie](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)voor meer informatie over de parameterdefinities en PostgreSQL-logboekregistratie.

