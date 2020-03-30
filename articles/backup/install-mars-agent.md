---
title: De MARS-agent (Microsoft Azure Recovery Services) installeren
description: Meer informatie over het installeren van de MARS-agent (Microsoft Azure Recovery Services) om een back-up te maken van Windows-machines.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: b9a6791709d5aff82d11bbf10e5f084fd8c1a000
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247759"
---
# <a name="install-the-azure-backup-mars-agent"></a>De Azure Backup MARS-agent installeren

In dit artikel wordt uitgelegd hoe u de MARS-agent (Microsoft Azure Recovery Services) installeert. MARS wordt ook wel de Azure Backup-agent genoemd.

## <a name="about-the-mars-agent"></a>Over de MARS-agent

Azure Backup maakt gebruik van de MARS-agent om een back-up te maken van bestanden, mappen en systeemstatus van on-premises machines en Azure VM's. Deze back-ups worden opgeslagen in een vault van Recovery Services in Azure. U de agent uitvoeren:

* Direct op on-premises Windows-machines. Deze machines kunnen rechtstreeks een back-up maken naar een vault van Recovery Services in Azure.
* Op Azure VM's die Windows naast elkaar uitvoeren met de Azure VM-back-upextensie. De agent maakt een back-up van specifieke bestanden en mappen op de VM.
* Op een MABS-exemplaar (Microsoft Azure Backup Server) of een DPM-server (System Center Data Protection Manager). In dit scenario maken machines en workloads een back-up naar MABS of Data Protection Manager. Vervolgens gebruikt MABS of Data Protection Manager de MARS-agent om een back-up te maken van een kluis in Azure.

De gegevens die beschikbaar zijn voor back-up, zijn afhankelijk van waar de agent is geïnstalleerd.

> [!NOTE]
> Over het algemeen maakt u een back-up van een Azure-vm met behulp van een Azure Backup-extensie op de VM. Met deze methode wordt een back-up gemaakt van de hele VM. Als u een back-up wilt maken van specifieke bestanden en mappen op de VM, installeert en gebruikt u de MARS-agent naast de extensie. Zie [Architectuur van een ingebouwde Azure VM-back-up](backup-architecture.md#architecture-built-in-azure-vm-backup)voor meer informatie.

![Stappen voor back-upproces](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Voordat u begint

* Meer informatie over hoe [Azure Backup de MARS-agent gebruikt om een back-up te maken van Windows-machines.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Meer informatie over de [back-uparchitectuur](backup-architecture.md#architecture-back-up-to-dpmmabs) waarop de MARS-agent wordt uitgevoerd op een secundaire MABS- of Data Protection Manager-server.
* Bekijk [wat er wordt ondersteund en wat je back-ups](backup-support-matrix-mars-agent.md) maken door de MARS-agent.
* Zorg ervoor dat u een Azure-account hebt als u een back-up van een server of client naar Azure wilt maken. Als je geen account hebt, kun je in slechts een paar minuten een [gratis](https://azure.microsoft.com/free/) account aanmaken.
* Controleer de internettoegang op de machines waarvan u een back-up wilt maken.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Opslagreplicatie wijzigen

Standaard gebruiken kluizen [georedundante opslag (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Als de kluis uw primaire back-upmechanisme is, raden we u aan GRS te gebruiken.
* U [lrs (localredundante opslag)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) gebruiken om azure-opslagkosten te verlagen.

Ga als lid van het type opslagreplicatie:

1. Selecteer **eigenschappen** in de nieuwe kluis onder de sectie **Instellingen.**

1. Selecteer Op de pagina **Eigenschappen** onder **Back-upconfiguratie**de optie **Bijwerken**.

1. Selecteer het type opslagreplicatie en selecteer **Opslaan**.

    ![Back-upconfiguratie bijwerken](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> U het type opslagreplicatie niet wijzigen nadat de kluis is ingesteld en bevat back-upitems. Als u dit wilt doen, moet u de kluis opnieuw maken.
>

### <a name="verify-internet-access"></a>Internettoegang verifiëren

Als uw machine beperkte internettoegang heeft, moet u ervoor zorgen dat firewall-instellingen op de machine of proxy de volgende URL's en IP-adressen toestaan:

*  URL's
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* IP-adressen
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Azure ExpressRoute gebruiken

U een back-up van uw gegevens maken via Azure ExpressRoute door gebruik te maken van openbare peering (beschikbaar voor oude circuits) en Microsoft-peering. Back-up via private peering wordt niet ondersteund.

Als u openbare peering wilt gebruiken, moet u eerst de toegang tot de volgende domeinen en adressen garanderen:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Als u Microsoft-peering wilt gebruiken, selecteert u de volgende services, regio's en relevante communitywaarden:

* Azure Active Directory (12076:5060)
* Azure-regio, afhankelijk van de locatie van uw vault voor Herstelservices
* Azure Storage, afhankelijk van de locatie van uw Vault Recovery Services

Zie [Routeringsvereisten expressroute voor](https://docs.microsoft.com/azure/expressroute/expressroute-routing)meer informatie.

> [!NOTE]
> Openbare peering is afgeschaft voor nieuwe circuits.

Alle voorgaande URL's en IP-adressen gebruiken het HTTPS-protocol op poort 443.

### <a name="private-endpoints"></a>Privéeindpunten

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Download de MARS-agent

Download de MARS-agent zodat u deze installeren op de machines waarvan u een back-up wilt maken.

Als u de agent al op machines hebt geïnstalleerd, moet u ervoor zorgen dat u de nieuwste versie van de agent uitvoert. Zoek de nieuwste versie in de portal, of ga direct naar de [download](https://aka.ms/azurebackup_agent).

1. Selecteer **Back-up**in de kluis onder **Aan de slag**.

    ![Het back-updoel openen](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. Selecteer **On-premises**onder **Waar wordt uw werkbelasting uitgevoerd?** Selecteer deze optie, zelfs als u de MARS-agent op een Azure VM wilt installeren.
1. Selecteer **Bestanden en mappen**onder Wat wilt u een **back-up maken?** U ook **Systeemstatus**selecteren. Er zijn veel andere opties beschikbaar, maar deze opties worden alleen ondersteund als u een secundaire back-upserver uitvoert. Selecteer **Infrastructuur voorbereiden**.

    ![Bestanden en mappen configureren](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Download de MARS-agent voor **voorbereiden infrastructuur**onder de **agent Herstelservices installeren.**

    ![De infrastructuur voorbereiden](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. Selecteer **Opslaan**in het downloadmenu . Standaard wordt het bestand *MARSagentinstaller.exe* opgeslagen in de map Downloads.

1. Selecteer **Al downloaden of gebruiken van de nieuwste Recovery Services Agent**en download vervolgens de vault credentials.

    ![Kluisreferenties downloaden](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Selecteer **Opslaan**. Het bestand wordt gedownload naar de map Downloads. U het bestand met vault-referenties niet openen.

## <a name="install-and-register-the-agent"></a>De agent installeren en registreren

1. Voer het *MARSagentinstaller.exe-bestand* uit op de machines die u wilt back-ups maken.
1. Selecteer **installatie-instellingen**in de wizard Installatie-installatie van MARS- agent . Kies daar waar u de agent wilt installeren en kies een locatie voor de cache. Selecteer **vervolgens Volgende**.
   * Azure Backup gebruikt de cache om gegevensmomentopnamen op te slaan voordat deze naar Azure worden verzonden.
   * De cachelocatie moet vrije ruimte hebben die gelijk is aan ten minste 5 procent van de grootte van de gegevens waar u een back-up van maakt.

    ![Installatie-instellingen kiezen in de wizard Installatie van MARS-agent](./media/backup-configure-vault/mars1.png)

1. Geef **voor proxyconfiguratie**op hoe de agent die op de Windows-machine wordt uitgevoerd, verbinding maakt met internet. Selecteer **vervolgens Volgende**.

   * Als u een aangepaste proxy gebruikt, geeft u de benodigde proxy-instellingen en referenties op.
   * Houd er rekening mee dat de agent toegang nodig heeft tot [specifieke URL's.](#before-you-start)

    ![Internettoegang instellen in de wizard MARS](./media/backup-configure-vault/mars2.png)

1. Controleer **voor installatie**de vereisten en selecteer **Installeren**.
1. Nadat de agent is geïnstalleerd, selecteert **u Doorgaan naar registratie**.
1. Blader in **Register Server Wizard** > **Vault Identification**naar en selecteer het referentiesbestand dat u hebt gedownload. Selecteer **vervolgens Volgende**.

    ![Vault-referenties toevoegen met de wizard Server registreren](./media/backup-configure-vault/register1.png)

1. Geef op de pagina **Versleutelingsinstelling** een wachtwoordzin op die wordt gebruikt om back-ups voor de machine te versleutelen en te decoderen.

    * Sla de wachtwoordzin op een veilige locatie op. Je hebt het nodig om een back-up te herstellen.
    * Als u de wachtwoordzin verliest of vergeet, kan Microsoft u niet helpen bij het herstellen van de back-upgegevens.

1. Selecteer **Finish**. De agent is nu geïnstalleerd, en uw machine is geregistreerd bij de kluis. U kunt nu uw back-up configureren en plannen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van back-ups van Windows-machines met de Azure Backup MARS-agent](backup-windows-with-mars-agent.md)
