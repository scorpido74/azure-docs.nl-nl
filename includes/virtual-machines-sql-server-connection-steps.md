---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d679767f1a4be3ecdcf1811662c72dbbe2df33a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84014761"
---
### <a name="open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine"></a>TCP-poorten in de Windows-firewall openen voor het standaardexemplaar van de database-engine
1. Maak verbinding met de virtuele machine via Extern bureaublad. Zie [De virtuele machine openen via Extern bureaublad](../articles/azure-sql/virtual-machines/windows/create-sql-vm-portal.md#remotedesktop) voor gedetailleerde instructies voor het verbinden met de virtuele machine.
2. Wanneer u bent aangemeld, typt u **WF. msc**in het Start scherm en drukt u vervolgens op ENTER.
   
    ![Het firewallprogramma starten](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. Open **Windows Firewall met geavanceerde beveiliging**, klik in het linkerdeelvenster met de rechtermuisknop op **Regels voor binnenkomende verkeer** en klik vervolgens in het actievenster op **Nieuwe regel**.
   
    ![Nieuwe regel](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. In het dialoogvenster **Wizard Nieuwe regel voor binnenkomende verbindingen** selecteert u **Poort** onder **Regeltype**. Klik vervolgens op **Volgende**.
5. Gebruik in het dialoogvenster **Protocol en poorten** de standaardinstelling **TCP**. Typ in het vak **Specifieke lokale poorten** het poortnummer van het exemplaar van de database-engine (**1433** voor het standaardexemplaar of uw waarde voor de particuliere poort in de eindpuntstap).
   
    ![TCP-poort 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. Klik op **Volgende**.
7. Selecteer **De verbinding toestaan** in het dialoogvenster **Actie** en klik vervolgens op **Volgende**.
   
    **Opmerking over de beveiliging:** de optie **De verbinding toestaan als deze veilig is** kan extra beveiliging bieden. Selecteer deze optie als u aanvullende beveiligingsopties wilt configureren in uw omgeving.
   
    ![Verbindingen toestaan](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. Selecteer **Openbaar**, **Particulier**, en **Domein** in het dialoogvenster **Profiel**. Klik op **Volgende**.
   
    **Opmerking over de beveiliging:** de optie **Openbaar** biedt toegang via internet. Selecteer een meer beperkend profiel indien mogelijk.
   
    ![Openbaar profiel](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. Typ een naam en beschrijving voor deze regel in het dialoogvenster **Naam** en klik vervolgens op **Voltooien**.
   
    ![Regelnaam](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Open naar behoefte aanvullende poorten voor andere onderdelen. Zie [Configuring the Windows Firewall to Allow SQL Server Access](https://msdn.microsoft.com/library/cc646023.aspx) (De Windows-firewall configureren voor toegang tot SQL Server) voor meer informatie.

### <a name="configure-sql-server-to-listen-on-the-tcp-protocol"></a>SQL Server configureren om te luisteren naar het TCP-protocol

[!INCLUDE [Enable TCP](virtual-machines-sql-server-connection-tcp-protocol.md)]

### <a name="configure-sql-server-for-mixed-mode-authentication"></a>SQL Server configureren voor verificatie in gemengde modus
De database-engine van SQL Server kan Windows-verificatie niet gebruiken zonder domeinomgeving. Als u vanaf een andere computer verbinding wilt maken met de database-engine, moet u SQL Server configureren voor verificatie in gemengde modus. Verificatie in gemengde modus maakt zowel SQL Server-verificatie als Windows-verificatie mogelijk.

> [!NOTE]
> U hoeft verificatie in gemengde modus mogelijk niet te configureren als u een virtueel netwerk van Azure hebt ingesteld met een geconfigureerde domeinomgeving.
> 
> 

1. Terwijl u verbinding hebt met de virtuele machine, typt u **SQL Server Management Studio** op de startpagina en klikt u op het geselecteerde pictogram.
   
    Wanneer u Management Studio voor het eerst opent, moet de gebruikersomgeving van Management Studio worden gemaakt. Dit kan even duren.
2. Het dialoogvenster **Verbinding maken met server** in Management Studio wordt geopend. Typ de naam van de virtuele machine in het vak **Servernaam** om verbinding te maken met de database-engine via Objectverkenner. U kunt in plaats van de naam van de virtuele machine ook **(lokaal)** of een punt gebruiken als de **servernaam**. Selecteer **Windows-verificatie**en verlaat ***your_VM_name \ your_local_administrator*** in het vak **gebruikers naam** . Klik op **Verbinden**.
   
    ![Verbinding maken met server](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. Klik in de SQL Server Management Studio-objectverkenner met de rechtermuisknop op de naam van het SQL Server-exemplaar (de naam van de virtuele machine) en klik vervolgens op **Eigenschappen**.
   
    ![Servereigenschappen](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. Ga naar **Serververificatie** op de pagina **Beveiliging**, selecteer **SQL Server- en Windows-verificatiemodus** en klik vervolgens op **OK**.
   
    ![Verificatiemodus selecteren](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. Klik in het dialoogvenster SQL Server Management Studio op **OK** om te erkennen dat SQL Server opnieuw moet worden gestart.
6. Klik in Objectverkenner met de rechtermuisknop op **Opnieuw starten**. (Als SQL Server Agent actief is, moet deze ook opnieuw worden gestart.)
   
    ![Opnieuw starten](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. Klik in het dialoogvenster SQL Server Management Studio op **Ja** om te bevestigen dat u SQL Server opnieuw wilt starten.

### <a name="create-sql-server-authentication-logins"></a>Aanmelding voor SQL Server-verificatie maken
Als u vanaf een andere computer verbinding wilt maken met de database-engine, moet u ten minste één aanmelding voor SQL Server-verificatie maken.

1. Open de SQL Server Management Studio-objectverkenner en breid de map uit van het serverexemplaar waarvoor u de nieuwe aanmelding wilt maken.
2. Klik met de rechtermuisknop op de map **Beveiliging**, wijs **Nieuw** aan en selecteer **Aanmelding...**.
   
    ![Nieuwe aanmelding](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. Het dialoogvenster **Aanmelding - nieuw** wordt geopend. Geef op de pagina **Algemeen** de naam van de nieuwe gebruiker op in het vak **Aanmeldingsnaam**.
4. Selecteer **SQL Server-verificatie**.
5. Geef in het vak **Wachtwoord** een wachtwoord op voor de nieuwe gebruiker. Typ dit wachtwoord opnieuw in het vak **Wachtwoord bevestigen**.
6. Selecteer de vereiste opties voor het afdwingen van wachtwoorden (**Afdwingbeleid voor wachtwoorden**, **Vervaldatum afdwingbeleid** en **De gebruiker moet het wachtwoord bij de volgende aanmelding wijzigen**). Als u deze aanmelding zelf gebruikt, hoeft u niet te vereisen dat het wachtwoord bij de volgende aanmelding wordt gewijzigd.
7. Selecteer een standaarddatabase voor de aanmelding in de lijst **Standaarddatabase**. **master** is de standaardwaarde voor deze optie. Als u nog geen gebruikersdatabase hebt gemaakt, laat u de instelling op **master**.
   
    ![Aanmeldingseigenschappen](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. Als dit de eerste aanmelding is die u maakt, kunt u deze aanwijzen als SQL Server-beheerder. Schakel hiervoor het selectievakje **sysadmin** in op de pagina **Serverrollen**.
   
   > [!NOTE]
   > Gebruikers met de vaste serverrol sysadmin hebben volledige controle over de database-engine. Beperk deze rol daarom tot gebruikers die volledige controle nodig hebben.
   > 
   > 
   
   ![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. Klik op OK.

Zie [Create a Login](https://msdn.microsoft.com/library/aa337562.aspx) (Een aanmelding maken) voor meer informatie over SQL Server-aanmeldingen.

