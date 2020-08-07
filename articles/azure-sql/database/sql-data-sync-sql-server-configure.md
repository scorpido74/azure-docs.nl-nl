---
title: SQL Data Sync instellen
description: Deze zelf studie laat zien hoe u SQL Data Sync instelt voor Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/14/2019
ms.openlocfilehash: bd1362db2e70d4f9f46d80b00805856e08aedac4
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987338"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>Zelf studie: SQL Data Sync instellen tussen data bases in Azure SQL Database en SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelf studie leert u hoe u SQL Data Sync kunt instellen door een synchronisatie groep te maken die zowel Azure SQL Database als SQL Server exemplaren bevat. De synchronisatie groep is aangepast geconfigureerd en gesynchroniseerd op het schema dat u hebt ingesteld.

In de zelf studie wordt ervan uitgegaan dat u ten minste een eerdere ervaring hebt met SQL Database en SQL Server.

Zie voor een overzicht van SQL Data Sync [gegevens synchroniseren in de Cloud en on-premises data bases met SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md).

Zie voor voor beelden van Power shell voor informatie over het configureren van SQL Data Sync de [synchronisatie tussen data bases in SQL database](scripts/sql-data-sync-sync-data-between-sql-databases.md) of [tussen data bases in Azure SQL database en SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> SQL Data Sync biedt op dit moment **geen** ondersteuning voor het beheerde exemplaar van Azure SQL.

## <a name="create-sync-group"></a>Synchronisatie groep maken

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw data base te vinden in SQL database. Zoek en selecteer **SQL-data bases**.

    ![Zoeken naar data bases, Microsoft Azure-portal](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. Selecteer de data base die u wilt gebruiken als de hub-Data Base voor de gegevens synchronisatie.

    ![Selecteer in de lijst met data bases Microsoft Azure-portal](./media/sql-data-sync-sql-server-configure/select-sql-database.png)

    > [!NOTE]
    > De hub-data base is het centrale eind punt van een synchronisatie topologie waarin een synchronisatie groep meerdere database eindpunten heeft. Alle andere leden databases met eind punten in de synchronisatie groep, synchroniseren met de hub-data base.

1. Selecteer in het menu **SQL database** voor de geselecteerde data base de optie **synchroniseren met andere data bases**.

    ![Synchroniseren met andere data bases, Microsoft Azure-portal](./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png)

1. Selecteer op de pagina **synchroniseren met andere data bases** de optie **nieuwe synchronisatie groep**. De pagina **nieuwe synchronisatie groep** wordt geopend met een **synchronisatie groep maken (stap 1)** gemarkeerd.

   ![Stap 1-instellingen](./media/sql-data-sync-sql-server-configure/stepone.png)

   Wijzig op de pagina **gegevens synchronisatie groep maken** de volgende instellingen:

   | Instelling                        | Beschrijving |
   | ------------------------------ | ------------------------------------------------- |
   | **Naam van synchronisatie groep** | Voer een naam in voor de nieuwe synchronisatie groep. Deze naam is verschillend van de naam van de data base zelf. |
   | **Meta gegevens database synchroniseren** | Kies ervoor om een Data Base te maken (aanbevolen) of om een bestaande Data Base te gebruiken.<br/><br/>Als u **nieuwe data base**kiest, selecteert u **nieuwe data base maken.** Geef op de pagina **SQL database** de naam en configureer de nieuwe data base en selecteer **OK**.<br/><br/>Als u **bestaande Data Base gebruiken**kiest, selecteert u de data base in de lijst. |
   | **Automatische synchronisatie** | Selecteer **aan** of **uit**.<br/><br/>Als u kiest **voor**, voert u een getal in en selecteert u **seconden**, **minuten**, **uren**of **dagen** in het gedeelte **synchronisatie frequentie** .<br/> De eerste synchronisatie begint nadat de geselecteerde interval periode is verstreken vanaf het moment dat de configuratie wordt opgeslagen.|
   | **Conflict oplossing** | Selecteer **hub winnen** of **lid worden gewonnen**.<br/><br/>**Hub wint** houdt in dat er conflicten optreden, maar gegevens in de hub-data base overschrijft de conflicterende gegevens in de leden database.<br/><br/>**Lid Win** : als er conflicten optreden, worden de gegevens in de data base van het lid overschreven met conflicterende gegevens in de hub-data base. |

   > [!NOTE]
   > Micro soft raadt aan om een nieuwe, lege data base te maken voor gebruik als de **meta gegevens database voor synchronisatie**. Gegevens synchronisatie maakt tabellen in deze data base en voert een frequente werk belasting uit. Deze data base wordt gedeeld als de **meta gegevens database** voor synchronisatie groepen in een geselecteerde regio en een abonnement. U kunt de data base of de naam niet wijzigen zonder alle synchronisatie groepen en synchronisatie agenten in de regio te verwijderen.

   Selecteer **OK** en wacht totdat de synchronisatie groep is gemaakt en geïmplementeerd.

## <a name="add-sync-members"></a>Synchronisatie leden toevoegen

Nadat de nieuwe synchronisatie groep is gemaakt en geïmplementeerd, kunt u **synchronisatie leden toevoegen (stap 2)** op de pagina **nieuwe synchronisatie groep** gemarkeerd.

Voer in de sectie **hub-data base** de bestaande referenties in voor de server waarop de hub-data base zich bevindt. Voer in deze sectie geen *nieuwe* referenties in.

![Stap 2-instellingen](./media/sql-data-sync-sql-server-configure/steptwo.png)

### <a name="to-add-a-database-in-azure-sql-database"></a>Een Data Base toevoegen in Azure SQL Database

Voeg in de sectie **lid data base** eventueel een data base in Azure SQL database toe aan de synchronisatie groep door **een Azure SQL database toevoegen**te selecteren. De pagina **Azure SQL database configureren** wordt geopend.

  ![Stap 2: data base configureren](./media/sql-data-sync-sql-server-configure/steptwo-configure.png)

  Wijzig op de pagina **Azure SQL database configureren** de volgende instellingen:

  | Instelling                       | Beschrijving |
  | ----------------------------- | ------------------------------------------------- |
  | **Lidnaam synchroniseren** | Geef een naam op voor het nieuwe lid Sync. Deze naam is niet hetzelfde als de naam van de data base zelf. |
  | **Abonnement** | Selecteer het bijbehorende Azure-abonnement voor facturerings doeleinden. |
  | **Azure SQL Server** | Selecteer de bestaande server. |
  | **Azure SQL Database** | Selecteer de bestaande data base in SQL Database. |
  | **Synchronisatie richtingen** | Selecteer **bidirectionele synchronisatie**, **naar de hub**of **vanuit de hub**. |
  | **Gebruikers naam** en **wacht woord** | Voer de bestaande referenties in voor de server waarop de member-data base zich bevindt. Voer in deze sectie geen *nieuwe* referenties in. |

  Selecteer **OK** en wacht totdat het nieuwe Sync-lid is gemaakt en geïmplementeerd.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>Een SQL Server-Data Base toevoegen

Voeg in de sectie **lid data base** optioneel een SQL Server Data Base toe aan de synchronisatie groep door **een on-premises data base toevoegen**te selecteren. De pagina **on-premises configureren** wordt geopend, waar u de volgende dingen kunt doen:

1. Selecteer **de gateway voor de synchronisatie agent kiezen**. De pagina **synchronisatie agent selecteren** wordt geopend.

   ![Een synchronisatie agent maken](./media/sql-data-sync-sql-server-configure/steptwo-agent.png)

1. Kies op de pagina **de synchronisatie agent kiezen** of u een bestaande agent wilt gebruiken of een agent wilt maken.

   Als u **bestaande agents**kiest, selecteert u de bestaande agent in de lijst.

   Als u **een nieuwe agent maken**kiest, doet u het volgende:

   1. Down load de Data Sync-agent via de koppeling en installeer deze op de computer waarop de SQL Server zich bevindt. U kunt de agent ook rechtstreeks vanuit [Azure SQL Data Sync-agent](https://www.microsoft.com/download/details.aspx?id=27693)downloaden.

      > [!IMPORTANT]
      > U moet de uitgaande TCP-poort 1433 in de firewall openen om de client agent te laten communiceren met de-server.

   1. Voer een naam in voor de agent.

   1. Selecteer **maken en Genereer sleutel** en kopieer de agent sleutel naar het klem bord.

   1. Selecteer **OK** om de pagina **synchronisatie agent selecteren** te sluiten.

1. Zoek op de SQL Server computer de agent voor clientsynchronisatie-app en voer deze uit.

   ![De Data Sync-client agent-app](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. Selecteer **agent sleutel verzenden**in de app synchronisatie agent. Het dialoog venster **synchronisatie meta gegevens database configuratie** wordt geopend.

    1. Plak in het dialoog venster **meta gegevens database configuratie synchroniseren** in de agent sleutel die is gekopieerd van de Azure Portal. Geef ook de bestaande referenties op voor de server waarop de meta gegevens database zich bevindt. (Als u een meta gegevens database hebt gemaakt, bevindt deze data base zich op dezelfde server als de hub-data base.) Selecteer **OK** en wacht totdat de configuratie is voltooid.

        ![De agent sleutel en Server referenties invoeren](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Als u een firewall fout krijgt, maakt u een firewall regel op Azure om binnenkomend verkeer van de SQL Server computer toe te staan. U kunt de regel hand matig maken in de portal of in SQL Server Management Studio (SSMS). In SSMS maakt u verbinding met de hub-Data Base op Azure door de naam ervan in te voeren als <hub_database_name>. database.windows.net.

    1. Selecteer **registreren** om een SQL Server Data Base bij de agent te registreren. Het dialoog venster **SQL Server configuratie** wordt geopend.

        ![Een SQL Server-Data Base toevoegen en configureren](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. Kies in het dialoog venster **SQL Server configuratie** om verbinding te maken met behulp van SQL Server-verificatie of Windows-verificatie. Als u SQL Server verificatie kiest, voert u de bestaande referenties in. Geef de SQL Server naam en de naam op van de data base die u wilt synchroniseren en selecteer **verbinding testen** om uw instellingen te testen. Selecteer vervolgens **Opslaan** en de geregistreerde data base wordt weer gegeven in de lijst.

        ![SQL Server Data Base is nu geregistreerd](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. Sluit de app agent voor clientsynchronisatie.

1. Selecteer in de portal op de pagina **on-premises configureren** **de optie de data base selecteren**.

1. Geef op de pagina **Data Base selecteren** in het veld **synchronisatie lidnaam** een naam op voor het nieuwe lid Sync. Deze naam is verschillend van de naam van de data base zelf. Selecteer de data base in de lijst. Selecteer in het veld **synchronisatie richting** **bidirectionele synchronisatie**, **naar de hub**of **vanuit de hub**.

    ![De on-premises data base selecteren](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. Selecteer **OK** om de pagina **Data Base selecteren** te sluiten. Selecteer vervolgens **OK** om de pagina **on-premises configureren** te sluiten en te wachten tot het nieuwe Sync-lid is gemaakt en geïmplementeerd. Selecteer ten slotte **OK** om de pagina **synchronisatie leden selecteren** te sluiten.

> [!NOTE]
> Als u verbinding wilt maken met SQL Data Sync en de lokale agent, voegt u uw gebruikers naam toe aan de rol *DataSync_Executor*. De gegevens synchronisatie maakt deze rol op het SQL Server-exemplaar.

## <a name="configure-sync-group"></a>Synchronisatie groep configureren

Nadat de leden van de nieuwe synchronisatie groep zijn gemaakt en geïmplementeerd, **configureert u de synchronisatie groep (stap 3)** is gemarkeerd op de pagina **nieuwe synchronisatie groep** .

![Stap 3-instellingen](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. Selecteer op de pagina **tabellen** een data base in de lijst met leden van synchronisatie groep en selecteer **schema vernieuwen**.

1. Selecteer in de lijst de tabellen die u wilt synchroniseren. Standaard zijn alle kolommen geselecteerd, dus Schakel het selectie vakje uit voor de kolommen die u niet wilt synchroniseren. Zorg ervoor dat u de primaire-sleutel kolom geselecteerd laten.

1. Selecteer **Opslaan**.

1. Standaard worden data bases pas gesynchroniseerd wanneer ze zijn gepland of hand matig worden uitgevoerd. Als u een hand matige synchronisatie wilt uitvoeren, bladert u naar uw data base in SQL Database in het Azure Portal, selecteert **u synchroniseren met andere data bases**en selecteert u de synchronisatie groep. De pagina **gegevens synchronisatie** wordt geopend. Selecteer **Synchroniseren**.

    ![Hand matige synchronisatie](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>Veelgestelde vragen

**Hoe vaak kan de gegevens synchronisatie mijn gegevens synchroniseren?**

De minimale duur tussen synchronisaties is vijf minuten.

**Maakt SQL Data Sync volledig tabellen?**

Als synchronisatieschematabellen ontbreken in de doeldatabase, worden ze in SQL Data Sync gemaakt met de kolommen die u hebt geselecteerd. Dit resulteert echter niet in een volledig beeld schema om de volgende redenen:

- Alleen kolommen die u selecteert, worden gemaakt in de doeltabel. Kolommen die niet zijn geselecteerd, worden genegeerd.
- Alleen geselecteerde kolomindexen worden gemaakt in de doeltabel. Voor niet geselecteerde kolommen worden deze indexen genegeerd.
- Er worden geen indexen voor kolommen van het type XML gemaakt.
- Er zijn geen CHECK-beperkingen gemaakt.
- Triggers op de bron tabellen zijn niet gemaakt.
- Er worden geen weer gaven en opgeslagen procedures gemaakt.

Vanwege deze beperkingen raden we u het volgend aan:

- Maak voor productie omgevingen het volledige schema zelf.
- Wanneer u met de service experimenteert, gebruikt u de functie voor automatische inrichting.

**Waarom worden tabellen weer geven die ik niet heb gemaakt?**

Gegevens synchronisatie maakt aanvullende tabellen in de Data Base voor het bijhouden van wijzigingen. Verwijder deze of gegevens synchronisatie werkt niet meer.

**Zijn mijn gegevens convergent na een synchronisatie?**

Niet noodzakelijkerwijs. Maak een synchronisatie groep met een hub en drie spaken (A, B en C), waarbij synchronisaties hub zijn naar A, hub naar B en hub naar C. Als er een wijziging wordt aangebracht in data base A *na* de hub naar een synchronisatie, wordt die wijziging niet naar data base B of Data Base C geschreven tot de volgende synchronisatie taak.

**Hoe kan ik schema wijzigingen in een synchronisatie groep ophalen?**

Zorg ervoor dat alle schema wijzigingen hand matig worden gemaakt en door gegeven.

1. De schema wijzigingen hand matig repliceren naar de hub en naar alle synchronisatie leden.
1. Het synchronisatie schema bijwerken.

Voor het toevoegen van nieuwe tabellen en kolommen:

Nieuwe tabellen en kolommen hebben geen invloed op de huidige synchronisatie en de synchronisatie van gegevens negeert deze totdat ze aan het synchronisatie schema worden toegevoegd. Volg de volg orde bij het toevoegen van nieuwe database objecten:

1. Nieuwe tabellen of kolommen toevoegen aan de hub en aan alle synchronisatie leden.
1. Nieuwe tabellen of kolommen aan het synchronisatie schema toevoegen.
1. Begin met het invoegen van waarden in de nieuwe tabellen en kolommen.

Voor het wijzigen van het gegevens type van een kolom:

Wanneer u het gegevens type van een bestaande kolom wijzigt, blijft de gegevens synchronisatie werken, zolang de nieuwe waarden overeenkomen met het oorspronkelijke gegevens type dat is gedefinieerd in het synchronisatie schema. Als u bijvoorbeeld het type in de bron database wijzigt van **int** in **bigint**, blijft de gegevens synchronisatie werken totdat u een waarde hebt ingevoegd die te groot is voor het gegevens type **int** . Als u de wijziging wilt volt ooien, repliceert u de schema wijziging hand matig naar de hub en naar alle synchronisatie leden en werkt u vervolgens het synchronisatie schema bij.

**Hoe kan ik een Data Base met gegevens synchronisatie exporteren en importeren?**

Nadat u een Data Base als een *. Bacpac* -bestand hebt geëxporteerd en het bestand hebt geïmporteerd om een Data Base te maken, gaat u als volgt te werk om gegevens synchronisatie in de nieuwe Data Base te gebruiken:

1. De gegevens synchronisatie objecten en aanvullende tabellen op de nieuwe data base opschonen met behulp van [Dit script](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Met het script worden alle vereiste gegevens synchronisatie objecten uit de data base verwijderd.
1. Maak de synchronisatie groep opnieuw met de nieuwe data base. Als u de oude synchronisatie groep niet meer nodig hebt, verwijdert u deze.

**Waar vind ik informatie over de client agent?**

Voor veelgestelde vragen over de client agent raadpleegt u [Veelgestelde vragen](sql-data-sync-agent-overview.md#agent-faq)over de agent.

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd. U hebt een synchronisatie groep gemaakt die zowel een SQL Database exemplaar als een SQL Server-Data Base bevat.

Zie de volgende onderwerpen voor meer informatie over SQL Data Sync:

- [Data Sync-agent voor Azure SQL Data Sync](sql-data-sync-agent-overview.md)
- [Best practices](sql-data-sync-best-practices.md) en [Problemen met Azure SQL Data Sync oplossen](sql-data-sync-troubleshoot.md)
- [SQL Data Sync bewaken met Azure Monitor-logboeken](sql-data-sync-monitor-sync.md)
- [Het synchronisatieschema bijwerken met Transact-SQL](sql-data-sync-update-sync-schema.md) of [PowerShell](scripts/update-sync-schema-in-sync-group.md)

Zie de volgende onderwerpen voor meer informatie over SQL Database:

- [Overzicht van SQL Database](sql-database-paas-overview.md)
- [Database Lifecycle Management (DLM)](https://msdn.microsoft.com/library/jj907294.aspx)
