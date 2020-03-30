---
title: De DPM-server voorbereiden om back-ups te maken
description: In dit artikel leest u hoe u zich voorbereiden op DPM-back-ups (System Center Data Protection Manager) naar Azure met behulp van de Azure Backup-service.
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 2119d46ca6102286ca879777058a49938b501ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273460"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Voorbereiden op een back-up van workloads naar Azure met System Center DPM

In dit artikel wordt uitgelegd hoe u zich voorbereiden op DPM-back-ups (System Center Data Protection Manager) naar Azure met behulp van de Azure Backup-service.

Het artikel biedt:

- Een overzicht van het implementeren van DPM met Azure Backup.
- Vereisten en beperkingen voor het gebruik van Azure Backup met DPM.
- Stappen voor het voorbereiden van Azure, waaronder het instellen van een herstelserviceback-upkluis en het optioneel wijzigen van het type Azure-opslag voor de kluis.
- Stappen voor het voorbereiden van de DPM-server, waaronder het downloaden van vault credentials, het installeren van de Azure Backup-agent en het registreren van de DPM-server in de kluis.
- Tips voor het oplossen van problemen voor veelvoorkomende fouten.

## <a name="why-back-up-dpm-to-azure"></a>Waarom een back-up maken van DPM naar Azure?

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) maakt een back-up van bestands- en toepassingsgegevens. DPM werkt als volgt samen met Azure Backup:

- **DPM wordt uitgevoerd op een fysieke server of on-premises VM** : u een back-up maken van gegevens naar een back-upkluis in Azure, naast schijf- en tapeback-ups.
- **DPM die wordt uitgevoerd op een Azure VM** — Vanuit System Center 2012 R2 met Update 3 of hoger u DPM implementeren op een Azure VM. U een back-up maken van gegevens naar Azure-schijven die aan de vm zijn gekoppeld, of Azure Backup gebruiken om een back-up van de gegevens te maken naar een back-upkluis.

De zakelijke voordelen van het maken van back-ups van DPM-servers naar Azure zijn:

- Voor on-premises DPM biedt Azure Backup een alternatief voor langdurige implementatie naar tape.
- Azure Backup u met Azure Backup opslag van de Azure-schijf uitvoeren. Als u oudere gegevens opslaat in een backup-kluis, u uw bedrijf opschalen door nieuwe gegevens op schijf op te slaan.

## <a name="prerequisites-and-limitations"></a>Vereisten en beperkingen

**Instelling** | **Vereiste**
--- | ---
DPM op een Azure VM | System Center 2012 R2 met DPM 2012 R2 Update Rollup 3 of hoger.
DPM op een fysieke server | System Center 2012 SP1 of hoger; Systeemcentrum 2012 R2.
DPM op een Hyper-V VM | System Center 2012 SP1 of hoger; Systeemcentrum 2012 R2.
DPM op een VMware VM | System Center 2012 R2 met Update Rollup 5 of hoger.
Onderdelen | De DPM-server moet Windows PowerShell en .NET Framework 4.5 hebben geïnstalleerd.
Ondersteunde apps | [Ontdek](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) waar DPM een back-up van kan maken.
Ondersteunde bestandstypen | Van deze bestandstypen kan een back-up worden gemaakt met Azure Backup: Encrypted (alleen volledige back-ups); Gecomprimeerd (incrementele back-ups ondersteund); Schaarse (incrementele back-ups ondersteund); Gecomprimeerd en schaars (behandeld als schaars).
Niet-ondersteunde bestandstypen | Servers op hoofdlettergevoelige bestandssystemen; harde links (overgeslagen); reparse punten (overgeslagen); versleuteld en gecomprimeerd (overgeslagen); versleuteld en schaars (overgeslagen); Gecomprimeerde stroom; parse stream.
Lokale opslag | Elke machine die u een back-up wilt maken, moet lokale gratis opslag hebben die ten minste 5% van de grootte van de gegevens is waarop een back-up wordt gemaakt. Voor het maken van een back-up van 100 GB aan gegevens is bijvoorbeeld minimaal 5 GB vrije ruimte op de scratch-locatie vereist.
Kluisopslag | Er is geen limiet aan de hoeveelheid gegevens die u back-ups maken met een Azure Backup-kluis, maar de grootte van een gegevensbron (bijvoorbeeld een virtuele machine of database) mag niet hoger zijn dan 54.400 GB.
Azure ExpressRoute | Als Azure ExpressRoute is geconfigureerd met Privé- of Microsoft-peering, kan deze niet worden gebruikt om een back-up van de gegevens naar Azure te maken.<br/><br/> Als Azure ExpressRoute is geconfigureerd met Public Peering, kan deze worden gebruikt om een back-up van de gegevens naar Azure te maken.<br/><br/> **Let op:** Public Peering is afgeschaft voor nieuwe circuits.
Azure Backup-agent | Als DPM wordt uitgevoerd op System Center 2012 SP1, installeert u Rollup 2 of hoger voor DPM SP1. Dit is vereist voor de installatie van de agent.<br/><br/> In dit artikel wordt beschreven hoe u de nieuwste versie van de Azure Backup-agent implementeert, ook wel bekend als de MARS-agent (Microsoft Azure Recovery Service). Als u een eerdere versie hebt geïmplementeerd, werkt u bij naar de nieuwste versie om ervoor te zorgen dat back-ups werken zoals verwacht.

Voordat u begint, hebt u een Azure-account nodig met de Azure Backup-functie ingeschakeld. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Lees meer over [azure backup-prijzen](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Opslaginstellingen wijzigen

U kiezen tussen georedundante opslag en lokaal redundante opslag.

- Uw kluis heeft standaard geografisch redundante opslag.
- Als de kluis uw primaire back-up is, laat u de optie in op georedundante opslag. Als u een goedkopere optie wilt die niet zo duurzaam is, gebruikt u de volgende procedure om lokaal redundante opslag te configureren.
- Meer informatie over [Azure-opslag](../storage/common/storage-redundancy.md)en de [georedundante](../storage/common/storage-redundancy-grs.md) en [lokaal redundante](../storage/common/storage-redundancy-lrs.md) opslagopties.
- Wijzig de opslaginstellingen vóór de eerste back-up. Als u al een back-up van een item hebt gemaakt, hoeft u geen back-up smaken in de kluis voordat u de opslaginstellingen wijzigt.

De instelling voor opslagreplicatie bewerken:

1. Open het dashboard van de kluis.

2. Klik **in Beheren**op **Back-upinfrastructuur**.

3. Selecteer in het menu **Back-upconfiguratie** een opslagoptie voor de kluis.

    ![Lijst met back-upkluizen](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Kluisreferenties downloaden

U gebruikt vault credentials wanneer u de DPM-server registreert in de kluis.

- Het kluisreferentiebestand is een certificaat dat de portal voor elke back-upkluis genereert.
- De portal uploadt de openbare sleutel vervolgens naar de Access Control Service (ACS).
- Tijdens de machineregistratieworkflow wordt de privésleutel van het certificaat beschikbaar gesteld aan de gebruiker, die de machine verifieert.
- Op basis van de verificatie stuurt de Azure Backup-service gegevens naar de geïdentificeerde kluis.

### <a name="best-practices-for-vault-credentials"></a>Aanbevolen procedures voor vault credentials

Download het kluisreferentiebestand via een beveiligd kanaal van de Azure-portal om de referenties te verkrijgen:

- De vault credentials worden alleen gebruikt tijdens de registratieworkflow.
- Het is uw verantwoordelijkheid om ervoor te zorgen dat het kluisbestand veilig is en niet in het gedrang komt.
  - Als de controle over de referenties verloren gaat, kunnen de vault credentials worden gebruikt om andere machines te registreren in kluis.
  - Back-upgegevens worden echter versleuteld met behulp van een wachtwoordzin die van de klant is, zodat bestaande back-upgegevens niet kunnen worden aangetast.
- Controleer of het bestand is opgeslagen op een locatie die toegankelijk is vanaf de DPM-server. Als het is opgeslagen in een bestandsshare/SMB, controleert u op de toegangsmachtigingen.
- Vault-referenties verlopen na 48 uur. U nieuwe vault referenties zo vaak als nodig downloaden. Echter, alleen de nieuwste vault credential file kan worden gebruikt tijdens de registratie workflow.
- De Azure Backup-service is niet op de hoogte van de privésleutel van het certificaat en de privésleutel is niet beschikbaar in de portal of de service.

Download het bestand met vaultcredentials als volgt naar een lokale machine:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Open de kluis waarin u de DPM-server wilt registreren.
3. Klik **in Instellingen**op **Eigenschappen**.

    ![Het menu Kluis openen](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Klik in **Eigenschappen** > **Back-upreferenties**op **Downloaden**. De portal genereert het kluisreferentiebestand met behulp van een combinatie van de kluisnaam en de huidige datum en maakt het beschikbaar om te downloaden.

    ![Download](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Klik **op Opslaan** om de vaultcredentials naar map te downloaden of als op te **slaan** en geef een locatie op. Het duurt maximaal een minuut voordat het bestand is gegenereerd.

## <a name="install-the-backup-agent"></a>De back-upagent installeren

Elke machine waarvan een back-up wordt gemaakt door Azure Backup moet de back-upagent (ook wel de Microsoft Azure Recovery Service (MARS)-agent genoemd hebben. Installeer de agent als volgt op de DPM-server:

1. Open de kluis waarop u de DPM-server wilt registreren.
2. Klik **in Instellingen**op **Eigenschappen**.

    ![Het menu Kluis openen](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Download op de pagina **Eigenschappen** de Azure Backup Agent.

    ![Download](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Voer MARSAgentInstaller.exe uit na het downloaden. om de agent op de DPM-machine te installeren.
5. Selecteer een installatiemap en cachemap voor de agent. De ruimte voor de cachelocatie moet ten minste 5% van de back-upgegevens bedragen.
6. Als u een proxyserver gebruikt om verbinding te maken met internet, voert u in het **scherm Proxy-configuratie** de gegevens van de proxyserver in. Als u een geverifieerde proxy gebruikt, voert u de gebruikersnaam en wachtwoordgegevens in dit scherm in.
7. De Azure Backup-agent installeert .NET Framework 4.5 en Windows PowerShell (als ze niet zijn geïnstalleerd) om de installatie te voltooien.
8. Nadat de agent is geïnstalleerd, **sluit u** het venster.

    ![Sluiten](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>De DPM-server registreren in de kluis

1. Klik in de console DPM-beheerder > **Beheer**op **Online**. Selecteer **Registreren**. De wizard Server registreren wordt geopend.
2. Geef in **proxyconfiguratie**de proxy-instellingen op zoals vereist.

    ![Proxyconfiguratie](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. Blader in **Backup Vault**naar en selecteer het kluisgegevensbestand dat u hebt gedownload.

    ![Vault-referenties](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. In **Throttling-instelling**u optioneel bandbreedtebeperking inschakelen voor back-ups. U de snelheidslimieten instellen voor het opgeven van werkuren en dagen.

    ![Instelling voor beperking](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. Geef in **Herstelmapinstelling**een locatie op die kan worden gebruikt tijdens gegevensherstel.

    - Azure Backup gebruikt deze locatie als tijdelijke bewaarruimte voor herstelde gegevens.
    - Na het voltooien van het gegevensherstel zal Azure Backup de gegevens in dit gebied opschonen.
    - De locatie moet voldoende ruimte hebben om items vast te houden waarvan u verwacht dat ze parallel herstellen.

    ![Instelling herstelmap](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. **Genereer of**geef in Versleuteling een wachtwoordzin op.

    - De wachtwoordzin wordt gebruikt om de back-ups te versleutelen naar de cloud.
    - Geef minimaal 16 tekens op.
    - Sla het bestand op een veilige locatie op, het is nodig voor herstel.

    ![Versleuteling](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > U bent eigenaar van de coderingswachtwoordzin en Microsoft heeft er geen inzicht in.
    > Als de wachtwoordzin verloren of vergeten is; Microsoft kan niet helpen bij het herstellen van de back-upgegevens.

7. Klik **op Registreren** om de DPM-server te registreren in de kluis.

Nadat de server is geregistreerd bij de kluis en u nu klaar bent om een back-up te maken naar Microsoft Azure. U moet de beveiligingsgroep in de DPM-console configureren om back-upworkloads naar Azure te maken. [Meer informatie over het](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019) implementeren van beveiligingsgroepen.

## <a name="troubleshoot-vault-credentials"></a>Vault-referenties oplossen

### <a name="expiration-error"></a>Vervaldatumfout

Het bestand met vault credentials is slechts 48 uur geldig (nadat het is gedownload van de portal). Als er een fout optreedt in dit scherm (bijvoorbeeld 'Het meegeleverde bestand met Vault-referenties is verlopen'), log t.a.v. in bij de Azure-portal en download het kluisgegevensbestand opnieuw.

### <a name="access-error"></a>Toegangsfout

Controleer of het bestand met vaultcredentials beschikbaar is op een locatie die toegankelijk is voor de installatietoepassing. Als u toegangsgerelateerde fouten tegenkomt, kopieert u het bestand met vaultcredentials naar een tijdelijke locatie in deze machine en probeert u de bewerking opnieuw.

### <a name="invalid-credentials-error"></a>Fout in ongeldige referenties

Als u een fout van de ongeldige vaultcredential er ondervindt (bijvoorbeeld 'Ongeldige vault credentials provided') is het bestand beschadigd of zijn de nieuwste referenties niet gekoppeld aan de herstelservice.

- Probeer de bewerking opnieuw na het downloaden van een nieuw kluisreferentiebestand van de portal.
- Deze fout wordt meestal gezien wanneer u op de optie **Kluisreferentie downloaden** in de Azure-portal klikt, twee keer snel achter elkaar. In dit geval is alleen het tweede kluisreferentiebestand geldig.
