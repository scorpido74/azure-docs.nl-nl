---
title: bestand opnemen
description: bestand opnemen
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 09/12/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: e7a6f7b4ba4219483cd3eb8f4600bc94213df131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973408"
---
Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Een Azure-database maken voor PostgreSQL - Hyperscale (Citus)

Volg deze stappen voor het maken van een Azure Database voor PostgreSQL-server:
1. Klik in de linkerbovenhoek van Azure Portal op **Een resource maken**.
2. Selecteer **Databases** op de pagina **Nieuw** en selecteer **Azure Database voor PostgreSQL** op de pagina **Databases**.
3. Klik voor de optie implementatie op de knop **Maken** onder **De servergroep Hyperscale (Citus).**
4. Vul het formulier voor de gegevens van de nieuwe server als volgt in:
   - Resourcegroep: klik op de **koppeling Nieuwe maken** onder het tekstvak voor dit veld. Voer een naam in zoals **myresourcegroup**.
   - Servergroepnaam: voer een unieke naam in voor de nieuwe servergroep, die ook wordt gebruikt voor een serversubdomein.
   - Gebruikersnaam beheerder: momenteel vereist om de **waardecitus**te zijn en kan niet worden gewijzigd.
   - Wachtwoord: moet ten minste acht tekens lang zijn en tekens bevatten uit drie van de volgende categorieën - Engelse hoofdletters, Engelse kleine letters, cijfers (0-9) en niet-alfanumerieke tekens (!, $, #, %, enzovoort.)
   - Locatie: gebruik de locatie die het dichtst bij uw gebruikers staat om hen de snelste toegang tot de gegevens te geven.

   > [!IMPORTANT]
   > Het wachtwoord voor serverbeheer dat u hier opgeeft, moet worden aangemeld bij de server en de bijbehorende databases. Onthoud of noteer deze informatie voor later gebruik.

5. Klik **op Servergroep configureren**. Laat de instellingen in die sectie ongewijzigd en klik op **Opslaan**.
6. Klik **op Volgende: netwerk>** onder aan het scherm.

7. Klik op het tabblad **Netwerken** op de knop **Doeleindpuntradio.**
   ![Openbaar eindpunt geselecteerd](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Klik op de koppeling **+ Huidig CLIENT-IP-adres toevoegen**.
   ![Client-IP toegevoegd](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > De Azure PostgreSQL-server communiceert via poort 5432. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 5432 mogelijk niet toegestaan door de firewall van uw netwerk. Als dat het zo is, u geen verbinding maken met uw Hyperscale-cluster (Citus), tenzij uw IT-afdeling poort 5432 opent.
   >

9. Klik **op Controleren + maken** en vervolgens **maken** om de server in te richten. De inrichting duurt een paar minuten.
10. De pagina wordt omgeleid om de implementatie te controleren. Wanneer de live status wordt gewijzigd van **Uw implementatie in Uw** implementatie is **voltooid,** klikt u op het **menu-item Uitvoer** aan de linkerkant van de pagina.
11. De uitvoerpagina bevat een hostnaam van de coördinator met een knop ernaast om de waarde naar het klembord te kopiëren. Neem deze informatie op voor later gebruik.

## <a name="connect-to-the-database-using-psql"></a>Verbinding maken met de database met behulp van psql

Wanneer u uw Azure Database voor PostgreSQL-server maakt, wordt een standaarddatabase met de naam **citus** gemaakt. Als u verbinding wilt maken met uw databaseserver, hebt u een verbindingstekenreeks en het beheerderswachtwoord nodig.

1. Verkrijg de verbindingstekenreeks. Klik op de pagina servergroep op het **menu-item Verbindingstekenreeksen.** (Het is onder **Instellingen**.) Zoek de tekenreeks met de vermelding **psql**. Het zal van het formulier zijn:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Kopieer de tekenreeks. U moet {uw\_wachtwoord} vervangen door het eerder gekozen beheerderswachtwoord. Het systeem slaat uw plaintext-wachtwoord niet op en kan het dus niet voor u weergeven in de verbindingstekenreeks.

2. Open een terminalvenster op uw lokale computer.

3. Maak bij de prompt verbinding met uw Azure Database voor PostgreSQL-server met het [psql-hulpprogramma.](https://www.postgresql.org/docs/current/app-psql.html) Geef uw verbindingstekenreeks door in aanhalingstekens, om er zeker van te zijn dat het uw wachtwoord bevat:
   ```bash
   psql "host=..."
   ```

   De volgende opdracht maakt bijvoorbeeld verbinding met het coördinatorknooppunt van de mydemoserver van de **servergroep:**

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
