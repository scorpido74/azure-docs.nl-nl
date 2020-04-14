---
title: FSLogix-profielcontainer Azure Files Active Directory Domain Services - Azure
description: In dit artikel wordt beschreven hoe u een FSLogix-profielcontainer maakt met Azure Files en Azure Active Directory Domain Services.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dd01b950435fadb96a961b6bb1c6b28ff436907a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265770"
---
# <a name="create-an-fslogix-profile-container-with-azure-files"></a>Een FSLogix-profielcontainer maken met Azure-bestanden

In dit artikel ziet u hoe u een FSLogix-profielcontainer maakt met Azure Files en Azure Active Directory Domain Services (AD DS).

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al een Azure AD DS-exemplaar hebt ingesteld. Als u er nog geen hebt, volgt u de instructies in [Eerst een basisbeheerd domein maken](../active-directory-domain-services/tutorial-create-instance.md) en keer dan hier terug.

## <a name="add-azure-ad-ds-admins"></a>Azure AD DS-beheerders toevoegen

Als u extra beheerders wilt toevoegen, maakt u een nieuwe gebruiker en verleent u deze machtigingen.

Ga als u een beheerder toevoeg:

1. Selecteer **Azure Active Directory** op de zijbalk, selecteer Alle **gebruikers**en selecteer Vervolgens **Nieuwe gebruiker**.

2.  Voer de gebruikersgegevens in de velden in.

3. Selecteer **Groepen**in het deelvenster Azure Active Directory aan de linkerkant van het scherm .

4. Selecteer de groep **AAD DC-beheerders.**

5. Selecteer in het linkerdeelvenster De optie **Leden**en selecteer Vervolgens **Leden toevoegen** in het hoofdvenster. Hier ziet u een lijst met alle gebruikers die beschikbaar zijn in Azure AD. Selecteer de naam van het gebruikersprofiel dat u zojuist hebt gemaakt.

## <a name="set-up-an-azure-storage-account"></a>Een Azure Storage-account instellen

Nu is het tijd om Azure AD DS-verificatie in te schakelen via Het Blok (SMB) van serverberichten. 

Verificatie inschakelen:

1. Als u dat nog niet hebt gedaan, u een v2 Azure Storage-account voor algemene doeleinden instellen en implementeren door de instructies te volgen in [Een Azure Storage-account maken.](../storage/common/storage-account-create.md)

2. Zodra u klaar bent met het instellen van uw account, selecteert u **Ga naar resource**.

3. Selecteer **Configuratie** in het deelvenster aan de linkerkant van het scherm en schakel vervolgens **Azure Active Directory-verificatie in voor Azure Files** in het hoofdvenster. Selecteer **Opslaan** als u klaar bent.

4. Selecteer **Overzicht** in het deelvenster aan de linkerkant van het scherm en selecteer **Vervolgens Bestanden** in het hoofdvenster.

5. Selecteer **Bestandsdelen** en voer de **naam** en **het quotum** in in de velden die aan de rechterkant van het scherm worden weergegeven.

## <a name="assign-access-permissions-to-an-identity"></a>Toegangsmachtigingen toewijzen aan een identiteit

Andere gebruikers hebben toegangsmachtigingen nodig om toegang te krijgen tot uw bestandsshare. Hiervoor moet u elke gebruiker een rol toewijzen met de juiste toegangsmachtigingen.

Ga als u gebruikers toegangsmachtigingen toewijst:

1. Open vanuit de Azure-portal het bestandsaandeel dat u hebt gemaakt in [Een Azure Storage-account instellen.](#set-up-an-azure-storage-account)

2. Selecteer **Toegangsbeheer (IAM)**.

3. Selecteer **Een roltoewijzing toevoegen**.

4. Selecteer in het tabblad **Roltoewijzing toevoegen** de juiste ingebouwde rol in de rollijst. U moet op zijn minst **SMB Share Contributor voor het** account selecteren om de juiste machtigingen te krijgen.

5. Selecteer Azure Active **Directory-gebruiker, -groep of serviceprincipal**voor Toegang **toewijzen**tot , selecteer Azure Active Directory-gebruiker, -groep of serviceprincipal .

6. Selecteer een naam of e-mailadres voor de doel-Azure Active Directory-identiteit.

7. Selecteer **Opslaan**.

## <a name="get-the-storage-account-access-key"></a>De toegangssleutel voor het opslagaccount ophalen

Vervolgens moet je de toegangssleutel voor je opslagaccount krijgen.

Ga als eerste voor de toegangssleutel voor het opslagaccount:

1. Selecteer **opslagaccounts**op de zijbalk van de Azure-portal .

2. Selecteer in de lijst met opslagaccounts het account waarvoor u Azure AD DS hebt ingeschakeld en de aangepaste rollen hebt gemaakt in de bovenstaande stappen.

3. Selecteer **onder Instellingen**de optie **Toegangssleutels** en kopieer de sleutel van **toets1**.

4. Ga naar het tabblad **Virtuele machines** en zoek elke virtuele machine die deel gaat uitmaken van uw hostpool.

5. Selecteer de naam van de virtuele machine (VM) onder **Virtuele machines (adVM)** en selecteer **Verbinden**

    Hiermee wordt een RDP-bestand gedownload waarmee u zich aanmelden bij de VM met zijn eigen referenties.

    ![Een schermafbeelding van het RDP-tabblad van het venster Verbinding maken met virtuele machine.](media/rdp-tab.png)

6. Wanneer u zich hebt aangemeld bij de virtuele machine, voert u als beheerder een opdrachtprompt uit.

7. Voer de volgende opdracht uit:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - Vervang `<desired-drive-letter>` door een drive letter naar `y:`keuze (bijvoorbeeld).
    - Vervang alle `<storage-account-name>` exemplaren van de naam van het opslagaccount dat u eerder hebt opgegeven.
    - Vervang `<share-name>` de naam van het deel dat u eerder hebt gemaakt.
    - Vervang `<storage-account-key>` de opslagaccountsleutel van Azure.

    Bijvoorbeeld:  
  
     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Voer de volgende opdracht uit om de gebruiker volledige toegang te verlenen tot het azure-bestandenaandeel.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - Vervang `<mounted-drive-letter>` door de letter van het station dat de gebruiker moet gebruiken.
    - Vervang `<user-email>` door de UPN van de gebruiker die dit profiel zal gebruiken om toegang te krijgen tot de vm's van de sessiehost.

    Bijvoorbeeld:
     
     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>Een profielcontainer maken

Nu uw profielen klaar zijn om te gaan, maken we een FSLogix-profielcontainer.

Ga als lid van het FSLogix-profielcontainer als gevolg van het configureren van een FSLogix-profielcontainer:

1. Meld u aan bij de vm voor sessiehost die u aan het begin van dit artikel hebt geconfigureerd en [download en installeer vervolgens de FSLogix-agent.](/fslogix/install-ht/)

2. Rits het FSLogix-agentbestand dat u hebt gedownload open en ga naar **x64-releases** > **Releases**en open **vervolgens FSLogixAppsSetup.exe**.

3. Zodra de installateur lanceert, selecteer **ik ga akkoord met de licentie voorwaarden.** Geef indien van toepassing een nieuwe sleutel op.

4. Selecteer **Installeren**.

5. Open **Drive C**en ga vervolgens naar**FSLogix-apps** > **Apps** **programmabestanden** > om ervoor te zorgen dat de FSLogix-agent goed is geïnstalleerd.

     >[!NOTE]
     > Als er meerdere VM's in de hostgroep zijn, moet u stap 1 tot en met 5 voor elke vm herhalen.

6. **Voer registereditor** (RegEdit) uit als beheerder.

7. Navigeer naar **Computer** > **HKEY_LOCAL_MACHINE** > **software** > **FSLogix,** klik met de rechtermuisknop op **FSLogix,** selecteer **Nieuw**en selecteer Vervolgens **Toets .**

8. Een nieuwe sleutel met de naam **Profielen maken**.

9.  Klik met de rechtermuisknop op **Profielen,** selecteer **Nieuw**en selecteer vervolgens **DWORD-waarde (32-bits).** Geef de waarde **de waarde een naam en** stel de waarde **Gegevens** in op **1**.

    ![Een screenshot van de toetsen Profielen. Het REG_DWORD bestand wordt gemarkeerd en de waarde gegevens is ingesteld op 1.](media/dword-value.png)

10. Klik met de rechtermuisknop op **Profielen,** selecteer **Nieuw**en selecteer vervolgens **Waarde voor meerdere tekenreeksen**. Geef de waarde **VHDLocaties** een naam en stel `\\fsprofile.file.core.windows.net\share` de URI in voor het aandeel Azure-bestanden als de waarde Gegevens.

    ![Een schermafbeelding van de toetsen Profielen met het Bestand VHDLocaties. De waarde gegevens toont de URI voor het aandeel Azure-bestanden.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Gebruikers toewijzen aan een sessiehost

Nu moet u gebruikers toewijzen aan uw sessiehost.

Gebruikers toewijzen:

1. Voer Windows PowerShell uit als beheerder en voer vervolgens de volgende cmdlet uit om u aan te melden bij Windows Virtual Desktop met PowerShell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Wanneer u om referenties wordt gevraagd, voert u dezelfde gebruiker in die de rol TenantCreator, RDS-eigenaar of RDS-bijdrager heeft gekregen op de Windows Virtual Desktop-tenant.

2. Voer de volgende cmdlets uit om de gebruiker aan de externe bureaubladgroep toe te wijzen:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Net als de eerdere cmdlets, `<your-wvd-tenant>` `<wvd-pool>`zorg `<user-principal>` ervoor dat te vervangen, , en met de relevante waarden.

    Bijvoorbeeld:

     ```powershell
     $pool1 = "contoso"
     
     $tenant = "contoso"
     
     $appgroup = "Desktop Application Group"
     
     $user1 = "jane.doe@contoso.com"
     
     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Zorg ervoor dat je profiel werkt

Nu hoef je alleen nog maar te zorgen dat het profiel dat je hebt gemaakt bestaat en werkt zoals bedoeld.

Ga als volgt te werk om je profiel te verifiëren:

1. Open een browser en ga naar [de Windows Virtual Desktop-webclient](https://rdweb.wvd.microsoft.com/webclient/index.html).

2. Meld u aan met het gebruikersaccount dat is toegewezen aan de groep Extern bureaublad.

3. Zodra de gebruikerssessie is ingesteld, opent u de Azure-portal en meldt u zich aan met een beheerdersaccount.

4. Selecteer **opslagaccounts**op de zijbalk .

5. Selecteer het opslagaccount dat u hebt geconfigureerd als bestandsshare voor uw sessiehostgroep en ingeschakeld met Azure AD DS.

6. Selecteer het pictogram **Bestanden** en vouw vervolgens uw aandeel uit.

    Als alles correct is ingesteld, ziet u een **map** met een naam `<user SID>-<username>`die als volgt is opgemaakt: .

## <a name="next-steps"></a>Volgende stappen

Als u op zoek bent naar alternatieve manieren om FSLogix-profielcontainers te maken, raadpleegt u de volgende artikelen:

- [Maak een profielcontainer voor een hostgroep met behulp van een bestandsshare](create-host-pools-user-profile.md).
- [Een FSLogix-profielcontainer maken voor een hostgroep met Azure NetApp-bestanden](create-fslogix-profile-container.md)

Meer gedetailleerde informatie over concepten met betrekking tot FSlogix-containers voor Azure-bestanden vindt u in [FSLogix-profielcontainers en Azure-bestanden.](fslogix-containers-azure-files.md)
