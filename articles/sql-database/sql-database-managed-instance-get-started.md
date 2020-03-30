---
title: 'Azure-portal: een beheerde instantie maken'
description: Maak een SQL Database beheerde instantie, netwerkomgeving en client-VM voor toegang.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: 25128442cd922f6b9130586e245695b6880f661c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80257611"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Snelstart: een azure SQL-databasebeheerexemplaar maken

Met deze quickstart u een Azure SQL [Database-beheerde instantie](sql-database-managed-instance.md) maken in Azure-portal.

> [!IMPORTANT]
> Zie Ondersteunde [regio's](sql-database-managed-instance-resource-limits.md#supported-regions) en [ondersteunde abonnementstypen](sql-database-managed-instance-resource-limits.md#supported-subscription-types)voor beperkingen.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/)aan.

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Een beheerd exemplaar maken

In de volgende stappen ziet u hoe u een beheerde instantie maakt:

1. Selecteer **Azure SQL** in het linkermenu van Azure-portal. Als **Azure SQL** niet in de lijst staat, selecteert u Alle **services**en voert u Azure **SQL** in het zoekvak in.
2. Selecteer **+Toevoegen** om de **optiepagina SQL-implementatie selecteren** te openen. U aanvullende informatie over een azure SQL Database-beheerde instantie weergeven door **Details weergeven** op de tegel **Beheerde instanties** te selecteren.
3. Selecteer **Maken**.

   ![Een beheerd exemplaar maken](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Gebruik de tabbladen op het inrichtingsformulier **Beheerde instantie van Azure SQL Database Managed Instance** om vereiste en optionele informatie toe te voegen. In de volgende secties worden deze tabbladen beschreven.

### <a name="basics"></a>Basisbeginselen

- Vul de vereiste verplichte informatie in op het tabblad **Basisbeginselen.** Dit is een minimale set informatie die nodig is om een beheerde instantie in te dienen.

   ![Tabblad Basisbeginselen voor het maken van een beheerde instantie](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Gebruik de onderstaande tabel als referentie voor informatie die op dit tabblad vereist is.

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ------ | --------------- | ----------- |
   | **Abonnement** | Uw abonnement. | Een abonnement dat u toestemming geeft om nieuwe bronnen te maken. |
   | **Resourcegroep** | een nieuwe of bestaande resourcegroep.|Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige namen van resourcegroepen.|
   | **Naam van het beheerde exemplaar** | Elke geldige naam.|Zie [Naamregels en beperkingen voor](/azure/architecture/best-practices/resource-naming)geldige namen.|
   | **Regio** |Het gebied waarin u de beheerde instantie wilt maken.|Zie [Azure-regio's](https://azure.microsoft.com/regions/) voor informatie over regio's.|
   | **Beheerdersaanmeldgegevens voor het beheerde exemplaar** | Elke geldige gebruikersnaam. | Zie [Naamregels en beperkingen voor](/azure/architecture/best-practices/resource-naming)geldige namen. Gebruik geen "serveradmin", want dat is een gereserveerde rol op serverniveau.|
   | **Wachtwoord** | Elk geldig wachtwoord.| Het wachtwoord moet minstens 16 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Selecteer **Beheerde instantie configureren** om reken- en opslagbronnen te vergroten en de prijsniveaus te controleren. Gebruik de schuifregelaars of tekstvakken om de hoeveelheid opslagruimte en het aantal virtuele kernen op te geven. Wanneer u klaar bent, selecteert u **Toepassen** om uw selectie op te slaan. 

   ![Beheerd instantieformulier](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Als u uw keuzes wilt bekijken voordat u een beheerde instantie maakt, u **Controleren + maken selecteren.** Of configureer netwerkopties door **Volgende: Netwerken te**selecteren.

### <a name="networking"></a>Netwerken

- Vul optionele informatie in op het tabblad **Netwerken.** Als u deze informatie weglaat, past de portal standaardinstellingen toe.

   ![Tabblad 'Netwerken' voor het maken van een beheerde instantie](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Gebruik de onderstaande tabel als referentie voor informatie die op dit tabblad vereist is.

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ------ | --------------- | ----------- |
   | **Virtueel netwerk** | Selecteer **Nieuw virtueel netwerk maken** of een geldig virtueel netwerk en subnet.| Als een netwerk of subnet niet beschikbaar is, moet het worden [gewijzigd om te voldoen aan de netwerkvereisten](sql-database-managed-instance-configure-vnet-subnet.md) voordat u het selecteert als doel voor het nieuwe beheerde exemplaar. Zie [Een virtueel netwerk configureren voor een beheerde instantie voor](sql-database-managed-instance-connectivity-architecture.md)informatie over de vereisten voor het configureren van de netwerkomgeving voor een beheerde instantie. |
   | **Verbindingstype** | Kies tussen een proxy en een omleidingsverbindingstype.|Zie [Azure SQL Database-verbindingsbeleid](sql-database-connectivity-architecture.md#connection-policy)voor meer informatie over verbindingstypen van verbindingen .|
   | **Openbaar eindpunt**  | Selecteer **Inschakelen**. | Als u wilt dat een beheerde instantie toegankelijk is via het eindpunt voor openbare gegevens, moet u deze optie inschakelen. | 
   | **Toegang toestaan vanaf** (als **Openbaar eindpunt** is ingeschakeld) | Selecteer een van de opties.   |De portal-ervaring maakt het configureren van een beveiligingsgroep met een openbaar eindpunt mogelijk. </br> </br> Selecteer op basis van uw scenario een van de volgende opties: </br> <ul> <li>**Azure-services:** we raden deze optie aan wanneer u verbinding maakt vanuit Power BI of een andere multitenant-service. </li> <li> **Internet:** Gebruik voor testdoeleinden wanneer u snel een beheerde instantie wilt draaien. We raden het niet aan voor productieomgevingen. </li> <li> **Geen toegang:** met deze optie wordt een beveiligingsregel **weigeren.** Wijzig deze regel om een beheerde instantie toegankelijk te maken via een openbaar eindpunt. </li> </ul> </br> Zie [Een azure SQL Database-beheerde instantie veilig gebruiken met een openbaar eindpunt](sql-database-managed-instance-public-endpoint-securely.md)voor meer informatie over openbare endpoint-beveiliging.|

- Selecteer **Controleren + maken** om uw keuzes te bekijken voordat u een beheerde instantie maakt. Of configureer meer aangepaste instellingen door **Volgende: Aanvullende instellingen**te selecteren.

### <a name="additional-settings"></a>Meer instellingen

- Vul optionele informatie in op het tabblad **Extra instellingen.** Als u deze informatie weglaat, past de portal standaardinstellingen toe.

   ![Tabblad 'Extra instellingen' voor het maken van een beheerde instantie](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Gebruik de onderstaande tabel als referentie voor informatie die op dit tabblad vereist is.

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ------ | --------------- | ----------- |
   | **Sortering** | Kies de collatie die u wilt gebruiken voor uw beheerde instantie. Als u databases migreert vanuit SQL Server, `SELECT SERVERPROPERTY(N'Collation')` controleert u de bronverzameling met behulp van deze waarde en gebruikt u deze waarde.| Zie [De servercollatie instellen of wijzigen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation)voor informatie over collaties.|   
   | **Tijdzone** | Selecteer de tijdzone die uw beheerde instantie zal observeren.|Zie [Tijdzones voor](sql-database-managed-instance-timezone.md)meer informatie.|
   | **Gebruiken als failover secundair** | Selecteer **Ja**. | Schakel deze optie in om de beheerde instantie te gebruiken als een secundaire failovergroep.|
   | **Primaire beheerde instantie** (als **Gebruik als secundaire failover** is ingesteld op **Ja)** | Kies een bestaand primair beheerd exemplaar dat wordt samengevoegd in dezelfde DNS-zone met het beheerde exemplaar dat u maakt. | Met deze stap u de fail-over-groep na het maken van de status van failover mogelijk maken. Zie [Zelfstudie: een SQL Database-beheerde instantie toevoegen aan een failovergroep](sql-database-managed-instance-failover-group-tutorial.md)voor meer informatie.|

### <a name="review--create"></a>Controleren + maken

5. Selecteer **Controleren + tabblad maken** om uw keuzes te bekijken voordat u de beheerde instantie maakt.

   ![Tabblad voor het bekijken en maken van een beheerde instantie](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Selecteer **Maken** om te beginnen met het inrichten van de beheerde instantie.

> [!IMPORTANT]
> Het implementeren van een beheerde instantie is een langdurige bewerking. Implementatie van de eerste instantie in het subnet duurt doorgaans veel langer dan het implementeren in een subnet met bestaande beheerde exemplaren. Zie [Beheervan de beheerbewerkingen voor beheervan de instantie](sql-database-managed-instance.md#managed-instance-management-operations)voor gemiddelde indiktijden .

### <a name="monitor-deployment-progress"></a>Voortgang van de implementatie controleren

7. Selecteer het pictogram **Meldingen** om de status van de implementatie weer te geven.

   ![Implementatievoortgang van een beheerde instantieimplementatie](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Selecteer **Implementatie in uitvoering** in de melding om het beheerde instantievenster te openen en de voortgang van de implementatie verder te controleren. 

> [!TIP]
> Als u uw webbrowser hebt gesloten of bent verwijderd van het scherm implementatievoortgang, voert u de volgende stappen uit om het voortgangsscherm voor implementatie terug te zoeken:
> 1. Open in Azure-portal de brongroep (op het tabblad **Basisbeginselen)** waaraan u een beheerde instantie implementeert.
> 2. Selecteer **Implementaties**.
> 3. Selecteer de beheerde instantieimplementatiebewerking die wordt uitgevoerd.

> [!IMPORTANT]
> Als u de status van beheerde instantiecreatie wilt krijgen, moet u **leesmachtigingen** hebben voor de resourcegroep. Als u deze toestemming niet hebt of intrekt terwijl de beheerde instantie in het creatieproces is, kan dit ertoe leiden dat beheerde instantie niet zichtbaar is in de lijst met implementaties van resourcegroepen.
>

## <a name="post-deployment-operations"></a>Bewerkingen na implementatie

Als u de gemaakte resources wilt controleren, moet u de netwerkinstellingen verfijnen en de gegevens van de hostverbinding (FQDN) opvragen, worden de stappen die in deze sectie zijn beschreven, uitgevoerd.

### <a name="view-resources-created"></a>Resources weergeven die zijn gemaakt

Bij een succesvolle implementatie van beheerde instantie u resources bekijken die zijn gemaakt:

1. Open de resourcegroep voor uw beheerde instantie. Bekijk de resources die voor u zijn gemaakt in de snelstart [van een beheerde instantie](#create-a-managed-instance) maken.

   ![Resources voor beheerd exemplaar](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Netwerkinstellingen weergeven en verfijnen

Als u de netwerkinstellingen optioneel wilt verfijnen, controleert u het volgende:

1. Selecteer de routetabel om de door de gebruiker gedefinieerde route (UDR) te bekijken die voor u is gemaakt.

   ![Routetabel](./media/sql-database-managed-instance-get-started/route-table.png)

2. Bekijk in de routetabel de vermeldingen om verkeer van en binnen het virtuele netwerk van de beheerde instantie te routeren. Als u de routetabel handmatig maakt of configureert, moet u ervoor zorgen dat deze vermeldingen worden gemaakt in de beheerde instantieroutetabel.

   ![Vermelding voor een beheerd instantiesubnet naar lokaal](./media/sql-database-managed-instance-get-started/udr.png)

3. Ga terug naar de brongroep en selecteer de netwerkbeveiligingsgroep.

   ![Netwerkbeveiligingsgroep](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Bekijk de inkomende en uitgaande beveiligingsregels. 

   ![Beveiligingsregels](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Als u een openbaar eindpunt voor uw beheerde instantie hebt geconfigureerd, moet u poorten openen om netwerkverkeer toe te staan waarmee verbindingen kunnen worden beheerd vanaf het openbare internet, zie [Een openbaar eindpunt configureren voor beheerde instantie](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) voor meer informatie.
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Verbindingsgegevens ophalen naar beheerde instantie

Als u verbinding wilt maken met beheerde instantie, voert u deze stappen uit om de hostnaam en de volledig gekwalificeerde domeinnaam (FQDN) op te halen:

1. Ga terug naar de resourcegroep en selecteer uw beheerde exemplaar.

   ![Beheerde instantie in de resourcegroep](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. Zoek op het tabblad **Overzicht** de eigenschap **Host.** Kopieer de hostnaam voor het beheerde exemplaar voor gebruik in de volgende quickstart.

   ![Hostnaam](./media/sql-database-managed-instance-get-started/host-name.png)

   De gekopieerde waarde vertegenwoordigt een volledig gekwalificeerde domeinnaam (FQDN) die kan worden gebruikt om verbinding te maken met beheerde instantie. Het is vergelijkbaar met het volgende adresvoorbeeld: *your_host_name.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Volgende stappen

Ga als u meer te weten over het maken van verbinding met een beheerde instantie:
- Zie [Uw toepassingen verbinden met een beheerde instantie voor](sql-database-managed-instance-connect-app.md)een overzicht van de verbindingsopties voor toepassingen.
- Zie [Een azure-verbinding](sql-database-managed-instance-configure-vm.md)met een virtuele machine configureren voor een snelle start met een beheerde instantie.
- Zie [Een point-to-site-verbinding configureren](sql-database-managed-instance-configure-p2s.md)voor een snelle start die laat zien hoe u verbinding maakt met een beheerde instantie via een on-premises clientcomputer.

Ga als volgt te werk om een bestaande SQL Server-database te herstellen van on-premises naar een beheerde instantie: 
- Gebruik de [Azure Database Migration Service voor migratie](../dms/tutorial-sql-server-to-managed-instance.md) om te herstellen vanuit een databaseback-upbestand. 
- Gebruik de [opdracht T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) om te herstellen vanuit een databaseback-upbestand.

Zie Azure SQL Database controleren met Azure SQL [Analytics](../azure-monitor/insights/azure-sql.md)voor geavanceerde bewaking van beheerde instantiedatabaseprestaties met ingebouwde informatie over probleemoplossing.
