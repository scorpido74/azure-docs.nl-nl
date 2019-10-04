---
title: Server logboeken configureren en openen in Azure Database for PostgreSQL-één server van de Azure Portal
description: In dit artikel wordt beschreven hoe u de server logboeken van Azure Database for PostgreSQL-één server kunt configureren en openen vanuit de Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c77e708e14d34545754ca38095aedb63ff0172a1
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841510"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Azure Database for PostgreSQL-logboeken met één server configureren en gebruiken vanuit de Azure Portal

U kunt de [Azure database for PostgreSQL logboeken](concepts-server-logs.md) van de Azure Portal configureren, weer geven en downloaden.

## <a name="prerequisites"></a>Vereisten
Voor de stappen in dit artikel moet [Azure database for postgresql server](quickstart-create-server-database-portal.md)zijn geïnstalleerd.

## <a name="configure-logging"></a>Logboek registratie configureren
De toegang tot de query logboeken en fout logboeken configureren. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer uw Azure Database for PostgreSQL-server.

3. Selecteer in de sectie **bewaking** in de zijbalk **Server logboeken**. 

   ![Scherm opname van opties voor Server logboeken](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Selecteer om de server parameters weer te geven de optie **Klik hier om Logboeken in te scha kelen en logboek parameters te configureren**.

5. Wijzig de para meters die u wilt aanpassen. Alle wijzigingen die u aanbrengt in deze sessie, worden in paars gemarkeerd.

   Nadat u de para meters hebt gewijzigd, selecteert u **Opslaan**. Of u kunt uw wijzigingen negeren. 

   ![Scherm opname van opties voor server parameters](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Op de pagina **server parameters** kunt u teruggaan naar de lijst met Logboeken door de pagina te sluiten.

## <a name="view-list-and-download-logs"></a>Lijst weer geven en logboeken downloaden
Nadat de logboek registratie is gestart, kunt u een lijst met beschik bare logboeken bekijken en afzonderlijke logboek bestanden downloaden. 

1. Open Azure Portal.

2. Selecteer uw Azure Database for PostgreSQL-server.

3. Selecteer in de sectie **bewaking** in de zijbalk **Server logboeken**. Op de pagina wordt een lijst met uw logboek bestanden weer gegeven.

   ![Scherm opname van de pagina Server logboeken, met de lijst met gemarkeerde logboeken](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > De naam Conventie van het logboek is **postgresql-yyyy-mm-dd_hh0000. log**. De datum en tijd die in de bestands naam wordt gebruikt, is het tijdstip waarop het logboek is uitgegeven. De logboek bestanden draaien elk uur of 100 MB, afhankelijk van wat het eerste komt.

4. Gebruik, indien nodig, het zoekvak om snel naar een specifiek logboek te beperken, op basis van datum en tijd. De zoek opdracht bevindt zich op de naam van het logboek.

   ![Scherm opname van de pagina Server logboeken, met zoekvak en resultaten gemarkeerd](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Als u afzonderlijke logboek bestanden wilt downloaden, selecteert u het pictogram met de pijl-omlaag naast elk logboek bestand in de tabelrij.

   ![Scherm opname van de pagina Server logboeken, met pictogram met de pijl-omlaag gemarkeerd](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Volgende stappen
- Zie [toegangs server Logboeken in cli](howto-configure-server-logs-using-cli.md) voor meer informatie over het programmatisch downloaden van Logboeken.
- Meer informatie over [Server logboeken](concepts-server-logs.md) in azure database for PostgreSQL. 
- Zie de PostgreSQL-documentatie over [fout rapportage en logboek registratie](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)voor meer informatie over de parameter definities en postgresql-logboek registratie.

