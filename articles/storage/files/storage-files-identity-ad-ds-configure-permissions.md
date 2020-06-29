---
title: 'Bepalen wat een gebruiker op het bestands niveau kan doen: Azure-bestands shares'
description: Meer informatie over het configureren van Windows Acl's-machtigingen voor on-premises AD DS-verificatie voor Azure-bestands shares. U kunt profiteren van nauw keurig toegangs beheer.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 38168db9706bd168b3edc2e740eaea40b23d4b0b
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/28/2020
ms.locfileid: "85510582"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>Deel drie: machtigingen voor mappen en bestands niveau configureren via SMB 

Voordat u aan dit artikel begint, moet u ervoor zorgen dat u het vorige artikel hebt voltooid, [machtigingen op share niveau toewijzen aan een identiteit](storage-files-identity-ad-ds-assign-permissions.md) om ervoor te zorgen dat uw machtigingen op share niveau aanwezig zijn.

Nadat u machtigingen op share niveau hebt toegewezen met RBAC, moet u de juiste Windows-Acl's configureren op basis van de hoofdmap, map of bestands niveau om te profiteren van nauw keurig toegangs beheer. U kunt de RBAC-machtigingen op share niveau beschouwen als de gate keeper op hoog niveau die bepaalt of een gebruiker toegang heeft tot de share. Hoewel de Windows-Acl's op een meer gedetailleerd niveau werken om te bepalen welke bewerkingen de gebruiker kan uitvoeren op het niveau van de map of het bestand. Machtigingen op share niveau en op het niveau van bestanden en mappen worden afgedwongen wanneer een gebruiker toegang probeert te krijgen tot een bestand of map, dus als er een verschil is tussen een van beide, wordt alleen de meest beperkende waarde toegepast. Als een gebruiker bijvoorbeeld lees-en schrijf toegang heeft op bestands niveau, maar alleen op share niveau leest, kan het bestand alleen worden gelezen. Hetzelfde geldt als het is omgekeerd, en een gebruiker had Lees-en schrijf toegang op het niveau van de share, maar alleen lezen op bestands niveau, maar kan het bestand nog steeds lezen.

## <a name="supported-permissions"></a>Ondersteunde machtigingen

Azure Files ondersteunt de volledige set basis-en geavanceerde Windows-Acl's. U kunt Windows-Acl's op mappen en bestanden in een Azure-bestands share weer geven en configureren door de share te koppelen en vervolgens met behulp van Windows Verkenner, de Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) -opdracht of de [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-acl) opdracht uit te voeren. 

Als u Acl's wilt configureren met machtigingen voor de super gebruiker, moet u de share koppelen met behulp van de sleutel van uw opslag account van de virtuele machine die lid is van het domein. Volg de instructies in de volgende sectie voor het koppelen van een Azure-bestands share vanaf de opdracht prompt en voor het configureren van Windows-Acl's.

De volgende machtigingen zijn opgenomen in de hoofdmap van een bestands share:

- BUILTIN\Administrators: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (i/o) (GR, GE)
- NT AUTHORITY\Authenticated-gebruikers: (OI) (CI) (M)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (F)
- MAKER EIGENAAR: (OI) (CI) (I/O) (F)

|Gebruikers|Definitie|
|---|---|
|BUILTIN\Administrators|Alle gebruikers die domein beheerders zijn van de on-premises AD DS omgeving.
|BUILTIN\Users|Ingebouwde beveiligings groep in AD. Het bevat standaard NT AUTHORITY\Authenticated-gebruikers. Voor een traditionele Bestands server kunt u de definitie van het lidmaatschap per server configureren. Voor Azure Files is er geen hosting server, dus BUILTIN\Users bevat dezelfde set gebruikers als NT AUTHORITY\Authenticated-gebruikers.|
|NT AUTHORITY\SYSTEM|Het service account van het besturings systeem van de bestands server. Dit service account is niet van toepassing in Azure Files context. De map is opgenomen in de hoofdmap om consistent te zijn met de Windows-Server ervaring voor hybride scenario's.|
|NT AUTHORITY\Authenticated-gebruikers|Alle gebruikers in AD die een geldig Kerberos-token kunnen verkrijgen.|
|MAKER EIGENAAR|Elk object van een map of bestand heeft een eigenaar voor dat object. Als er voor dat object Acl's zijn toegewezen aan de maker eigenaar, heeft de gebruiker die de eigenaar van dit object is, de machtigingen voor het object dat door de ACL is gedefinieerd.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>Een bestands share koppelen vanaf de opdracht prompt

Gebruik de Windows- `net use` opdracht om de Azure-bestands share te koppelen. Vergeet niet om de waarden van de tijdelijke aanduidingen in het volgende voor beeld te vervangen door uw eigen waarden. Zie [een Azure-bestands share gebruiken met Windows](storage-how-to-use-files-windows.md)voor meer informatie over het koppelen van bestands shares. 

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
```

Als u problemen ondervindt bij het maken van verbinding met Azure Files, raadpleegt u [het hulp programma voor probleem oplossing dat is gepubliceerd voor Azure files-montage fouten in Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5). We bieden ook [richt lijnen](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) voor het omzeilen van scenario's wanneer poort 445 wordt geblokkeerd. 

## <a name="configure-windows-acls"></a>Windows-Acl's configureren

Als de bestands share is gekoppeld aan de sleutel voor het opslag account, moet u de Windows-Acl's (ook wel NTFS-machtigingen genoemd) configureren. U kunt de Windows-Acl's configureren met behulp van Windows Verkenner of icacls.

Als u mappen of bestanden hebt in on-premises bestands servers met Windows-DACL'S die zijn geconfigureerd voor de AD DS identiteiten, kunt u deze kopiëren naar Azure Files persistentie van de Acl's met bestanden voor het traditionele Kopieer programma zoals Robocopy of [Azure AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases). Als uw mappen en bestanden worden getierd tot Azure Files via Azure File Sync, worden uw Acl's overgenomen en bewaard in de oorspronkelijke indeling.

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Windows-Acl's configureren met Windows-bestanden Verkenner

Gebruik Windows Verkenner om volledige machtigingen te verlenen aan alle mappen en bestanden onder de bestands share, met inbegrip van de hoofdmap.

1. Open Windows Verkenner, klik met de rechter muisknop op het bestand of de map en selecteer **Eigenschappen**.
1. Selecteer het tabblad **beveiliging** .
1. Selecteer **bewerken..** om machtigingen te wijzigen.
1. U kunt de machtigingen van bestaande gebruikers wijzigen of **toevoegen selecteren...** om machtigingen te verlenen aan nieuwe gebruikers.
1. Geef in het prompt venster voor het toevoegen van nieuwe gebruikers de doel gebruikersnaam op waaraan u machtigingen wilt verlenen in het vak **Geef de object namen op** en selecteer **Namen controleren** om de volledige UPN-naam van de doel gebruiker te zoeken.
1.    Selecteer **OK**.
1.    Selecteer op het tabblad **beveiliging** alle machtigingen die u wilt toewijzen aan de nieuwe gebruiker.
1.    Selecteer **Toepassen**.

### <a name="configure-windows-acls-with-icacls"></a>Windows-Acl's configureren met icacls

Gebruik de volgende Windows-opdracht om volledige machtigingen te verlenen aan alle mappen en bestanden onder de bestands share, met inbegrip van de hoofdmap. Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Zie voor meer informatie over het gebruik van icacls voor het instellen van Windows-Acl's en voor de verschillende typen ondersteunde machtigingen [de opdracht regel verwijzing voor icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="next-steps"></a>Volgende stappen

Nu de functie is ingeschakeld en geconfigureerd, gaat u verder met het volgende artikel, waar u uw Azure-bestands share koppelt van een virtuele machine die lid is van een domein.

[Deel vier: een bestands share koppelen vanaf een virtuele machine die lid is van een domein](storage-files-identity-ad-ds-mount-file-share.md)
