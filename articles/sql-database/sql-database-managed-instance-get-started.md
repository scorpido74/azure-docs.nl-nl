---
title: 'Azure Portal: een beheerd exemplaar maken'
description: Maak een SQL Database beheerde instantie, netwerk omgeving en VM-client voor toegang.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80257611"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Snelstartgids: een door Azure SQL Database beheerd exemplaar maken

In deze Quick Start leert u hoe u een Azure SQL Database [beheerd exemplaar](sql-database-managed-instance.md) maakt in azure Portal.

> [!IMPORTANT]
> Zie [ondersteunde regio's](sql-database-managed-instance-resource-limits.md#supported-regions) en [ondersteunde abonnements typen](sql-database-managed-instance-resource-limits.md#supported-subscription-types)voor beperkingen.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/).

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Een beheerd exemplaar maken

De volgende stappen laten zien hoe u een beheerd exemplaar maakt:

1. Selecteer **Azure SQL** in het menu links van Azure Portal. Als **Azure SQL** niet voor komt in de lijst, selecteert u **alle services**en voert u vervolgens **Azure SQL** in het zoekvak in.
2. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie over een beheerde instantie van een Azure SQL Database bekijken door **Details weer geven** op de tegel **Managed instances** te selecteren.
3. Selecteer **Maken**.

   ![Een beheerd exemplaar maken](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Gebruik de tabbladen in het formulier voor het inrichten van **Azure SQL database beheerde instantie maken** om vereiste en optionele informatie toe te voegen. Deze tabbladen worden beschreven in de volgende secties.

### <a name="basics"></a>Basisbeginselen

- Vul verplichte vereiste gegevens in op het tabblad **basis beginselen** . Dit is een minimale set informatie die is vereist voor het inrichten van een beheerd exemplaar.

   ![Tabblad ' basis principes ' voor het maken van een beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Gebruik de onderstaande tabel als referentie voor informatie die op dit tabblad is vereist.

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ------ | --------------- | ----------- |
   | **Abonnement** | Uw abonnement. | Een abonnement dat u toestemming geeft om nieuwe resources te maken. |
   | **Resourcegroep** | een nieuwe of bestaande resourcegroep.|Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige namen van resourcegroepen.|
   | **Naam van het beheerde exemplaar** | Een geldige naam.|Zie [naamgevings regels en beperkingen](/azure/architecture/best-practices/resource-naming)voor geldige namen.|
   | **Regio** |De regio waarin u het beheerde exemplaar wilt maken.|Zie [Azure-regio's](https://azure.microsoft.com/regions/) voor informatie over regio's.|
   | **Beheerdersaanmeldgegevens voor het beheerde exemplaar** | Een geldige gebruikers naam. | Zie [naamgevings regels en beperkingen](/azure/architecture/best-practices/resource-naming)voor geldige namen. Gebruik niet ' ServerAdmin ' omdat dat een gereserveerde rol op server niveau is.|
   | **Wachtwoord** | Een geldig wacht woord.| Het wachtwoord moet minstens 16 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Selecteer **beheerd exemplaar configureren** voor grootte van reken-en opslag resources en om de prijs categorieën te controleren. Gebruik de schuifregelaars of tekstvakken om de hoeveelheid opslagruimte en het aantal virtuele kernen op te geven. Wanneer u klaar bent, selecteert u **Toep assen** om uw selectie op te slaan. 

   ![Formulier beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Als u uw keuzes wilt bekijken voordat u een beheerd exemplaar maakt, kunt u **bekijken en maken**selecteren. Of configureer netwerk opties door **volgende: netwerken**te selecteren.

### <a name="networking"></a>Netwerken

- Vul optionele informatie in op het tabblad **netwerk** . Als u deze informatie weglaat, worden de standaard instellingen toegepast op de portal.

   ![Tabblad netwerken voor het maken van een beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Gebruik de onderstaande tabel als referentie voor informatie die op dit tabblad is vereist.

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ------ | --------------- | ----------- |
   | **Virtueel netwerk** | Selecteer **Nieuw virtueel netwerk maken** of een geldig virtueel netwerk en subnet.| Als een netwerk of subnet niet beschikbaar is, moet dit worden [gewijzigd om aan de netwerk vereisten te voldoen](sql-database-managed-instance-configure-vnet-subnet.md) voordat u het selecteert als doel voor het nieuwe beheerde exemplaar. Zie [een virtueel netwerk configureren voor een beheerd exemplaar](sql-database-managed-instance-connectivity-architecture.md)voor informatie over de vereisten voor het configureren van de netwerk omgeving voor een beheerd exemplaar. |
   | **Verbindings type** | Kies tussen een proxy en een verbindings type omleiden.|Zie [Azure SQL database verbindings beleid](sql-database-connectivity-architecture.md#connection-policy)voor meer informatie over verbindings typen.|
   | **Openbaar eindpunt**  | Selecteer **Inschakelen**. | Als u wilt dat een beheerd exemplaar toegankelijk is via het eind punt voor open bare gegevens, moet u deze optie inschakelen. | 
   | **Toegang toestaan vanaf** (als het **open bare eind punt** is ingeschakeld) | Selecteer een van de opties.   |Met de portal-ervaring kunt u een beveiligings groep configureren met een openbaar eind punt. </br> </br> Selecteer op basis van uw scenario een van de volgende opties: </br> <ul> <li>**Azure-Services**: we raden u aan deze optie te selecteren wanneer u verbinding maakt vanuit Power bi of een andere multi tenant-service. </li> <li> **Internet**: gebruik voor test doeleinden wanneer u snel een beheerd exemplaar wilt maken. Het wordt niet aanbevolen voor productie omgevingen. </li> <li> **Geen toegang**: met deze optie wordt een regel voor het **weigeren** van een beveiliging gemaakt. Wijzig deze regel om een beheerd exemplaar toegankelijk te maken via een openbaar eind punt. </li> </ul> </br> Zie voor meer informatie over de beveiliging van open bare endpoints [een Azure SQL database beheerde instantie veilig met een openbaar eind punt gebruiken](sql-database-managed-instance-public-endpoint-securely.md).|

- Selecteer **controleren + maken** om uw keuzes te bekijken voordat u een beheerd exemplaar maakt. Of configureer meer aangepaste instellingen door **volgende: extra instellingen**te selecteren.

### <a name="additional-settings"></a>Meer instellingen

- Vul optionele informatie in op het tabblad **extra instellingen** . Als u deze informatie weglaat, worden de standaard instellingen toegepast op de portal.

   ![Tabblad aanvullende instellingen voor het maken van een beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Gebruik de onderstaande tabel als referentie voor informatie die op dit tabblad is vereist.

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ------ | --------------- | ----------- |
   | **Sortering** | Kies de sortering die u wilt gebruiken voor uw beheerde exemplaar. Als u data bases migreert vanaf SQL Server, controleert u de bron `SELECT SERVERPROPERTY(N'Collation')` sortering door te gebruiken en deze waarde te gebruiken.| Zie [de server sortering instellen of wijzigen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation)voor meer informatie over sorteringen.|   
   | **Tijdzone** | Selecteer de tijd zone die het beheerde exemplaar moet zien.|Zie [tijd zones](sql-database-managed-instance-timezone.md)voor meer informatie.|
   | **Gebruiken als secundaire failover** | Selecteer **Ja**. | Schakel deze optie in als u het beheerde exemplaar wilt gebruiken als een failovergroep secundair.|
   | **Primair beheerd exemplaar** (als **secundair gebruiken als secundaire failover** is ingesteld op **Ja**) | Kies een bestaand primair beheerd exemplaar dat wordt toegevoegd aan dezelfde DNS-zone met het beheerde exemplaar dat u maakt. | Met deze stap wordt de configuratie van de failovergroep na het maken ingeschakeld. Zie [zelf studie: een door SQL database beheerd exemplaar toevoegen aan een failovergroep](sql-database-managed-instance-failover-group-tutorial.md)voor meer informatie.|

### <a name="review--create"></a>Controleren en maken

5. Selecteer **controleren + tabblad maken** om uw keuzes te bekijken voordat u het beheerde exemplaar maakt.

   ![Tabblad voor het controleren en maken van een beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Selecteer **maken** om te beginnen met het inrichten van het beheerde exemplaar.

> [!IMPORTANT]
> Het implementeren van een beheerd exemplaar is een langlopende bewerking. De implementatie van de eerste instantie in het subnet duurt doorgaans veel langer dan implementeren in een subnet met bestaande beheerde exemplaren. Zie [Managed instance Management Operations](sql-database-managed-instance.md#managed-instance-management-operations)(Engelstalig) voor gemiddelde inrichtings tijden.

### <a name="monitor-deployment-progress"></a>Voortgang van de implementatie bewaken

7. Selecteer het pictogram **meldingen** om de status van de implementatie weer te geven.

   ![Voortgang van de implementatie van de implementatie van een beheerd exemplaar](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Selecteer de implementatie die wordt **uitgevoerd** in de melding om het venster beheerd exemplaar te openen en de voortgang van de implementatie verder te bewaken. 

> [!TIP]
> Als u de webbrowser hebt gesloten of als u het scherm voor de voortgang van de implementatie hebt verplaatst, voert u de volgende stappen uit om terug te gaan naar het scherm voor de voortgang van de implementatie:
> 1. Open in Azure Portal de resource groep (op het tabblad **basis beginselen** ) waarnaar u een beheerd exemplaar implementeert.
> 2. Selecteer **implementaties**.
> 3. Selecteer de implementatie bewerking voor het beheerde exemplaar wordt uitgevoerd.

> [!IMPORTANT]
> Als u de status van het maken van beheerde instanties wilt ophalen, moet u **Lees machtigingen** hebben voor de resource groep. Als u deze machtiging niet hebt of intrekt terwijl het beheerde exemplaar wordt gemaakt, kan dit ertoe leiden dat het beheerde exemplaar niet zichtbaar is in de lijst met implementaties van resource groepen.
>

## <a name="post-deployment-operations"></a>Bewerkingen na de implementatie

Volg de stappen die worden beschreven in deze sectie om de resources te bekijken die zijn gemaakt, de netwerk instellingen te verfijnen en de gegevens van de host-verbinding op te halen (FQDN).

### <a name="view-resources-created"></a>Gemaakte resources weer geven

Na een geslaagde implementatie van het beheerde exemplaar om de resources weer te geven die zijn gemaakt:

1. Open de resource groep voor uw beheerde exemplaar. Bekijk de resources die voor u zijn gemaakt in de Snelstartgids [een beheerd exemplaar maken](#create-a-managed-instance) .

   ![Resources voor beheerd exemplaar](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Netwerk instellingen weer geven en aanpassen

Als u de netwerk instellingen optioneel wilt aanpassen, inspecteert u het volgende:

1. Selecteer de route tabel om de door de gebruiker gedefinieerde route (UDR) die voor u is gemaakt, te controleren.

   ![Routetabel](./media/sql-database-managed-instance-get-started/route-table.png)

2. Controleer in de route tabel de vermeldingen voor het routeren van verkeer van en binnen het virtuele netwerk van het beheerde exemplaar. Als u de route tabel hand matig maakt of configureert, moet u ervoor zorgen dat u deze vermeldingen maakt in de route tabel van het beheerde exemplaar.

   ![Vermelding voor een subnet van een beheerd exemplaar naar lokaal](./media/sql-database-managed-instance-get-started/udr.png)

3. Ga terug naar de resource groep en selecteer de netwerk beveiligings groep.

   ![Netwerkbeveiligingsgroep](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Bekijk de inkomende en uitgaande beveiligingsregels. 

   ![Beveiligingsregels](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Als u het open bare eind punt voor uw beheerde exemplaar hebt geconfigureerd, moet u poorten openen om netwerk verkeer toe te staan dat verbindingen met een beheerd exemplaar toestaat via het open bare Internet. Zie [een openbaar eind punt voor een beheerd exemplaar configureren](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) voor meer informatie.
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Verbindings Details ophalen voor een beheerd exemplaar

Als u verbinding wilt maken met een beheerd exemplaar, voert u de volgende stappen uit om de hostnaam en het Fully Qualified Domain Name op te halen (FQDN-naam):

1. Ga terug naar de resourcegroep en selecteer uw beheerde exemplaar.

   ![Beheerd exemplaar in de resource groep](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. Ga naar het tabblad **overzicht** en zoek de eigenschap **host** op. Kopieer de hostnaam voor het beheerde exemplaar voor gebruik in de volgende Snelstartgids.

   ![Hostnaam](./media/sql-database-managed-instance-get-started/host-name.png)

   De gekopieerde waarde vertegenwoordigt een Fully Qualified Domain Name (FQDN) die kan worden gebruikt om verbinding te maken met een beheerd exemplaar. Dit is vergelijkbaar met het volgende adres voorbeeld: *your_host_name. a1b2c3d4e5f6. data base. Windows. net*.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe u verbinding maakt met een beheerd exemplaar:
- Zie [uw toepassingen koppelen aan een beheerd exemplaar](sql-database-managed-instance-connect-app.md)voor een overzicht van de verbindings opties voor toepassingen.
- Zie [een Azure virtual machine-verbinding configureren](sql-database-managed-instance-configure-vm.md)voor een Snelstartgids die laat zien hoe u verbinding maakt met een beheerd exemplaar vanuit een virtuele Azure-machine.
- Zie [een punt-naar-site-verbinding configureren](sql-database-managed-instance-configure-p2s.md)voor een Snelstartgids die laat zien hoe u verbinding maakt met een beheerd exemplaar van een on-premises client computer met behulp van een punt-naar-site-verbinding.

Een bestaande SQL Server-Data Base van on-premises herstellen naar een beheerd exemplaar: 
- Gebruik de [Azure database Migration service voor migratie](../dms/tutorial-sql-server-to-managed-instance.md) om te herstellen vanuit een back-upbestand van de data base. 
- Gebruik de [T-SQL-opdracht herstellen](sql-database-managed-instance-get-started-restore.md) om een back-upbestand van een Data Base te herstellen.

Zie [Azure SQL database bewaken met behulp van Azure SQL-analyse](../azure-monitor/insights/azure-sql.md)voor geavanceerde bewaking van beheerde exemplaar database prestaties met ingebouwde probleemoplossings informatie.
