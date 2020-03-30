---
title: Een Azure Migrate-toestel instellen met een script
description: Meer informatie over het instellen van een Azure Migrate-toestel met een script
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337686"
---
# <a name="set-up-an-appliance-with-a-script"></a>Een toestel instellen met een script

In dit artikel wordt beschreven hoe u het [Azure Migrate-toestel](deploy-appliance.md) instelt met behulp van een PowerShell-installatiescript.

Het script biedt:
- Een alternatief voor het instellen van het toestel met behulp van een OVA-sjabloon, voor beoordeling en agentloze migratie van VMware VM's.
- Een alternatief voor het instellen van het toestel met behulp van een VHD-sjabloon, voor beoordeling en migratie van Hyper-V VM's.
- Voor de beoordeling van fysieke servers (of VM's die u als fysieke servers wilt migreren), is het script de enige methode voor het instellen van het toestel.

## <a name="prerequisites"></a>Vereisten

Het script stelt het Azure Migrate-toestel in op een bestaande fysieke machine of VM.

- De machine die als toestel fungeert, moet Windows Server 2016 draaien, met 32 GB geheugen, acht vCPU's, ongeveer 80 GB schijfopslag en een externe virtuele switch. Het vereist een statisch of dynamisch IP-adres en toegang tot het internet.
- Voordat u het toestel implementeert, controleert u gedetailleerde toestelvereisten voor [Vm's van VMware,](migrate-appliance.md#appliance---vmware) [Hyper-V VM's](migrate-appliance.md#appliance---hyper-v)en [fysieke servers](migrate-appliance.md#appliance---physical).
- Voer het script niet uit op een bestaand Azure Migrate-toestel.


## <a name="download-the-script"></a>Het script downloaden

1. Zoek de machine/VM die fungeert als het Azure Migrate-toestel.
2. Ga als volgt te werk op de machine:

    - Als u het toestel wilt gebruiken met VMware VM's of Hyper-V VM's, [downloadt u](https://go.microsoft.com/fwlink/?linkid=2105112) de map met ritsdie het installatiescript en de MSI's bevat.
    - Als u het toestel wilt gebruiken met fysieke servers, downloadt u het script van de Azure Migrate-portal, zoals beschreven in deze [zelfstudie.](tutorial-assess-physical.md#set-up-the-appliance)

## <a name="verify-file-security"></a>Bestandsbeveiliging verifiëren

Controleer of het zip-bestand veilig is, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Controleer of de gegenereerde hashwaarden overeenkomen met deze instellingen (voor de nieuwste toestelversie):

    **Algoritme** | **Hash-waarde**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cdd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae6be1b64e6de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>Het script uitvoeren

Dit is wat het script doet:

- Installeert agents en een webapplicatie.
- Installeert Windows-rollen, waaronder Windows Activation Service, IIS en PowerShell ISE.
- Hiermee wordt een IIS-herschrijfbare module gedownload en geïnstalleerd. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Werkt een registersleutel (HKLM) bij met permanente instellingen voor Azure Migrate.
- Hiermee maakt u als volgt logboek- en configuratiebestanden:
    - **Config-bestanden**: %ProgramData%\Microsoft Azure\Config
    - **Logboekbestanden**: %ProgramData%\Microsoft Azure\Logs

Het script uitvoeren:

1. Haal het ritsbestand naar een map op de machine die het toestel host.
2. Start PowerShell op de machine, met beheerdersbevoegdheden (verhoogde) bevoegdheden.
3. Wijzig de PowerShell-map in de map met de inhoud die uit het gedownloade zip-bestand is gehaald.
4. Voer het script **AzureMigrateInstaller.ps1** als volgt uit:

    - Voor VMware: 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - Voor Hyper-V:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - Voor fysieke servers:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. Nadat het script succesvol is uitgevoerd, wordt de webtoepassing van het toestel gestart, zodat u het toestel instellen. Als u problemen ondervindt, u de scriptlogboeken bekijken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

## <a name="next-steps"></a>Volgende stappen

Nadat u het toestel met het script hebt ingesteld, volgt u de volgende instructies:

- Stel het toestel in voor [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Stel het apparaat in voor [Hyper-V.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)
- Stel het toestel in voor [fysieke servers.](how-to-set-up-appliance-physical.md)