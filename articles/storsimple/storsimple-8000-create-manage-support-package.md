---
title: Maak een StorSimple 8000-serie ondersteuningspakket
description: Meer informatie over het maken, decoderen en bewerken van een ondersteuningspakket voor uw StorSimple 8000-serie apparaat.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 9ca033f6f786c0142261dafa31b93b71a8b3336a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277077"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Een ondersteuningspakket voor de StorSimple 8000-serie maken en beheren

## <a name="overview"></a>Overzicht

Een StorSimple-ondersteuningspakket is een gebruiksvriendelijk mechanisme dat alle relevante logboeken verzamelt om Microsoft Support te helpen bij het oplossen van problemen met storsimple-apparaten. De verzamelde logboeken worden versleuteld en gecomprimeerd.

Deze zelfstudie bevat stapsgewijze instructies voor het maken en beheren van het ondersteuningspakket voor uw StorSimple 8000-serieapparaat. Als u met een StorSimple Virtual Array werkt, gaat u naar [het genereren van een logboekpakket.](storsimple-ova-web-ui-admin.md#generate-a-log-package)

## <a name="create-a-support-package"></a>Een ondersteuningspakket maken

In sommige gevallen moet u het ondersteuningspakket handmatig maken via Windows PowerShell voor StorSimple. Bijvoorbeeld:

* Als u gevoelige informatie uit uw logboekbestanden moet verwijderen voordat u deze deelt met Microsoft Support.
* Als u problemen ondervindt bij het uploaden van het pakket als gevolg van verbindingsproblemen.

U uw handmatig gegenereerde ondersteuningspakket via e-mail delen met Microsoft Support. Voer de volgende stappen uit om een ondersteuningspakket te maken in Windows PowerShell voor StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Een ondersteuningspakket maken in Windows PowerShell voor StorSimple

1. Als u een Windows PowerShell-sessie wilt starten als beheerder op de externe computer die wordt gebruikt om verbinding te maken met uw StorSimple-apparaat, voert u de volgende opdracht in:
   
    `Start PowerShell`
2. Maak in de Windows PowerShell-sessie verbinding met de SSAdmin-console van uw apparaat:
   
   1. Voer in de opdrachtregel in:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. Voer in het dialoogvenster dat wordt geopend het wachtwoord van de apparaatbeheerder in. Het standaardwachtwoord is _Password1_.
     
      ![Dialoogvenster PowerShell-referenties](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Selecteer **OK**.
   4. Voer in de opdrachtregel in:
     
      `Enter-PSSession $MS`
3. Voer in de sessie die wordt geopend de juiste opdracht in.
   
   * Voer voor netwerkshares die met een wachtwoord zijn beveiligd:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       U wordt gevraagd om een wachtwoord en een versleutelingswachtwoord (omdat het ondersteuningspakket is versleuteld). Vervolgens wordt een ondersteuningspakket gemaakt in de standaardmap (apparaatnaam met huidige datum en tijd).
   * Voor aandelen die niet met een wachtwoord `-Credential` zijn beveiligd, hebt u de parameter niet nodig. Voer het volgende in:
     
       `Export-HcsSupportPackage`
     
       Het ondersteuningspakket wordt gemaakt voor beide controllers in de standaardmap. Het pakket is een versleuteld, gecomprimeerd bestand dat naar Microsoft Support kan worden verzonden voor het oplossen van problemen. Zie [Contact opnemen met Microsoft Support](storsimple-8000-contact-microsoft-support.md)voor meer informatie.

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>De cmdletparameters Export-HcsSupportPackage

U de volgende parameters gebruiken met de cmdlet Export-HcsSupportPackage.

| Parameter | Vereist/optioneel | Beschrijving |
| --- | --- | --- |
| `-Path` |Vereist |Gebruik om de locatie van de gedeelde netwerkmap waarin het ondersteuningspakket is geplaatst, op te geven. |
| `-EncryptionPassphrase` |Vereist |Gebruik om een wachtwoordzin te bieden om het ondersteuningspakket te versleutelen. |
| `-Credential` |Optioneel |Gebruiken om toegangsreferenties voor de gedeelde netwerkmap te leveren. |
| `-Force` |Optioneel |Hiermee wilt u de bevestigingsstap voor versleutelingswachtwoordzin overslaan. |
| `-PackageTag` |Optioneel |Met deze neigen een map op te geven onder *Pad* waarin het ondersteuningspakket is geplaatst. De standaardinstelling is [apparaatnaam]-[huidige datum en tijd:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Optioneel |Geef op als **cluster** (standaard) om een ondersteuningspakket voor beide controllers te maken. Als u alleen een pakket wilt maken voor de huidige controller, geeft u **Controller**op . |

## <a name="edit-a-support-package"></a>Een ondersteuningspakket bewerken

Nadat u een ondersteuningspakket hebt gegenereerd, moet u het pakket mogelijk bewerken om gevoelige informatie te verwijderen. Dit kunnen volumenamen, apparaat-IP-adressen en back-upnamen uit de logboekbestanden zijn.

> [!IMPORTANT]
> U alleen een ondersteuningspakket bewerken dat is gegenereerd via Windows PowerShell voor StorSimple. U een pakket dat is gemaakt in de Azure-portal met de StorSimple Device Manager-service niet bewerken.

Als u een ondersteuningspakket wilt bewerken voordat u het uploadt op de Microsoft Support-site, decodeert u eerst het ondersteuningspakket, bewerkt u de bestanden en versleutelt u het opnieuw. Voer de volgende stappen uit.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Een ondersteuningspakket bewerken in Windows PowerShell voor StorSimple

1. Een ondersteuningspakket genereren zoals eerder beschreven, in [Een ondersteuningspakket maken in Windows PowerShell voor StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Download het script](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokaal op uw client.
3. Importeer de Windows PowerShell-module. Geef het pad op naar de lokale map waarin u het script hebt gedownload. Voer het als u de module wilt importeren:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Alle bestanden zijn *.aes-bestanden* die worden gecomprimeerd en versleuteld. Voer het als nodig hebben om bestanden te decomprimeren en te decoderen:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Houd er rekening mee dat de werkelijke bestandsextensies nu worden weergegeven voor alle bestanden.
   
    ![Ondersteuningspakket bewerken](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Wanneer u wordt gevraagd om de wachtwoordzin voor versleuteling, voert u de wachtwoordzin in die u hebt gebruikt toen het ondersteuningspakket werd gemaakt.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Blader naar de map met de logboekbestanden. Omdat de logbestanden nu zijn gedecomprimeerd en gedecodeerd, hebben deze originele bestandsextensies. Wijzig deze bestanden om klantspecifieke informatie te verwijderen, zoals volumenamen en apparaat-IP-adressen, en sla de bestanden op.
7. Sluit de bestanden om ze te comprimeren met gzip en versleutel ze met AES-256. Dit is voor snelheid en veiligheid bij het overbrengen van het ondersteuningspakket via een netwerk. Voer het volgende in om bestanden te comprimeren en te versleutelen:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Ondersteuningspakket bewerken](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Geef desgevraagd een coderingswachtwoordvoor het gewijzigde ondersteuningspakket op.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Noteer de nieuwe wachtwoordzin, zodat u deze desgevraagd delen met Microsoft Support.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Voorbeeld: Bestanden bewerken in een ondersteuningspakket op een met een wachtwoord beveiligd aandeel

In het volgende voorbeeld ziet u hoe u een ondersteuningspakket decoderen, bewerken en opnieuw versleutelen.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [informatie die wordt verzameld in het ondersteuningspakket](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Meer informatie over het [gebruik van ondersteuningspakketten en apparaatlogboeken om problemen met de implementatie van uw apparaat op te lossen.](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting)
* Meer informatie over het [gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

