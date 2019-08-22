---
title: 'Azure Portal: Een door SQL Database beheerd exemplaar maken | Microsoft Docs'
description: Maak een SQL Database beheerde instantie, netwerk omgeving en VM-client voor toegang.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: cacd67e26b13df8ef456ac8f1391e4396f5bdd96
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873864"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Quickstart: Een beheerd exemplaar van Azure SQL Database maken

In deze Snelstartgids leert u hoe u een Azure SQL Database [beheerd exemplaar](sql-database-managed-instance.md) maakt in de Azure Portal.

> [!IMPORTANT]
> Zie [ondersteunde regio's](sql-database-managed-instance-resource-limits.md#supported-regions) en [ondersteunde abonnements typen](sql-database-managed-instance-resource-limits.md#supported-subscription-types)voor beperkingen.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/).

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Een beheerd exemplaar maken

In de volgende stappen wordt uitgelegd hoe u een beheerd exemplaar maakt.

1. Selecteer **Azure SQL** in het linkermenu van de Azure Portal. Als Azure SQL niet voor komt in de lijst, selecteert u **alle services**en typt u vervolgens *Azure SQL* in het zoekvak.
2. Selecteer **+ toevoegen** om de **optie pagina SQL-implementatie selecteren** te openen. U kunt aanvullende informatie over Azure SQL Database beheerde instantie bekijken door **Details weer geven** op de tegel **Managed instances** te selecteren.
3. Selecteer **maken**:

   ![Een beheerd exemplaar maken](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Vul het formulier **SQL Managed instance** in met behulp van de informatie in de volgende tabel.

   | Instelling| Voorgestelde waarde | Description |
   | ------ | --------------- | ----------- |
   | **Abonnement** | Uw abonnement. | Een abonnement dat u toestemming geeft om nieuwe resources te maken. |
   |**Naam van het beheerde exemplaar**|Een geldige naam.|Zie [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Naamgevingsconventies) voor geldige namen.|
   |**Beheerdersaanmeldgegevens voor het beheerde exemplaar**|Een geldige gebruikers naam.|Zie [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Naamgevingsconventies) voor geldige namen. Gebruik niet ' ServerAdmin ' omdat dat een gereserveerde rol op server niveau is.|
   |**Wachtwoord**|Een geldig wacht woord.|Het wachtwoord moet minstens 16 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Tijdzone**|De tijd zone die moet worden waargenomen door uw beheerde exemplaar.|Zie [tijd zones](sql-database-managed-instance-timezone.md)voor meer informatie.|
   |**Sortering**|De sortering die u wilt gebruiken voor uw beheerde exemplaar.|Als u data bases migreert vanaf SQL Server, controleert u de bron `SELECT SERVERPROPERTY(N'Collation')` sortering door te gebruiken en deze waarde te gebruiken. Zie [de server sortering instellen of wijzigen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation)voor meer informatie over sorteringen.|
   |**Location**|De locatie waar u het beheerde exemplaar wilt maken.|Zie [Azure-regio's](https://azure.microsoft.com/regions/) voor informatie over regio's.|
   |**Virtueel netwerk**|Selecteer **Nieuw virtueel netwerk maken** of een geldig virtueel netwerk en subnet.| Als een netwerk of subnet niet beschikbaar is, moet dit worden [gewijzigd om aan de netwerk vereisten te voldoen](sql-database-managed-instance-configure-vnet-subnet.md) voordat u het selecteert als doel voor het nieuwe beheerde exemplaar. Zie [een virtueel netwerk configureren voor een beheerd exemplaar](sql-database-managed-instance-connectivity-architecture.md)voor informatie over de vereisten voor het configureren van de netwerk omgeving voor een beheerd exemplaar. |
   |**Openbaar eind punt inschakelen**   |Schakel deze optie in om het open bare eind punt in te scha kelen   |Als u wilt dat beheerde exemplaren toegankelijk zijn via het open bare gegevens eind punt, moet u het **open bare eind punt inschakelen** .| 
   |**Toegang toestaan vanaf**   |Selecteer een van de volgende opties: <ul> <li>**Azure-Services**</li> <li>**Internet**</li> <li>**Geen toegang**</li></ul>   |Met portal-ervaring kunt u de beveiligings groep configureren met een openbaar eind punt. </br> </br> Selecteer op basis van uw scenario een van de volgende opties: </br> <ul> <li>Azure-Services: aanbevolen bij het maken van verbinding vanuit Power BI of een andere multi tenant-service. </li> <li> Internet-gebruik voor test doeleinden wanneer u snel een beheerd exemplaar wilt maken. Het wordt niet aanbevolen voor gebruik in productie omgevingen. </li> <li> Geen toegang: met deze optie maakt u een regel voor het weigeren van een beveiliging. U moet deze regel wijzigen om het beheerde exemplaar toegankelijk te maken via een openbaar eind punt. </li> </ul> </br> Zie voor meer informatie over open bare eindpunt beveiliging het [gebruik van Azure SQL database Managed instance veilig met een openbaar eind punt](sql-database-managed-instance-public-endpoint-securely.md).|
   |**Verbindings type**|Kies tussen een proxy en een verbindings type omleiden.|Zie [Azure SQL database verbindings beleid](sql-database-connectivity-architecture.md#connection-policy)voor meer informatie over verbindings typen.|
   |**Resourcegroep**|een nieuwe of bestaande resourcegroep.|Zie [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Naamgevingsconventies) voor geldige resourcegroepnamen.|

   ![Formulier beheerd exemplaar](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Voor gebruik van het beheerde exemplaar als secundaire exemplaarfailovergroep schakelt u het selectievakje in en geeft u het beheerde exemplaar DnsAzurePartner op. Deze functie is beschikbaar als preview-versie en wordt niet weer gegeven in de volgende scherm afbeelding.
6. Selecteer de **prijs categorie** voor de grootte van reken-en opslag resources en om de opties voor de prijs categorie te bekijken. De prijscategorie voor algemeen gebruik met 32 GB geheugen en 16 vCores is de standaardwaarde.
7. Gebruik de schuifregelaars of tekstvakken om de hoeveelheid opslagruimte en het aantal virtuele kernen op te geven.
8. Wanneer u klaar bent, selecteert u **Toep assen** om uw selectie op te slaan. 
9. Selecteer **Maken** om het beheerde exemplaar te implementeren.
10. Selecteer het pictogram **meldingen** om de status van de implementatie weer te geven.

    ![Voortgang van implementatie van beheerd exemplaar](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. Selecteer **Implementatie wordt uitgevoerd** om het venster Beheerd exemplaar te openen. Hier kunt u details van de voortgang van de implementatie controleren. 

> [!IMPORTANT]
> Het eerste exemplaar in een subnet kent doorgaans een veel langere implementatietijd dan volgende exemplaren. Annuleer de implementatiebewerking niet omdat deze langer duurt dan verwacht.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Resources controleren en de volledig gekwalificeerde servernaam ophalen

Nadat de implementatie is voltooid, controleert u de resources die zijn gemaakt en haalt u de volledig gekwalificeerde server naam op voor gebruik in latere Snelstartgids.

1. Open de resource groep voor uw beheerde exemplaar. Bekijk de resources die voor u zijn gemaakt in de Snelstartgids [een beheerd exemplaar maken](#create-a-managed-instance) .

   ![Resources van beheerd exemplaar](./media/sql-database-managed-instance-get-started/resources.png)

2. Selecteer de route tabel om de door de gebruiker gedefinieerde route tabel (UDR) die voor u is gemaakt, te controleren.

   ![Routetabel](./media/sql-database-managed-instance-get-started/route-table.png)

3. Bekijk de vermeldingen in de routetabel om verkeer vanaf en binnen het virtuele netwerk van het beheerde exemplaar door te sturen. Als u de route tabel hand matig maakt of configureert, moet u ervoor zorgen dat u deze vermeldingen in de route tabel maakt.

   ![Vermelding voor een subnet van een beheerd exemplaar naar lokaal](./media/sql-database-managed-instance-get-started/udr.png)

4. Ga terug naar de resource groep en selecteer de netwerk beveiligings groep om de beveiligings regels te controleren.

   ![Netwerkbeveiligingsgroep](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. Bekijk de inkomende en uitgaande beveiligingsregels. Als u open bare eind punten voor uw beheerde exemplaar hebt geconfigureerd, raadpleegt u het artikel [open bare eind punt configureren](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) voor meer informatie.

   ![Beveiligingsregels](./media/sql-database-managed-instance-get-started/security-rules.png)

6. Ga terug naar de resource groep en selecteer uw beheerde exemplaar.

   ![Beheerd exemplaar](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. Ga naar het tabblad **overzicht** en zoek de eigenschap **host** op. Kopieer het Fully Qualified host-adres voor het beheerde exemplaar voor gebruik in de volgende Snelstartgids.

   ![Hostnaam](./media/sql-database-managed-instance-get-started/host-name.png)

   De naam ziet er als volgt uit: **naam_van_uw_machine.a1b2c3d4e5f6.database.windows.net**.

## <a name="next-steps"></a>Volgende stappen

- Voor meer informatie over hoe u verbinding maakt met een beheerd exemplaar:
  - Zie [uw toepassingen koppelen aan een beheerd exemplaar](sql-database-managed-instance-connect-app.md)voor een overzicht van de verbindings opties voor toepassingen.
  - Zie [een Azure virtual machine-verbinding configureren](sql-database-managed-instance-configure-vm.md)voor een Snelstartgids die laat zien hoe u verbinding maakt met een beheerd exemplaar vanuit een virtuele Azure-machine.
  - Zie [een punt-naar-site-verbinding configureren](sql-database-managed-instance-configure-p2s.md)voor een Snelstartgids die laat zien hoe u verbinding maakt met een beheerd exemplaar van een on-premises client computer met behulp van een punt-naar-site-verbinding.
- Een bestaande SQL Server-Data Base van on-premises herstellen naar een beheerd exemplaar: 
    - Gebruik de [Azure database Migration service (DMS) voor migratie](../dms/tutorial-sql-server-to-managed-instance.md) om te herstellen vanuit een back-upbestand van de data base. 
    - Gebruik de [T-SQL-opdracht herstellen](sql-database-managed-instance-get-started-restore.md) om een back-upbestand van een Data Base te herstellen.
- Zie [Azure SQL database bewaken](../azure-monitor/insights/azure-sql.md)met behulp van Azure SQL-analyse voor geavanceerde bewaking van beheerde exemplaar database prestaties met ingebouwde probleemoplossings informatie.
