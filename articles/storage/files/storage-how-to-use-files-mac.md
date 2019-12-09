---
title: Een Azure-bestandsshare koppelen via SMB met macOS | Microsoft Docs
description: Informatie over het koppelen van een Azure-bestandsshare via SMB met macOS.
author: RenaShahMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 0e3420e469b117d90efb2949dab828021bfedcb6
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924712"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Een Azure-bestandsshare koppelen via SMB met macOS
[Azure Files ](storage-files-introduction.md) is het eenvoudig te gebruiken cloudbestandssysteem van Microsoft. Azure-bestandsshares kunnen aan het SMB 3-protocol (industrienorm) worden gekoppeld met macOS El Capitan 10.11+. Dit artikel behandelt twee verschillende manieren om een Azure-bestandsshare te koppelen op macOS: met de Finder-gebruikersinterface en met Terminal.

> [!Note]  
> We raden u aan om het tekenen van SMB-pakketten uit te schakelen voordat u een Azure-bestandsshare koppelt via SMB. Als u dit niet doet, zorgt dit mogelijk voor slechte prestaties bij het openen van de Azure-bestandsshare in macOS. De SMB-verbinding is versleuteld, dus dit heeft geen invloed op de beveiliging van de verbinding. Vanaf de terminal schakelen de volgende opdrachten het ondertekenen van SMB-pakketten uit, zoals beschreven door dit [ondersteuningsartikel van Apple over het uitschakelen van SMB-pakketondertekening](https://support.apple.com/HT205926):  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Vereisten voor het koppelen van een Azure-bestandsshare op macOS
* **Naam van het opslagaccount**: voor het koppelen van een Azure-bestandsshare hebt u de naam van het opslagaccount nodig.

* **Sleutel van het opslagaccount**: voor het koppelen van een Azure-bestandsshare hebt u de primaire (of secundaire) opslagsleutel nodig. SAS-sleutels worden momenteel niet ondersteund voor koppelen.

* **Zorg ervoor dat poort 445 is geopend**: SMB communiceert via TCP-poort 445. Controleer op de clientcomputer (Mac) of uw firewall TCP-poort 445 niet blokkeert.

## <a name="mount-an-azure-file-share-via-finder"></a>Een Azure-bestandsshare koppelen via Finder
1. **Open Finder**: Finder is standaard geopend op Mac OS, maar u kunt controleren of het de geselecteerde toepassing is door te klikken op het 'gezichtspictogram van Mac OS' op de dock:  
    ![Het gezichtspictogram van Mac OS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Selecteer ' verbinding maken met server ' in het menu ' go '** : gebruik het UNC-pad van de vereisten, converteer de eerste dubbele back slash (`\\`) naar `smb://` en alle andere backslashes (`\`) om slashes (`/`) door te sturen. De link moet er als volgt uitzien: ![het dialoogvenster 'Verbinden met server'](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Gebruik de naam en sleutel van het opslagaccount wanneer u wordt gevraagd om een gebruikersnaam en wachtwoord**: wanneer u klikt op 'Verbinden' in het dialoogvenster 'Verbinden met server', wordt u gevraagd om de gebruikersnaam en het wachtwoord (hier wordt uw macOS-gebruikersnaam automatisch ingevuld). U hebt de mogelijkheid om de naam/sleutel van het opslagaccount in uw macOS-sleutelhanger op te slaan.

4. **Gebruik de Azure-bestandsshare naar wens**: nadat u de sharenaam en de sleutel van het opslagaccount hebt gebruikt in plaats van de gebruikersnaam en het wachtwoord, wordt de share gekoppeld. U kunt deze gebruiken zoals u een lokale map/bestandsshare zou gebruiken. Zo kunt u bestanden naar de bestandsshare slepen en neerzetten:

    ![Een momentopname van een gekoppelde Azure-bestandsshare](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Een Azure-bestandsshare koppelen via Terminal
1. Vervang `<storage-account-name>` door de naam van uw opslagaccount. Geef de sleutel van het opslagaccount als wachtwoord wanneer hierom wordt gevraagd. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Gebruik de Azure-bestandsshare naar wens**: de Azure-bestandsshare wordt aan het koppelpunt dat is opgegeven door de vorige opdracht gekoppeld.  

    ![Een momentopname van de gekoppelde Azure-bestandsshare](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende koppelingen voor meer informatie over Azure Files.

* [Helpartikel van Apple: Verbinding maken met Bestandsdeling op een Mac](https://support.apple.com/HT204445)
* [Veelgestelde vragen](../storage-files-faq.md)
* [Problemen oplossen in Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Problemen oplossen in Linux](storage-troubleshoot-linux-file-connection-problems.md)    
