---
title: 'Azure Portal: Een beheerd exemplaar maken'
description: Maak een met SQL Database beheerd exemplaar, een netwerkomgeving en een client-VM om toegang te krijgen.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: e8a0b27f055f39186371e23e46c8b41679e05dea
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770105"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Quickstart: Een beheerd exemplaar van Azure SQL Database maken

In deze quickstart leert u hoe u een [beheerd exemplaar](sql-database-managed-instance.md) van Azure SQL Database maakt in de Azure-portal.

> [!IMPORTANT]
> Zie [Ondersteunde regio's](sql-database-managed-instance-resource-limits.md#supported-regions) en [Ondersteunde abonnementstypen](sql-database-managed-instance-resource-limits.md#supported-subscription-types) voor beperkingen.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/).

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Een beheerd exemplaar maken

In de volgende stappen wordt uitgelegd hoe u een beheerd exemplaar maakt:

1. Selecteer **Azure SQL** in het menu links van de Azure-portal. Als **Azure SQL** niet in de lijst staat, selecteert u **Alle services** en voert u vervolgens **Azure SQL** in het zoekvak in.
2. Selecteer **+Toevoegen** om de pagina **SQL-implementatieoptie selecteren** te openen. U kunt aanvullende informatie over een beheerd exemplaar van Azure SQL Database bekijken door **Details weergeven** te selecteren op de tegel **Beheerde exemplaren**.
3. Selecteer **Maken**.

   ![Een beheerd exemplaar maken](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Gebruik de tabbladen in het inrichtingsformulier **Een met Azure SQL Database beheerd exemplaar maken** om vereiste en optionele informatie toe te voegen. In de volgende secties worden deze tabbladen in meer detail beschreven.

### <a name="basics"></a>Basisbeginselen

- Vul verplichte gegevens in die vereist zijn op het tabblad **Basis**. Dit is een set informatie die minimaal vereist is voor het inrichten van een beheerd exemplaar.

   ![Tabblad 'Basis' voor het maken van een beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Gebruik de onderstaande tabel als referentie voor informatie die op dit tabblad is vereist.

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ------ | --------------- | ----------- |
   | **Abonnement** | Uw abonnement. | Een abonnement met toestemming voor het maken van nieuwe resources. |
   | **Resourcegroep** | een nieuwe of bestaande resourcegroep.|Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige resourcegroepnamen.|
   | **Naam van het beheerde exemplaar** | Een geldige naam.|Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige namen.|
   | **Regio** |De regio waarin u het beheerde exemplaar wilt maken.|Zie [Azure-regio's](https://azure.microsoft.com/regions/) voor informatie over regio's.|
   | **Beheerdersaanmeldgegevens voor het beheerde exemplaar** | Een geldige gebruikersnaam. | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige namen. Maak geen gebruik van 'serverbeheerder' aangezien dit een rol is die op serverniveau is gereserveerd.|
   | **Wachtwoord** | Een geldig wachtwoord.| Het wachtwoord moet minstens 16 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Selecteer **Beheerd exemplaar configureren** om de grootte van reken- en opslagresources te bepalen en de prijscategorieën te controleren. Gebruik de schuifregelaars of tekstvakken om de hoeveelheid opslagruimte en het aantal virtuele kernen op te geven. Wanneer u klaar bent, selecteert u **Toepassen** om uw selectie op te slaan. 

   ![Formulier voor beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Als u uw keuzes wilt bekijken voordat u een beheerd exemplaar maakt, kunt u **Bekijken en maken** selecteren. U kunt ook de netwerkopties configureren door  **te selecteren. We gaan nu verder met: Netwerken**.

### <a name="networking"></a>Netwerken

- Vul optionele informatie in op het tabblad **Netwerken**. Als u deze informatie weglaat, worden de standaardinstellingen toegepast door de portal.

   ![Tabblad 'Netwerken' voor het maken van een beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Gebruik de onderstaande tabel als referentie voor informatie die op dit tabblad is vereist.

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ------ | --------------- | ----------- |
   | **Virtueel netwerk** | Selecteer **Nieuw virtueel netwerk maken** of een geldig virtueel netwerk en subnet.| Als een netwerk/subnet niet beschikbaar is, moet het worden [gewijzigd om te voldoen aan de netwerkvereisten](sql-database-managed-instance-configure-vnet-subnet.md) voordat u het als doel voor het nieuwe beheerde exemplaar kunt selecteren. Zie [Een virtueel netwerk configureren voor een beheerd exemplaar](sql-database-managed-instance-connectivity-architecture.md) voor informatie over de vereisten voor het configureren van de netwerkomgeving voor een beheerd exemplaar. |
   | **Verbindingstype** | Kies tussen een proxy of omleiden als verbindingstype.|Zie [Verbindingsbeleid voor Azure SQL Database](sql-database-connectivity-architecture.md#connection-policy) voor meer informatie over verbindingstypen.|
   | **Openbaar eindpunt**  | Selecteer **Inschakelen**. | Als u wilt dat een beheerd exemplaar toegankelijk is via het eindpunt voor openbare gegevens, moet u deze optie inschakelen. | 
   | **Toegang toestaan vanaf** (als **Openbaar eindpunt** is ingeschakeld) | Selecteer een van de opties.   |Met de portalervaring kunt u een beveiligingsgroep configureren met een openbaar eindpunt. </br> </br> Selecteer één van de volgende opties op basis van uw scenario: </br> <ul> <li>**Azure-services**: We raden u aan deze optie te selecteren wanneer u verbinding maakt vanuit Power BI of een andere service voor meerdere tenants. </li> <li> **Internet**: Gebruik dit om te testen wanneer u snel een beheerd exemplaar wilt maken. Deze optie wordt niet aanbevolen in productieomgevingen. </li> <li> **Geen toegang**: Met deze optie maakt u de beveiligingsregel **Weigeren**. Wijzig deze regel om een beheerd exemplaar toegankelijk te maken via een openbaar eindpunt. </li> </ul> </br> Zie [Een met Azure SQL Database beheerd exemplaar veilig gebruiken met een openbaar eindpunt](sql-database-managed-instance-public-endpoint-securely.md) voor meer informatie over de beveiliging van het openbare eindpunt.|

- Als u uw keuzes wilt bekijken voordat u een beheerd exemplaar maakt, kunt u **Bekijken en maken** selecteren. U kunt ook meer aangepaste instellingen configureren door  **te selecteren. We gaan nu verder met: Aanvullende instellingen**.

### <a name="additional-settings"></a>Aanvullende instellingen

- Vul optionele informatie in op het tabblad **Aanvullende instellingen**. Als u deze informatie weglaat, worden de standaardinstellingen toegepast door de portal.

   ![Tabblad 'Aanvullende instellingen' voor het maken van een beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Gebruik de onderstaande tabel als referentie voor informatie die op dit tabblad is vereist.

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ------ | --------------- | ----------- |
   | **Sortering** | Kies de sortering die u wilt gebruiken voor uw beheerde exemplaar. Als u SQL Server-databases wilt migreren, moet u de bronsortering controleren met `SELECT SERVERPROPERTY(N'Collation')` en die waarde gebruiken.| Zie [De serversortering instellen of wijzigen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) voor informatie over sorteringen.|   
   | **Tijdzone** | Selecteer de tijdzone die het beheerde exemplaar moet gebruiken.|Zie [Tijdzones](sql-database-managed-instance-timezone.md) voor meer informatie.|
   | **Gebruiken als secundaire failover** | Selecteer **Ja**. | Schakel deze optie in als u het beheerde exemplaar wilt gebruiken als een secundaire failovergroep.|
   | **Primair beheerd exemplaar** (als **Gebruiken als secundaire failover** is ingesteld op **Ja**) | Kies een bestaand primair beheerd exemplaar dat wordt toegevoegd aan dezelfde DNS-zone met het beheerde exemplaar dat u nu maakt. | Met deze stap wordt de configuratie van de failovergroep na het maken ingeschakeld. Zie [Zelfstudie: Een met SQL Database beheerd exemplaar aan een failovergroep toevoegen](sql-database-managed-instance-failover-group-tutorial.md).|

### <a name="review--create"></a>Beoordelen en maken

5. Als u uw keuzes wilt bekijken voordat u een beheerd exemplaar maakt, kunt u het tabblad **Bekijken en maken** selecteren.

   ![Tabblad voor het bekijken en maken van een beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Selecteer **Maken** om het inrichten van het beheerde exemplaar te starten.

> [!IMPORTANT]
> Het implementeren van een beheerd exemplaar is een langdurende bewerking. De implementatie van het eerste exemplaar in het subnet duurt doorgaans veel langer dan de implementatie in een subnet met bestaande beheerde exemplaren. Zie [Beheerbewerkingen voor beheerde exemplaren](sql-database-managed-instance.md#managed-instance-management-operations) voor de gemiddelde inrichtingsduur.

### <a name="monitor-deployment-progress"></a>De voortgang van de implementatie controleren

7. Selecteer het pictogram voor **Meldingen** om de status van de implementatie te bekijken.

   ![De voortgang van de implementatie van een beheerd exemplaar](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Selecteer **Implementatie wordt uitgevoerd** in de melding om het venster Beheerd exemplaar te openen. Hier kunt u details van de voortgang van de implementatie controleren. 

> [!TIP]
> Als u de webbrowser hebt gesloten of als u het scherm voor de voortgang van de implementatie hebt verplaatst, voert u de volgende stappen uit om terug te gaan naar het scherm voor de voortgang van de implementatie:
> 1. Open in de Azure-portal de resourcegroep (op het tabblad **Basis**) waarnaar u een beheerd exemplaar implementeert.
> 2. Selecteer **Implementaties**.
> 3. Selecteer de implementatiebewerking voor het beheerde exemplaar die wordt uitgevoerd.

> [!IMPORTANT]
> Als u de status van het maken van beheerde exemplaren wilt ophalen, moet u **leesrechten hebben** voor de resourcegroep. Als u deze rechten niet hebt of intrekt terwijl het beheerde exemplaar wordt gemaakt, kan dit ertoe leiden dat het beheerde exemplaar niet zichtbaar is in de lijst met implementaties van resourcegroepen.
>

## <a name="post-deployment-operations"></a>Bewerkingen na de implementatie

Volg de stappen die worden beschreven in deze sectie om de resources te bekijken die zijn gemaakt, de netwerkinstellingen te verfijnen en de gegevens van de hostverbinding op te halen.

### <a name="view-resources-created"></a>Gemaakte resources weergeven

Na een geslaagde implementatie van het beheerde exemplaar kunt u de resources die zijn gemaakt, als volgt bekijken:

1. Open de resourcegroep voor uw beheerde exemplaar. Bekijk de resources die voor u zijn gemaakt in de quickstart [Een beheerd exemplaar maken](#create-a-managed-instance).

   ![Resources voor beheerd exemplaar](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Netwerkinstellingen weergeven en aanpassen

Als u de netwerkinstellingen wilt aanpassen, bekijkt u het volgende:

1. Selecteer de routetabel om de door de gebruiker gedefinieerde route op te geven die voor u is gemaakt.

   ![Routetabel](./media/sql-database-managed-instance-get-started/route-table.png)

2. Bekijk de vermeldingen in de routetabel om verkeer vanaf en binnen het virtuele netwerk van het beheerde exemplaar door te sturen. Als u de routetabel handmatig maakt of configureert, moet u deze vermeldingen in de routetabel van het beheerde exemplaar maken.

   ![Vermelding voor een subnet van een beheerd exemplaar naar lokaal](./media/sql-database-managed-instance-get-started/udr.png)

3. Ga terug naar de resourcegroep en selecteer de netwerkbeveiligingsgroep.

   ![Netwerkbeveiligingsgroep](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Bekijk de inkomende en uitgaande beveiligingsregels. 

   ![Beveiligingsregels](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Als u het openbare eindpunt voor uw beheerde exemplaar hebt geconfigureerd, moet u poorten openen voor netwerkverkeer dat verbindingen met een beheerd exemplaar toestaat via het openbare internet. Zie [Een openbaar eindpunt configureren voor een beheerd exemplaar](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) voor meer informatie.
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Verbindingsdetails ophalen voor een beheerd exemplaar

Als u verbinding wilt maken met een beheerd exemplaar, voert u de volgende stappen uit om de hostnaam en de FQDN (Fully Qualified Domain Name) op te halen:

1. Ga terug naar de resourcegroep en selecteer uw beheerde exemplaar.

   ![Beheerd exemplaar in de resourcegroep](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. Ga naar het tabblad **Overzicht** en zoek de eigenschap **Host**. Kopieer de hostnaam voor het beheerde exemplaar voor gebruik in de volgende quickstart.

   ![Hostnaam](./media/sql-database-managed-instance-get-started/host-name.png)

   De gekopieerde waarde vertegenwoordigt een Fully Qualified Domain Name (FQDN) die kan worden gebruikt om verbinding te maken met een beheerd exemplaar. Deze is vergelijkbaar met het volgende voorbeeld: *uw_hostnaam.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over het maken van een verbinding met een beheerd exemplaar:
- Zie [Verbinding maken tussen uw toepassing en het beheerde exemplaar van Azure SQL Database](sql-database-managed-instance-connect-app.md) voor een overzicht van de verbindingsopties voor toepassingen.
- Zie [Een verbinding vanaf een virtuele Azure-machine configureren](sql-database-managed-instance-configure-vm.md) voor een quickstart over het verbinding maken met een beheerd exemplaar vanaf een virtuele Azure-machine.
- Zie [Een punt-naar-site-verbinding configureren](sql-database-managed-instance-configure-p2s.md) voor een quickstart over verbinding maken met een beheerd exemplaar vanaf een on-premises clientcomputer met behulp van een punt-naar-site-verbinding.

Als u een bestaande SQL Server-database van on-premises wilt herstellen naar een beheerd exemplaar: 
- Gebruik de [Azure Database Migration Service voor migratie](../dms/tutorial-sql-server-to-managed-instance.md) om te herstellen vanuit een back-upbestand van de database. 
- Gebruik [de opdracht T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) om te herstellen vanuit een back-upbestand van een database.

Zie [Azure SQL Database bewaken met Azure SQL-analyse](../azure-monitor/insights/azure-sql.md) voor geavanceerde bewaking van de prestaties van de beheerde exemplaardatabase met ingebouwde intelligentie voor het oplossen van problemen.
