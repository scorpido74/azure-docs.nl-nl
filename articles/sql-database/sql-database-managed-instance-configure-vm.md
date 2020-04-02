---
title: ClientVM verbinden - beheerde instantie
description: Maak verbinding met een Azure SQL Database Managed Instance met SQL Server Management Studio vanaf een virtuele Azure-machine.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: 8b5dce0b43fac7cfd0e974f26451338ca1541f8f
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528425"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Snelstart: Azure VM configureren om verbinding te maken met een Azure SQL Database Managed Instance

In deze quickstart ziet u hoe u een virtuele Azure-machine configureert om verbinding te maken met een Azure SQL Database Managed Instance met SQL Server Management Studio (SSMS). Zie [Een point-to-site-verbinding configureren](sql-database-managed-instance-configure-p2s.md) voor een snelle start met de manier waarop u verbinding maken vanaf een on-premises clientcomputer.

## <a name="prerequisites"></a>Vereisten

Deze quickstart gebruikt de resources die zijn gemaakt in [Een beheerde instantie maken](sql-database-managed-instance-get-started.md) als uitgangspunt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Een nieuw subnet maken in de VNet beheerde instantie

Met de volgende stappen wordt een nieuw subnet gemaakt in het VNet beheerde instantie, zodat een virtuele Azure-machine verbinding kan maken met het beheerde exemplaar. Het subnet Beheerde instantie is gewijd aan Beheerde instanties. U geen andere bronnen, zoals virtuele Azure-machines, in dat subnet maken.

1. Open de resourcegroep voor het beheerde exemplaar dat u hebt gemaakt in de snelstart [van Een beheerde instantie](sql-database-managed-instance-get-started.md) maken. Selecteer het virtuele netwerk voor uw beheerde instantie.

   ![Resources van beheerd exemplaar](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Selecteer **Subnetten** en selecteer **+ Subnet** om een nieuw subnet te maken.

   ![Subnetten voor beheerde instantie](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Vul het formulier in met de informatie in deze tabel:

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ---------------- | ----------------- | ----------- |
   | **Naam** | Een geldige naam|Zie [Naamregels en beperkingen voor](/azure/architecture/best-practices/resource-naming)geldige namen.|
   | **Adresbereik (CIDR-blok)** | Een geldig bereik | De standaardwaarde is goed voor deze quickstart.|
   | **Netwerkbeveiligingsgroep** | Geen | De standaardwaarde is goed voor deze quickstart.|
   | **Routetabel** | Geen | De standaardwaarde is goed voor deze quickstart.|
   | **Service-eindpunten** | 0 geselecteerd | De standaardwaarde is goed voor deze quickstart.|
   | **Delegatie van subnet** | Geen | De standaardwaarde is goed voor deze quickstart.|

   ![Nieuw subnet Beheerde instantie voor client-VM](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Selecteer **OK** om dit extra subnet te maken in de VNet beheerde instantie.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Een virtuele machine maken in het nieuwe subnet in het VNet

In de volgende stappen ziet u hoe u een virtuele machine maakt in het nieuwe subnet om verbinding te maken met het beheerde exemplaar.

## <a name="prepare-the-azure-virtual-machine"></a>De virtuele Azure-machine voorbereiden

Aangezien SQL Managed Instance in uw privé-virtuele netwerk wordt geplaatst, moet u een Azure VM maken met een geïnstalleerd SQL-clienthulpprogramma, zoals SQL Server Management Studio of Azure Data Studio. Met deze tool u verbinding maken met de beheerde instantie en query's uitvoeren. In deze snelstart wordt SQL Server Management Studio gebruikt.

De eenvoudigste manier om een virtuele clientmachine met alle benodigde hulpprogramma's te maken, is door de Azure Resource Manager-sjablonen te gebruiken.

1. Zorg ervoor dat u bent aangemeld bij de Azure-portal op een ander browsertabblad. Selecteer vervolgens de volgende knop om een virtuele clientmachine te maken en SQL Server Management Studio te installeren:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Vul het formulier in met de informatie in de volgende tabel:

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ---------------- | ----------------- | ----------- |
   | **Abonnement** | Een geldig abonnement | Het moet een abonnement zijn waarin u toestemming hebt om nieuwe bronnen te maken. |
   | **Resourcegroep** |De resourcegroep die u hebt opgegeven in de snelstart [van beheerde instantie](sql-database-managed-instance-get-started.md) maken.|Deze resourcegroep moet de groep zijn waarin het VNet bestaat.|
   | **Locatie** | De locatie voor de resourcegroep | Deze waarde wordt ingevuld op basis van de geselecteerde resourcegroep. |
   | **Naam virtuele machine**  | Een geldige naam | Zie [Naamregels en beperkingen voor](/azure/architecture/best-practices/resource-naming)geldige namen.|
   |**Gebruikersnaam beheerder**|Elke geldige gebruikersnaam|Zie [Naamregels en beperkingen voor](/azure/architecture/best-practices/resource-naming)geldige namen. Maak geen gebruik van 'serverbeheerder' aangezien dit een rol is die op serverniveau is gereserveerd.<br>U gebruikt deze gebruikersnaam wanneer u [verbinding maakt met de VM.](#connect-to-virtual-machine)|
   |**Wachtwoord**|Een geldig wachtwoord|Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>U gebruikt dit wachtwoord wanneer u [verbinding maakt met de VM.](#connect-to-virtual-machine)|
   | **Virtuele machinegrootte** | Elke geldige maat | De standaardinstelling in deze sjabloon van **Standard_B2s** is voldoende voor deze quickstart. |
   | **Locatie**|[resourceGroup().locatie].| Verander deze waarde niet. |
   | **Naam virtueel netwerk**|Het virtuele netwerk waarin u de beheerde instantie hebt gemaakt.|
   | **Subnetnaam**|De naam van het subnet dat u in de vorige procedure hebt gemaakt| Kies niet het subnet waarin u de beheerde instantie hebt gemaakt.|
   | **artefacten Locatie** | [implementatie().properties.templateLink.uri] | Verander deze waarde niet. |
   | **artefacten Locatie Sas-token** | leeg laten | Verander deze waarde niet. |

   ![client-vm maken](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Als u de voorgestelde VNet-naam en het standaardsubnet hebt gebruikt bij [het maken van uw beheerde instantie,](sql-database-managed-instance-get-started.md)hoeft u de laatste twee parameters niet te wijzigen. Anders moet u deze waarden wijzigen in de waarden die u hebt ingevoerd bij het instellen van de netwerkomgeving.

3. Schakel het **selectievakje Ik ga akkoord met de bovenstaande voorwaarden.**
4. Selecteer **Kopen** om de Azure VM in uw netwerk te implementeren.
5. Selecteer het pictogram voor **Meldingen** om de status van de implementatie te bekijken.

> [!IMPORTANT]
> Ga niet verder tot ongeveer 15 minuten nadat de virtuele machine is gemaakt om tijd te geven voor de scripts na het maken om SQL Server Management Studio te installeren.

## <a name="connect-to-virtual-machine"></a>Verbinding maken met de virtuele machine

In de volgende stappen wordt uitgelegd hoe u verbinding maakt met uw nieuwe virtuele machine via verbinding met een extern bureaublad.

1. Nadat de implementatie is voltooid, gaat u naar de virtuele machine-resource.

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Selecteer **Verbinden**.

   Er wordt een extern bureaublad-protocolbestandformulier (.rdp-bestand) weergegeven met het openbare IP-adres en het poortnummer voor de virtuele machine.

   ![RDP-formulier](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Selecteer **RDP-bestand downloaden**.

   > [!NOTE]
   > U SSH ook gebruiken om verbinding te maken met uw VM.

4. Sluit het **formulier Verbinding met virtuele machine.**
5. Open het gedownloade RDP-bestand om verbinding met de VM te maken.
6. Selecteer **Verbinding maken**wanneer u daarom wordt gevraagd. Op een Mac hebt u een RDP-client nodig, zoals deze [Extern-bureaubladclient](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) uit de Mac App Store.

7. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine en kies **OK**.

8. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Kies **Ja** of **Doorgaan** om door te gaan met de verbinding.

U bent verbonden met uw virtuele machine in het dashboard serverbeheer.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>SSMS gebruiken om verbinding te maken met het beheerde exemplaar

1. Open SQL Server Management Studio (SSMS) in de virtuele machine.

   Het duurt een paar momenten om te openen als het nodig heeft om de configuratie te voltooien, omdat dit de eerste keer SSMS is gestart.
2. Voer in het dialoogvenster **Verbinding maken met server** de volledig gekwalificeerde **hostnaam** voor uw beheerde instantie in het vak **Servernaam** in. Selecteer **SQL Server-verificatie,** geef uw gebruikersnaam en wachtwoord op en selecteer **Verbinding maken**.

    ![ssms verbinden](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Nadat u verbinding hebt gemaakt, kunt u uw systeem en gebruikersdatabases bekijken in het knooppunt Databases. Daarnaast kunt u diverse objecten bekijken in de knooppunten Beveiliging, Server-objecten, Replicatie, Beheer, SQL Server Agent en XEvent Profiler.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een point-to-site-verbinding configureren](sql-database-managed-instance-configure-p2s.md)voor een snelle start met het weermaken van verbinding vanaf een on-premises clientcomputer via een point-to-site-verbinding.
- Zie [Verbinding maken tussen uw toepassing en het beheerde exemplaar van Azure SQL Database](sql-database-managed-instance-connect-app.md) voor een overzicht van de verbindingsopties voor toepassingen.
- Als u een bestaande SQL Server-database wilt herstellen van on-premises naar een beheerde instantie, u de [Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) gebruiken voor migratie of de opdracht [T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) om te herstellen uit een databaseback-upbestand.
