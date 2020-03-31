---
title: Beveiligingsfuncties die hybride back-ups beschermen
description: Meer informatie over het gebruik van beveiligingsfuncties in Azure Backup om back-ups veiliger te maken
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 7213f26493a118c2cb32f8f9935b4954176b99a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586390"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Beveiligingsfuncties om hybride back-ups te beschermen die Azure Backup gebruiken

Zorgen over beveiligingsproblemen, zoals malware, ransomware en inbraak, nemen toe. Deze beveiligingsproblemen kunnen duur zijn, zowel in termen van geld als gegevens. Om dergelijke aanvallen te voorkomen, biedt Azure Backup nu beveiligingsfuncties om hybride back-ups te beschermen. In dit artikel wordt uitgelegd hoe u deze functies inschakelen en gebruiken met behulp van een Azure Recovery Services-agent en Azure Backup Server. Deze functies omvatten onder andere:

- **Preventie**. Er wordt een extra verificatielaag toegevoegd wanneer een kritieke bewerking zoals het wijzigen van een wachtwoordzin wordt uitgevoerd. Deze validatie is om ervoor te zorgen dat dergelijke bewerkingen alleen kunnen worden uitgevoerd door gebruikers die over geldige Azure-referenties beschikken.
- **Waarschuwen**. Er wordt een e-mailmelding naar de abonnementsbeheerder verzonden wanneer een kritieke bewerking wordt uitgevoerd, zoals het verwijderen van back-upgegevens. Deze e-mail zorgt ervoor dat de gebruiker snel op de hoogte wordt gesteld van dergelijke acties.
- **Herstel**. Verwijderde back-upgegevens worden 14 dagen na de datum van verwijdering bewaard. Dit zorgt voor herstelbaarheid van de gegevens binnen een bepaalde periode, dus er is geen gegevensverlies, zelfs als er een aanval plaatsvindt. Ook wordt een groter aantal minimale herstelpunten gehandhaafd om te beschermen tegen corrupte gegevens.

> [!NOTE]
> Beveiligingsfuncties mogen niet worden ingeschakeld als u een VM-back-up (Infrastructure as A Service) gebruikt. Deze functies zijn nog niet beschikbaar voor IaaS VM back-up, dus het inschakelen van hen zal geen invloed hebben. Beveiligingsfuncties mogen alleen worden ingeschakeld als u: <br/>
>  * **Azure Backup-agent**. Minimum agent versie 2.0.9052. Nadat u deze functies hebt ingeschakeld, moet u upgraden naar deze agentversie om kritieke bewerkingen uit te voeren. <br/>
>  * **Azure Backup Server**. Minimaal Azure Backup agent versie 2.0.9052 met Azure Backup Server update 1. <br/>
>  * **System Center Data Protection Manager**. Minimaal Azure Backup agent versie 2.0.9052 met Data Protection Manager 2012 R2 UR12 of Data Protection Manager 2016 UR2. <br/>


> [!NOTE]
> Deze functies zijn alleen beschikbaar voor de vault van Recovery Services. Alle nieuw gemaakte Vaults van Recovery Services hebben deze functies standaard ingeschakeld. Voor bestaande Vaults van Recovery Services schakelen gebruikers deze functies in met behulp van de stappen die in de volgende sectie worden genoemd. Nadat de functies zijn ingeschakeld, zijn ze van toepassing op alle herstelservices-agentcomputers, Azure Backup Server-exemplaren en Gegevensbeschermingsbeheer-servers die bij de kluis zijn geregistreerd. Het inschakelen van deze instelling is een eenmalige actie en u deze functies niet uitschakelen nadat u deze hebt inschakeld.
>

## <a name="enable-security-features"></a>Beveiligingsfuncties in te schakelen

Als u een vault voor Herstelservices maakt, u alle beveiligingsfuncties gebruiken. Als u met een bestaande kluis werkt, schakelt u beveiligingsfuncties in door de volgende stappen te volgen:

1. Meld u aan bij de Azure-portal met uw Azure-referenties.
2. Selecteer **Bladeren**en typ **Herstelservices**.

    ![Schermafbeelding van de optie Bladeren van Azure-portal](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    De lijst met Recovery Services-kluizen wordt weergegeven. Selecteer in deze lijst een kluis. Het geselecteerde kluisdashboard wordt geopend.
3. Klik in de lijst met items die onder de kluis worden weergegeven onder **Instellingen**op **Eigenschappen**.

    ![Schermafbeelding van de kluisopties van Recovery Services](./media/backup-azure-security-feature/vault-list-properties.png)
4. Klik **onder Beveiligingsinstellingen**op **Bijwerken**.

    ![Schermafbeelding van de kluiseigenschappen van Recovery Services](./media/backup-azure-security-feature/security-settings-update.png)

    De updatekoppeling opent het blade **beveiligingsinstellingen,** waarmee u een overzicht geeft van de functies en deze inschakelen.
5. Selecteer in de vervolgkeuzelijst **Azure Multi-Factor Authentication?** [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) Als dit is ingeschakeld, wordt u gevraagd om te verifiëren vanaf een ander apparaat (bijvoorbeeld een mobiele telefoon) terwijl u zich aanmeldt bij de Azure-portal.

   Wanneer u kritieke bewerkingen uitvoert in Back-up, moet u een beveiligingspincode invoeren die beschikbaar is op de Azure-portal. Als u Azure Multi-Factor Authentication inschakelt, wordt een beveiligingslaag toegevoegd. Alleen geautoriseerde gebruikers met geldige Azure-referenties en geverifieerd vanaf een tweede apparaat hebben toegang tot de Azure-portal.
6. Als u beveiligingsinstellingen wilt opslaan, selecteert u **Inschakelen** en klikt u op **Opslaan**. U **Alleen inschakelen** selecteren nadat u een waarde hebt geselecteerd in de lijst Azure **Multi-Factor Authentication?**

    ![Schermafbeelding van beveiligingsinstellingen](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Verwijderde back-upgegevens herstellen

Back-up behoudt verwijderde back-upgegevens gedurende nog eens 14 dagen en verwijdert deze niet onmiddellijk als de bewerking **Back-up stoppen met back-upgegevens verwijderen** wordt uitgevoerd. Als u deze gegevens in de periode van 14 dagen wilt herstellen, neemt u de volgende stappen, afhankelijk van wat u gebruikt:

Voor azure **Recovery Services-agentgebruikers:**

1. Als de computer waar back-ups plaatsvonden nog steeds beschikbaar is, moet u de verwijderde gegevensbronnen opnieuw beveiligen en de [gegevens herstellen naar dezelfde machine](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) in Azure Recovery Services gebruiken om te herstellen van alle oude herstelpunten.
2. Als deze computer niet beschikbaar is, gebruikt u [Herstellen naar een alternatieve machine](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) om een andere Azure Recovery Services-computer te gebruiken om deze gegevens op te halen.

Voor **Azure Backup Server-gebruikers:**

1. Als de server waar back-ups plaatsvonden nog steeds beschikbaar is, moet u de verwijderde gegevensbronnen opnieuw beveiligen en de functie **Gegevens herstellen** gebruiken om te herstellen van alle oude herstelpunten.
2. Als deze server niet beschikbaar is, gebruikt u [Gegevens herstellen van een andere Azure Backup Server](backup-azure-alternate-dpm-server.md) om een ander Azure Backup Server-exemplaar te gebruiken om deze gegevens op te halen.

Voor gebruikers **van Data Protection Manager:**

1. Als de server waar back-ups plaatsvonden nog steeds beschikbaar is, moet u de verwijderde gegevensbronnen opnieuw beveiligen en de functie **Gegevens herstellen** gebruiken om te herstellen van alle oude herstelpunten.
2. Als deze server niet beschikbaar is, gebruikt u [Externe DPM toevoegen](backup-azure-alternate-dpm-server.md) om een andere gegevensbeschermingsbeheerserver te gebruiken om deze gegevens op te halen.

## <a name="prevent-attacks"></a>Aanvallen voorkomen

Er zijn controles toegevoegd om ervoor te zorgen dat alleen geldige gebruikers verschillende bewerkingen kunnen uitvoeren. Deze omvatten het toevoegen van een extra laag van authenticatie, en het handhaven van een minimale bewaarbereik voor hersteldoeleinden.

### <a name="authentication-to-perform-critical-operations"></a>Verificatie om kritieke bewerkingen uit te voeren

Als onderdeel van het toevoegen van een extra verificatielaag voor kritieke bewerkingen wordt u gevraagd een beveiligingspincode in te voeren wanneer u **Stopbeveiliging** uitvoert met Gegevens verwijderen en **Wachtwoordzinbewerkingen wijzigen.**

> [!NOTE]
>
> Momenteel wordt de beveiligingspin niet ondersteund voor **Stop Protection met Delete-gegevens** voor DPM en MABS.

Ga als volgt te werk om deze pincode te ontvangen:

1. Meld u aan bij Azure Portal.
2. Blader naar**eigenschappen** **van de kluisinstellingen** > **Settings** > van Recovery Services .
3. Klik **onder Beveiligingspincode**op **Genereren**. Hiermee wordt een blade geopend dat de pincode bevat die moet worden ingevoerd in de gebruikersinterface van de Azure Recovery Services-agent.
    Deze pincode is slechts vijf minuten geldig en wordt na die periode automatisch gegenereerd.

### <a name="maintain-a-minimum-retention-range"></a>Een minimum bewaarbereik behouden

Om ervoor te zorgen dat er altijd een geldig aantal herstelpunten beschikbaar is, zijn de volgende controles toegevoegd:

- Voor dagelijkse retentie, een minimum van **zeven** dagen van retentie moet worden gedaan.
- Voor wekelijkse retentie moet minimaal **vier** weken retentie worden gedaan.
- Voor maandelijkse retentie moet minimaal **drie** maanden retentie worden gedaan.
- Voor de jaarlijkse retentie moet minimaal **één** jaar retentie worden gedaan.

## <a name="notifications-for-critical-operations"></a>Meldingen voor kritieke bewerkingen

Wanneer een kritieke bewerking wordt uitgevoerd, ontvangt de abonnementsbeheerder doorgaans een e-mailmelding met details over de bewerking. U extra e-mailontvangers voor deze meldingen configureren met behulp van de Azure-portal.

De beveiligingsfuncties die in dit artikel worden genoemd, bieden verdedigingsmechanismen tegen gerichte aanvallen. Wat nog belangrijker is, als een aanval gebeurt, deze functies geven u de mogelijkheid om uw gegevens te herstellen.

## <a name="troubleshooting-errors"></a>Fouten oplossen

| Bewerking | Foutdetails | Oplossing |
| --- | --- | --- |
| Beleidswijziging |Het back-upbeleid kan niet worden gewijzigd. Fout: De huidige bewerking is mislukt als gevolg van een interne servicefout [0x29834]. Probeer de operatie na een keer opnieuw. Neem contact op met Microsoft Ondersteuning als het probleem zich blijft voordoen. |**Oorzaak:**<br/>Deze fout komt wanneer beveiligingsinstellingen zijn ingeschakeld, u probeert het bewaarbereik te verlagen tot onder de hierboven opgegeven minimumwaarden en u op niet-ondersteunde versie staat (ondersteunde versies worden opgegeven in de eerste notitie van dit artikel). <br/>**Aanbevolen actie:**<br/> In dit geval moet u de bewaartermijn boven de opgegeven minimale bewaartermijn (zeven dagen voor dagelijks, vier weken voor wekelijks, drie weken voor maandelijks of één jaar voor jaarlijks) instellen om verder te gaan met beleidsgerelateerde updates. Optioneel is de voorkeursbenadering het bijwerken van back-upagent, Azure Backup Server en/of DPM UR om alle beveiligingsupdates te gebruiken. |
| Wachtwoordzin wijzigen |Ingevoerde beveiligingspincode is onjuist. (ID: 100130) Geef de juiste beveiligingspincode op om deze bewerking te voltooien. |**Oorzaak:**<br/> Deze fout wordt geleverd wanneer u een ongeldige of verlopen beveiligingspincode invoert tijdens het uitvoeren van kritieke bewerkingen (zoals wachtwoordzin wijzigen). <br/>**Aanbevolen actie:**<br/> Als u de bewerking wilt voltooien, moet u een geldige beveiligingspincode invoeren. Als u de pincode wilt opvragen, meldt u zich aan bij de Azure-portal en navigeert u naar de kluis Recovery Services > Instellingen > Eigenschappen > Beveiligingspincode genereren. Gebruik deze pincode om de wachtwoordzin te wijzigen. |
| Wachtwoordzin wijzigen |De bewerking is mislukt. ID: 120002 |**Oorzaak:**<br/>Deze fout komt wanneer beveiligingsinstellingen zijn ingeschakeld, u probeert de wachtwoordzin te wijzigen en u bent op niet-ondersteunde versie (geldige versies opgegeven in de eerste noot van dit artikel).<br/>**Aanbevolen actie:**<br/> Als u de wachtwoordzin wilt wijzigen, moet u eerst een update-back-upagent bijwerken naar minimaal versie-minimum 2.0.9052, Azure Backup server naar minimale update 1 en/of DPM naar minimaal DPM 2012 R2 UR12 of DPM 2016 UR2 (downloadkoppelingen hieronder), en voer vervolgens geldige beveiligingspincode in. Als u de pincode wilt opvragen, meldt u zich aan bij de Azure-portal en navigeert u naar de kluis Recovery Services > Instellingen > Eigenschappen > Beveiligingspincode genereren. Gebruik deze pincode om de wachtwoordzin te wijzigen. |

## <a name="next-steps"></a>Volgende stappen

- [Ga aan de slag met de kluis Azure Recovery Services](backup-azure-vms-first-look-arm.md) om deze functies in te schakelen.
- [Download de nieuwste Azure Recovery Services-agent](https://aka.ms/azurebackup_agent) om Windows-computers te beschermen en uw back-upgegevens te beschermen tegen aanvallen.
- [Download de nieuwste Azure Backup Server](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) om workloads te beschermen en uw back-upgegevens te beschermen tegen aanvallen.
- [Download UR12 voor System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) of [download UR2 voor System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) om workloads te beschermen en uw back-upgegevens te beschermen tegen aanvallen.
