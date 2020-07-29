---
title: 'Zelfstudie: Back-ups maken van virtuele Windows-machines in de Azure-portal'
description: In deze zelfstudie leert u hoe u Azure Portal gebruikt om uw virtuele Windows-machines te beschermen met Azure Backup.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: recovery
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b5b3d1240c621a1bcdc135825e70fe164452a428
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500392"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Zelfstudie: Back-ups maken en bestanden herstellen voor virtuele Windows-machines in Azure

U kunt uw gegevens beschermen door regelmatig back-ups te maken. Azure Backup maakt herstelpunten die worden opgeslagen in geografisch redundante Recovery Services-kluizen. Wanneer u vanaf een herstelpunt herstelt, kunt u de hele VM of specifieke bestanden herstellen. In dit artikel wordt uitgelegd hoe u één bestand herstelt naar een VM waarop Windows Server en IIS worden uitgevoerd. Als u nog geen VM hebt die u kunt gebruiken, kunt u er een maken met behulp van de [Windows-snelstart](quick-create-portal.md). In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een back-up maken van een VM
> * Een dagelijkse back-up plannen
> * Een bestand herstellen vanaf een back-up

## <a name="backup-overview"></a>Overzicht van Backup

Wanneer er een back-uptaak wordt gestart met de Azure Backup-service, wordt de back-upextensie geactiveerd om een momentopname van een bepaald tijdstip te maken. De Azure Backup-service maakt gebruik van de [VMSnapshot-extensie](../extensions/vmsnapshot-windows.md). De extensie is geïnstalleerd tijdens de eerste VM-back-up als de VM actief is. Als de VM niet actief is, wordt met de Backup-service een momentopname gemaakt van de onderliggende opslag (aangezien er geen schrijfbewerkingen van toepassingen plaatsvinden als de VM is gestopt).

Wanneer er een momentopname van virtuele Windows-machines wordt gemaakt, werkt de Backup-service samen met de Volume Shadow Copy Service (VSS) om een consistente momentopname van de schijven van de virtuele machine op te halen. Nadat de momentopname is gemaakt met de Azure Backup-service, worden de gegevens overgedragen naar de kluis. Voor maximale efficiëntie wordt met de service geïdentificeerd welke gegevensblokken sinds de vorige back-up zijn gewijzigd. Alleen deze worden vervolgens overgedragen.

Wanneer de gegevensoverdracht is voltooid, wordt de momentopname verwijderd en wordt er een herstelpunt gemaakt.

## <a name="create-a-backup"></a>Een back-up maken
U plant als volgt een eenvoudige dagelijkse back-up naar een Recovery Services-kluis. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Selecteer **Virtuele machines** in het menu aan de linkerkant. 
1. Selecteer in de lijst de virtuele machine waarvan u een back-up wilt maken.
1. Klik in het gedeelte **Bewerkingen** van de VM-blade op **Back-up**. De blade **Back-up inschakelen** wordt geopend.
1. Klik in **Recovery Services-kluis** op **Nieuwe maken** en geef de naam op voor de nieuwe kluis. Een nieuwe kluis wordt gemaakt in dezelfde resourcegroep en op dezelfde locatie als de virtuele machine.
1. Behoud onder **Back-upbeleid kiezen** de standaardinstelling **(New) DailyPolicy** en klik vervolgens op **Back-up inschakelen**.
1. Klik op de blade **Back-up** op **Nu een back-up maken** om een initieel herstelpunt te maken.
1. Klik op de blade **Nu een back-up maken** op het kalenderpictogram en selecteer in de kalender hoe lang het herstelpunt wordt bewaard. Klik vervolgens op **OK**.
1. Op de blade **Backup** voor uw VM ziet u het aantal voltooide herstelpunten.


    ![Herstelpunten](./media/tutorial-backup-vms/backup-complete.png)
    
De eerste back-up duurt ongeveer 20 minuten. Ga nadat de back-up is voltooid verder met het volgende gedeelte van deze zelfstudie.

## <a name="recover-a-file"></a>Een bestand herstellen

Als u een bestand per ongeluk verwijdert of onbedoelde wijzigingen in een bestand aanbrengt, kunt u het bestand herstellen vanuit de back-upkluis. Bestandsherstel maakt gebruik van een script dat wordt uitgevoerd op de VM, om het herstelpunt te koppelen als een lokaal station. Deze stations blijven gedurende 12 uur gekoppeld zodat u bestanden vanaf het herstelpunt kunt kopiëren en herstellen op de VM.  

In dit voorbeeld laten we zien hoe u het installatiekopiebestand dat voor de standaardwebpagina voor IIS wordt gebruikt, kunt herstellen. 

1. Open een browser en maak verbinding met het IP-adres van de VM om de standaard IIS-pagina weer te geven.

    ![Standaard IIS-webpagina](./media/tutorial-backup-vms/iis-working.png)

1. Maak verbinding met de VM.
1. Open **Verkenner** op de VM, navigeer naar \inetpub\wwwroot en verwijder het bestand **iisstart.png**.
1. Vernieuw de browser op uw lokale computer om te kijken of de installatiekopie op de standaard IIS-pagina weg is.

    ![Standaard IIS-webpagina](./media/tutorial-backup-vms/iis-broken.png)

1. Open een nieuw tabblad op uw lokale computer en ga naar [Azure Portal](https://portal.azure.com).
1. Selecteer **Virtuele machines** in het menu aan de linkerkant en selecteer de VM in de lijst.
1. Klik in het gedeelte **Bewerkingen** van de VM-blade op **Back-up**. De blade **Back-up** wordt geopend. 
1. Selecteer in het menu boven aan de blade de optie **Bestandsherstel**. De blade **Bestandsherstel** wordt geopend.
1. In **Stap 1: Herstelpunt selecteren** selecteert u een herstelpunt in de vervolgkeuzelijst.
1. In **Stap 2: Script downloaden om naar bestanden te zoeken en ze te herstellen** klikt u op de knop **Uitvoerbaar bestand downloaden**. Kopieer het wachtwoord voor het bestand en sla het op een veilige plek op.
1. Open **Verkenner** op uw lokale computer, navigeer naar uw map **Downloads** en kopieer het gedownloade .exe-bestand. De bestandsnaam wordt voorafgegaan door de naam van uw virtuele machine. 
1. Ga weer naar uw VM (via de RDP-verbinding) en plak het .exe-bestand op het bureaublad van uw VM. 
1. Navigeer naar het bureaublad van uw VM en dubbelklik op het .exe-bestand. Er wordt een opdrachtprompt gestart. Het programma koppelt het herstelpunt als een bestandsshare waartoe u toegang hebt. Wanneer de share is gemaakt, typt u **q** om de opdrachtprompt te sluiten.
1. Open **Verkenner** op uw VM en navigeer naar de stationsletter die voor de bestandsshare is gebruikt.
1. Navigeer naar \inetpub\wwwroot, kopieer **iisstart.png** uit de bestandsshare en plak het in \inetpub\wwwroot. U kopieert bijvoorbeeld F:\inetpub\wwwroot\iisstart.png en plakt het in c:\inetpub\wwwroot om het bestand te herstellen.
1. Open op de lokale computer het browsertabblad waardoor u verbonden bent met het IP-adres van de VM waarop de standaard IIS-webpagina wordt weergegeven. Druk op Ctrl+F5 om de browserpagina te vernieuwen. Nu ziet u dat de installatiekopie is hersteld.
1. Ga op de lokale computer terug naar het browsertabblad voor Azure Portal en klik in **Stap 3: De schijven ontkoppelen na het herstel** op de knop **Schijven ontkoppelen**. Als u deze stap vergeet uit te voeren, wordt de verbinding met het koppelpunt na 12 uur automatisch verbroken. Na deze 12 uur moet u een nieuw script downloaden voor het maken van een nieuw koppelpunt.





## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een back-up maken van een VM
> * Een dagelijkse back-up plannen
> * Een bestand herstellen vanaf een back-up

Ga naar de volgende zelfstudie voor meer informatie over het controleren van virtuele machines.

> [!div class="nextstepaction"]
> [Virtuele machines beheren](tutorial-govern-resources.md)
