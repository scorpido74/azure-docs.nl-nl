---
title: 'Azure Portal: Een door SQL Database beheerd exemplaar maken | Microsoft Docs'
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
ms.date: 05/07/2019
ms.openlocfilehash: 5f37ca8e22b07e39eda87e11f52358e1d1497c60
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178511"
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

4. Vul het inrichtings formulier **Create SQL database Managed instance** in door de vereiste informatie op het tabblad basis in te vullen. Gebruik netwerken en geavanceerde instellingen voor het configureren van aanvullende instellingen.

### <a name="basic-tab"></a>Tabblad basis

Vul de vereiste gegevens op het tabblad **basis** in met behulp van de onderstaande tabel. Dit is een minimale set informatie voor het inrichten van een beheerd exemplaar.

   ![Tabblad voor het maken van een beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   | Instelling| Voorgestelde waarde | Description |
   | ------ | --------------- | ----------- |
   | **Abonnement** | Uw abonnement. | Een abonnement dat u toestemming geeft om nieuwe resources te maken. |
   | **Resourcegroep** | een nieuwe of bestaande resourcegroep.|Zie [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Naamgevingsconventies) voor geldige resourcegroepnamen.|
   | **Naam van het beheerde exemplaar** | Een geldige naam.|Zie [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Naamgevingsconventies) voor geldige namen.|
   | **Regio** |De regio waarin u het beheerde exemplaar wilt maken.|Zie [Azure-regio's](https://azure.microsoft.com/regions/) voor informatie over regio's.|
| **Beheerdersaanmeldgegevens voor het beheerde exemplaar** | Een geldige gebruikers naam. | Zie [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Naamgevingsconventies) voor geldige namen. Gebruik niet ' ServerAdmin ' omdat dat een gereserveerde rol op server niveau is.|
   | **Wachtwoord** | Een geldig wacht woord.| Het wachtwoord moet minstens 16 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Selecteer **beheerd exemplaar configureren** om de grootte van reken-en opslag resources te bepalen en de opties voor de prijs categorie te bekijken.
- Gebruik de schuifregelaars of tekstvakken om de hoeveelheid opslagruimte en het aantal virtuele kernen op te geven.
- Wanneer u klaar bent, selecteert u **Toep assen** om uw selectie op te slaan. 

  ![Formulier beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

U kunt op **controleren + maken** klikken om de geselecteerde opties te bekijken voordat u een beheerd exemplaar maakt, of u kunt aangepaste netwerk **configuratie configureren door op volgende te klikken: Netwerken**.

### <a name="networking-tab"></a>Tabblad netwerken

Vul optionele gegevens in op het tabblad netwerken met behulp van de onderstaande tabel. Als deze informatie wordt wegge laten, worden de standaard instellingen toegepast.

   ![Tabblad voor het maken van een beheerd exemplaar van het netwerk](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   | Instelling| Voorgestelde waarde | Description |
   | ------ | --------------- | ----------- |
   | **Virtueel netwerk** | Selecteer **Nieuw virtueel netwerk maken** of een geldig virtueel netwerk en subnet.| Als een netwerk of subnet niet beschikbaar is, moet dit worden [gewijzigd om aan de netwerk vereisten te voldoen](sql-database-managed-instance-configure-vnet-subnet.md) voordat u het selecteert als doel voor het nieuwe beheerde exemplaar. Zie [een virtueel netwerk configureren voor een beheerd exemplaar](sql-database-managed-instance-connectivity-architecture.md)voor informatie over de vereisten voor het configureren van de netwerk omgeving voor een beheerd exemplaar. |
   | **Verbindings type** | Kies tussen een proxy en een verbindings type omleiden.|Zie [Azure SQL database verbindings beleid](sql-database-connectivity-architecture.md#connection-policy)voor meer informatie over verbindings typen.|
   | **Openbaar eindpunt**  | Selecteer deze optie om het open bare eind punt in te scha kelen | Als u wilt dat beheerde exemplaren toegankelijk zijn via het open bare gegevens eind punt, moet u openbaar eind punt **inschakelen** selecteren. | 
   | **Toegang toestaan vanaf** (als het open bare eind punt is ingesteld op ingeschakeld) | Selecteer een van de volgende opties: <ul> <li>**Azure-Services**</li> <li>**Internet**</li> <li>**Geen toegang**</li></ul>   |Met portal-ervaring kunt u de beveiligings groep configureren met een openbaar eind punt. </br> </br> Selecteer op basis van uw scenario een van de volgende opties: </br> <ul> <li>Azure-Services: aanbevolen bij het maken van verbinding vanuit Power BI of een andere multi tenant-service. </li> <li> Internet-gebruik voor test doeleinden wanneer u snel een beheerd exemplaar wilt maken. Het wordt niet aanbevolen voor gebruik in productie omgevingen. </li> <li> Geen toegang: met deze optie maakt u een regel voor het weigeren van een beveiliging. U moet deze regel wijzigen om het beheerde exemplaar toegankelijk te maken via een openbaar eind punt. </li> </ul> </br> Zie voor meer informatie over open bare eindpunt beveiliging het [gebruik van Azure SQL database Managed instance veilig met een openbaar eind punt](sql-database-managed-instance-public-endpoint-securely.md).|

U kunt op **revisie + maken** klikken om geselecteerde opties te controleren voordat u een beheerd exemplaar maakt, of u kunt extra instellingen configureren **door op volgende te klikken: Aanvullende instellingen**.

### <a name="additional-settings-tab"></a>Tabblad Extra instellingen

Vul optionele informatie in op het tabblad **extra instellingen** met behulp van de onderstaande tabel. Als deze informatie wordt wegge laten, worden de standaard instellingen toegepast.

   ![Tabblad beheerde instantie voor het maken van extra instellingen](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   | Instelling| Voorgestelde waarde | Description |
   | ------ | --------------- | ----------- |
   | **Sortering** | De sortering die u wilt gebruiken voor uw beheerde exemplaar.|Als u data bases migreert vanaf SQL Server, controleert u de bron `SELECT SERVERPROPERTY(N'Collation')` sortering door te gebruiken en deze waarde te gebruiken. Zie [de server sortering instellen of wijzigen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation)voor meer informatie over sorteringen.|   
   | **Tijdzone** | De tijd zone die moet worden waargenomen door uw beheerde exemplaar.|Zie [tijd zones](sql-database-managed-instance-timezone.md)voor meer informatie.|
   | **Gebruiken als secundaire failover** | Als u het beheerde exemplaar wilt gebruiken als een failovergroep voor een exemplaar, selecteert u Ja. |
   | **Primair beheerd exemplaar** (in geval van gebruik als secundaire failover is ingesteld op Ja) | Kies het primaire beheerde exemplaar (moet al bestaan) met het beheerde exemplaar dat u maakt, wordt toegevoegd aan dezelfde DNS-zone. Dit is een vooraf ingestelde stap voor het inschakelen van de configuratie van de failovergroep na het maken. Zie [zelf studie voor meer informatie: Een door SQL Database beheerd exemplaar toevoegen aan een failovergroep](sql-database-managed-instance-failover-group-tutorial.md). |

### <a name="review--create-tab"></a>Tabblad controleren en maken

1. Selecteer het tabblad **controleren + maken** om de geselecteerde opties te controleren voordat u een beheerd exemplaar maakt.

   ![Tabblad controleren en maken van beheerd exemplaar](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

1. Selecteer **maken** om te beginnen met het inrichten van het beheerde exemplaar.

> [!IMPORTANT]
> Het implementeren van een beheerd exemplaar is een langlopende bewerking. De implementatie van de eerste instantie in het subnet duurt doorgaans veel langer dan implementeren in een subnet met bestaande beheerde exemplaren. Zie [Managed instance Management Operations](sql-database-managed-instance.md#managed-instance-management-operations)(Engelstalig) voor gemiddelde inrichtings tijden.

### <a name="monitor-deployment-progress"></a>Voortgang van de implementatie bewaken

1. Selecteer het pictogram **meldingen** om de status van de implementatie weer te geven.

    ![Voortgang van implementatie van beheerd exemplaar](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

1. Selecteer de implementatie die wordt **uitgevoerd** op de melding om het venster beheerd exemplaar te openen om de voortgang van de implementatie verder te bewaken. 

> [!TIP]
> Als u de webbrowser hebt gesloten of als u het scherm voortgang van de implementatie hebt bezocht, voert u de volgende stappen uit om het scherm voortgang van de implementatie te vinden:
> 1. Open in de Azure Portal de resource groep (ingevoerd op het tabblad **basis** ) waarnaar u een beheerd exemplaar implementeert.
> 2. Selecteer **implementaties**.
> 3. Selecteer de implementatie bewerking voor het beheerde exemplaar wordt uitgevoerd.

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

6. Ga terug naar de resourcegroep en selecteer uw beheerde exemplaar.

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
- Zie [Azure SQL database bewaken met behulp van Azure SQL-analyse](../azure-monitor/insights/azure-sql.md)voor geavanceerde bewaking van beheerde exemplaar database prestaties met ingebouwde probleemoplossings informatie.
