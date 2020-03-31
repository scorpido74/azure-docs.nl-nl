---
title: Een domeinonafhankelijke werkgroepbeschikbaarheidsgroep configureren
description: Meer informatie over het configureren van een active directory-domeinonafhankelijke werkgroep Always On availability-groep op een virtuele SQL Server-machine in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 72c04cf5e3e5fbdeac2d267dfc7b2703bd37a1c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122673"
---
# <a name="configure-a-workgroup-availability-group"></a>Een groep voor beschikbaarheid van werkgroepen configureren 

In dit artikel worden de stappen uitgelegd die nodig zijn om een Active Directory-domeinonafhankelijk cluster te maken met een groep beschikbaarheid always on. dit wordt ook wel een werkgroepcluster genoemd. Dit artikel richt zich op de stappen die relevant zijn voor het voorbereiden en configureren van de werkgroep en beschikbaarheidsgroep, en glinstert over stappen die in andere artikelen worden behandeld, zoals het maken van het cluster of het implementeren van de beschikbaarheidsgroep. 


## <a name="prerequisites"></a>Vereisten

Als u een groep beschikbaarheidsgroep wilt configureren, hebt u het volgende nodig:
- Ten minste twee Virtuele Apparaten (Of hoger) van Windows Server 2016 (of hoger) met SQL Server 2016 (of hoger), geïmplementeerd in dezelfde beschikbaarheidsset of verschillende beschikbaarheidszones, met behulp van statische IP-adressen. 
- Een lokaal netwerk met minimaal 4 gratis IP-adressen op het subnet. 
- Een account op elke machine in de beheerdersgroep die ook sysadmin-rechten heeft binnen SQL Server. 
- Open poorten: TCP 1433, TCP 5022, TCP 59999. 

Ter referentie worden in dit artikel de volgende parameters gebruikt, maar kunnen zo nodig worden gewijzigd: 

| **Naam** | **Parameter** |
| :------ | :---------------------------------- |
| **Knooppunt1**   | AGNode1 (10.0.0.4) |
| **Knooppunt 2**   | AGNode2 (10.0.0.5) |
| **Clusternaam** | AGWGAG (10.0.0.6) |
| **Luisteraar** | AGListener (10.0.0.7) | 
| **DNS-achtervoegsel** | ag.wgcluster.example.com | 
| **Naam werkgroep** | AGWorkgroep | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>DNS-achtervoegsel instellen 

Configureer in deze stap het DNS-achtervoegsel voor beide servers. Bijvoorbeeld `ag.wgcluster.example.com`. Hiermee u de naam van het object waarmee u verbinding wilt maken `AGNode1.ag.wgcluster.example.com`gebruiken als een volledig gekwalificeerd adres binnen uw netwerk, zoals. 

Voer de volgende stappen uit om het DNS-achtervoegsel te configureren:

1. RDP in uw eerste knooppunt en open Serverbeheer. 
1. Selecteer **Lokale server** en selecteer vervolgens de naam van uw virtuele machine onder **Computernaam**. 
1. Selecteer **Wijzigen...** onder **De naam van deze computer wijzigen...**. 
1. Wijzig de naam van de naam van de `AGWORKGROUP`werkgroep als iets zinvols, zoals: 

   ![Naam werkgroep wijzigen](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. Selecteer **Meer...** om het dialoogvenster **DNS-achtervoegsel en NetBIOS-computernaam** te openen. 
1. Typ de naam van uw DNS-achtervoegsel onder **Primair DNS-achtervoegsel van deze computer**, zoals `ag.wgcluster.example.com` en selecteer **OK:** 

   ![DNS-achtervoegsel toevoegen](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. Controleer of de **naam Volledige computer** nu het DNS-achtervoegsel weergeeft en selecteer **OK** om de wijzigingen op te slaan: 

   ![DNS-achtervoegsel toevoegen](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. Start de server opnieuw op wanneer u wordt gevraagd dit te doen. 
1. Herhaal deze stappen op andere knooppunten die moeten worden gebruikt voor de beschikbaarheidsgroep. 

## <a name="edit-host-file"></a>Hostbestand bewerken

Aangezien er geen actieve directory is, is er geen manier om windows-verbindingen te verifiëren. Wijs als zodanig vertrouwen toe door het hostbestand te bewerken met een teksteditor. 

Voer de volgende stappen uit om het hostbestand te bewerken:

1. RDP in uw virtuele machine. 
1. Gebruik **Verkenner** om `c:\windows\system32\drivers\etc`naar . 
1. Klik met de rechtermuisknop op het **hosts-bestand** en open het bestand met **Kladblok** (of een andere teksteditor).
1. Voeg aan het einde van het bestand een vermelding toe voor elk knooppunt, `IP Address, DNS Suffix #comment` de beschikbaarheidsgroep en de listener in de vorm van like: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Items voor het IP-adres, het cluster en de listener toevoegen aan het hostbestand](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>Machtigingen instellen

Aangezien er geen Active Directory is om machtigingen te beheren, moet u handmatig toestaan dat een niet-ingebouwd lokaal beheerdersaccount het cluster maakt. 

Voer hiervoor de volgende PowerShell-cmdlet uit in een administratieve PowerShell-sessie op elk knooppunt: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Het failovercluster maken

In deze stap maakt u het failovercluster. Als u niet bekend bent met deze stappen, u ze volgen via de [failoverclusterzelfstudie.](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct)

Opmerkelijke verschillen tussen de zelfstudie en wat moet worden gedaan voor een werkgroepcluster:
- Schakel **opslag**en **opslagruimte direct uit** wanneer u de clustervalidatie uitvoert. 
- Voeg bij het toevoegen van de knooppunten aan het cluster de volledig gekwalificeerde naam toe, zoals:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Schakel het **selectievakje Alle in aanmerking komende opslag toevoegen aan het cluster uit**. 

Zodra het cluster is gemaakt, wijst u een statisch cluster-IP-adres toe. Hiervoor volgt u de volgende stappen:

1. Open op een van de knooppunten **Failoverclusterbeheer,** selecteer het cluster, klik met de rechtermuisknop op de **naam: \<clusternam>** onder **Clusterkernbronnen** en selecteer **vervolgens Eigenschappen**. 

   ![Eigenschappen voor de clusternaam starten](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. Selecteer het IP-adres onder **IP-adressen** en selecteer **Bewerken**. 
1. Selecteer **Statisch gebruiken,** geef het IP-adres van het cluster op en selecteer **OK:** 

   ![Een statisch IP-adres opgeven voor het cluster](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. Controleer of uw instellingen er correct uitzien en selecteer **OK** om ze op te slaan:

   ![Clustereigenschappen verifiëren](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Een cloudgetuige maken 

Configureer in deze stap een getuige voor het delen van een cloud. Als u niet bekend bent met de stappen, raadpleegt u de [zelfstudie failovercluster](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness). 

## <a name="enable-availability-group-feature"></a>Functie beschikbaarheidsgroep inschakelen 

Schakel in deze stap de functie beschikbaarheidsgroep in. Als u niet bekend bent met de stappen, raadpleegt u de zelfstudie van de [beschikbaarheidsgroep](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificate"></a>Sleutels en certificaat maken

Maak in deze stap certificaten die een SQL-login gebruikt op het versleutelde eindpunt. Maak een map op elk knooppunt om de `c:\certs`certificaatback-ups vast te houden, zoals . 

Voer de volgende stappen uit om het eerste knooppunt te configureren: 

1. Open **SQL Server Management Studio** en maak verbinding `AGNode1`met uw eerste knooppunt, zoals . 
1. Open een **nieuw queryvenster** en voer de volgende Transact-SQL-instructie (T-SQL) uit na het bijwerken naar een complex en veilig wachtwoord:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Maak vervolgens het HADR-eindpunt en gebruik het certificaat voor verificatie door deze Transact-SQL-instructie (T-SQL) uit te voeren:

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Gebruik **Verkenner** om naar de bestandslocatie te gaan `c:\certs`waar het certificaat zich bevindt, zoals . 
1. Maak handmatig een kopie van het `AGNode1Cert.crt`certificaat, zoals , vanaf het eerste knooppunt, en breng het over naar dezelfde locatie op het tweede knooppunt. 

Voer de volgende stappen uit om het tweede knooppunt te configureren: 

1. Maak verbinding met het tweede knooppunt met `AGNode2`SQL Server Management **Studio**, zoals . 
1. Voer in een **venster Nieuwe query** de volgende Transact-SQL-instructie (T-SQL) uit nadat u bent bijgewerkt naar een complex en veilig wachtwoord: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Maak vervolgens het HADR-eindpunt en gebruik het certificaat voor verificatie door deze Transact-SQL-instructie (T-SQL) uit te voeren:

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Gebruik **Verkenner** om naar de bestandslocatie te gaan `c:\certs`waar het certificaat zich bevindt, zoals . 
1. Maak handmatig een kopie van het `AGNode2Cert.crt`certificaat, zoals , vanaf het tweede knooppunt, en breng het over naar dezelfde locatie op het eerste knooppunt. 

Als er andere knooppunten in het cluster zijn, herhaalt u deze stappen daar ook en wijzigt u de respectievelijke certificaatnamen. 

## <a name="create-logins"></a>Aanmeldingen maken

Certificaatverificatie wordt gebruikt om gegevens tussen knooppunten te synchroniseren. Maak hiervoor een login voor het andere knooppunt, maak een gebruiker voor de aanmelding, maakt een certificaat voor de aanmelding om het back-upcertificaat te gebruiken en verleen vervolgens verbinding op het spiegelende eindpunt. 

Voer hiervoor eerst de volgende Transact-SQL -query (T-SQL) uit `AGNode1`op het eerste knooppunt, zoals: 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Voer vervolgens de volgende Transact-SQL -query (T-SQL) uit `AGNode2`op het tweede knooppunt, zoals: 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Als er andere knooppunten in het cluster zijn, herhaalt u deze stappen daar ook en wijzigt u de respectievelijke certificaat- en gebruikersnamen. 

## <a name="configure-availability-group"></a>Beschikbaarheidsgroep configureren

Configureer in deze stap uw beschikbaarheidsgroep en voeg uw databases toe. Maak op dit moment geen listener. Als u niet bekend bent met de stappen, raadpleegt u de zelfstudie van de [beschikbaarheidsgroep](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group). Zorg ervoor dat u een failover en failback te starten om te controleren of alles werkt zoals het zou moeten zijn. 

   > [!NOTE]
   > Als er een fout optreedt tijdens het synchronisatieproces, `NT AUTHORITY\SYSTEM` moet u mogelijk sysadmin-rechten verlenen om `AGNode1` clusterbronnen op het eerste knooppunt te maken, zoals tijdelijk. 

## <a name="configure-load-balancer"></a>Load balancer configureren

Configureer in deze laatste stap de load balancer met de [Azure-portal](virtual-machines-windows-portal-sql-alwayson-int-listener.md) of [PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)


## <a name="next-steps"></a>Volgende stappen

U [az SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) ook gebruiken om een beschikbaarheidsgroep te configureren. 


