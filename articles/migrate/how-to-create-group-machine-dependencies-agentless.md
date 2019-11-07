---
title: Machines in Azure Migrate groeperen met behulp van een afhankelijkheids visualisatie zonder agent
description: Hierin wordt beschreven hoe u groepen maakt met behulp van computer afhankelijkheden op een agentloze manier.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/23/2019
ms.author: hamusa
ms.openlocfilehash: e9f9e812d5463f0a503b100780f9b988e43f748d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720267"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>Visualisatie van de afhankelijkheid van agents instellen voor evaluatie

> [!NOTE]
> Als u deze functie nog niet ziet in de Azure Migrate Portal, loopt u vast. Deze wordt in de volgende week weer gegeven.

In dit artikel wordt beschreven hoe u afhankelijkheids toewijzing zonder agent instelt in Azure Migrate: Server evaluatie. Deze functie is momenteel beschikbaar als preview-versie voor VMware-machines die zijn gedetecteerd met een Azure Migrate apparaat. 

> [!IMPORTANT]
> Visualisatie van de afhankelijkheid van agents is momenteel beschikbaar als preview-versie voor Azure VMware-Vm's die zijn gedetecteerd met een Azure Migrate apparaat.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="about-dependency-mapping"></a>Over afhankelijkheids toewijzing 

Met afhankelijkheids toewijzing kunt u afhankelijkheden visualiseren tussen computers die u wilt beoordelen en migreren. U gebruikt meestal afhankelijkheids toewijzing als u computers wilt beoordelen met een hoger vertrouwens niveau.

- In Azure Migrate: Server analyse verzamelt u computers in groepen voor evaluatie. Groepen bestaan gewoonlijk uit machines die samen moeten worden gemigreerd en de afhankelijkheids toewijzing helpt u bij het controleren van afhankelijkheden van machines, zodat u machines nauw keurig kunt groeperen.
- Met toewijzing kunt u afhankelijke systemen detecteren die samen moeten worden gemigreerd. U kunt ook bepalen of een actief systeem nog steeds gebruikers is of een kandidaat is voor buiten gebruik stellen in plaats van migratie.
- Met het visualiseren van afhankelijkheden kunt u ervoor zorgen dat er geen onverwachte storingen optreden tijdens de migratie.

## <a name="about-agentless-visualization"></a>Over visualisatie zonder agents

Voor visualisatie zonder agent hoeft u geen agents op computers te installeren. Het werkt door de TCP-verbindings gegevens vast te leggen van de computers waarvoor deze is ingeschakeld.

- Nadat de detectie van afhankelijkheden is gestart, verzamelt het apparaat gegevens van machines met een polling-interval van vijf minuten.
- De volgende gegevens worden verzameld:
    - TCP-verbindingen
    - Namen van processen die actieve verbindingen hebben
    - Namen van geïnstalleerde toepassingen die de bovenstaande processen uitvoeren
    - Nee. van verbindingen die zijn gedetecteerd bij elk polling-interval

## <a name="current-limitations"></a>Huidige beperkingen

- Visualisatie zonder agent is momenteel alleen beschikbaar voor virtuele VMware-machines.
- Nu kunt u een server niet toevoegen aan of verwijderen uit een groep in de weer gave afhankelijkheids analyse. 
- Afhankelijkheids toewijzing voor een groep servers is momenteel niet beschikbaar.
- De afhankelijkheids gegevens kunnen momenteel niet worden gedownload in de indeling tabellair.

## <a name="before-you-start"></a>Voordat u begint

- Zorg ervoor dat u een Azure Migrate project hebt [gemaakt](how-to-add-tool-first-time.md) .
- De afhankelijkheids analyse zonder agent is momenteel alleen beschikbaar voor VMware-machines.
- Als u al een project hebt gemaakt, moet u ervoor zorgen dat u het Azure Migrate: Server Assessment Tool hebt [toegevoegd](how-to-assess.md) .
- Zorg ervoor dat u uw VMware-machines in Azure Migrate hebt gedetecteerd. u kunt dit doen door een Azure Migrate apparaat voor [VMware](how-to-set-up-appliance-vmware.md)in te stellen. Het apparaat detecteert on-premises machines en verstuurt meta gegevens en prestatie gegevens naar Azure Migrate: Server evaluatie. [Meer informatie](migrate-appliance.md).
- Zorg ervoor dat de virtuele VMware-machines worden ondersteund voor visualisatie van de afhankelijkheid van agents, zoals in de onderstaande tabel wordt beschreven.


### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
 
De volgende besturings systemen worden ondersteund voor visualisatie van agentloze afhankelijkheden.

**Type** | **Ondersteunde besturingssystemen**
--- | --- 
**Windows** | Micro soft Windows Server 2016 <br/> Micro soft Windows Server 2012 R2 <br/> Micro soft Windows Server 2012 <br/> Micro soft Windows Server 2008 R2 (64-bits) 
**Linux** | Red Hat Enterprise Linux 7, 6, 5 <br/> Ubuntu Linux 14,04, 16,04 <br/> Debian 7, 8 <br/> Oracle Linux 6, 7 <br/> CentOS 5, 6, 7  


## <a name="create-a-user-account-for-discovery"></a>Een gebruikers account maken voor detectie

Stel een gebruikers account in met de vereiste machtigingen zodat de server evaluatie toegang kan krijgen tot de virtuele machine voor detectie. U kunt één gebruikers account opgeven.

- **Vereiste machtiging voor Windows-vm's: voor**het gebruikers account is de toegang ' gast ' vereist.
- **Vereiste machtiging voor Linux-vm's**: de hoofd bevoegdheid is vereist voor het account. De gebruikers account vereist ook deze twee mogelijkheden op/bin/netstat-en/bin/ls-bestanden: CAP_DAC_READ_SEARCH en CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Het gebruikers account toevoegen aan het apparaat

U moet het gebruikers account toevoegen aan het apparaat.

Voeg het account als volgt toe:

1. Open de app voor het beheren van apparaten. Navigeer naar het deel venster **vCenter-gegevens opgeven** .
2. Klik in de sectie **toepassing en afhankelijkheden detecteren op vm's** op **referenties toevoegen** .
3. Kies het **besturings systeem**. 
4. Geef een beschrijvende naam op voor het account.
5. Geef de **gebruikers naam** en het **wacht woord** op
6. Klik op **Opslaan**.
7. Klik op **opslaan en detectie starten**.

    ![VM-gebruikers account toevoegen](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Detectie van afhankelijkheid starten

Kies de computers waarop u detectie van afhankelijkheden wilt inschakelen.

1. Klik in **Azure migrate: Server evaluatie**op **gedetecteerde servers**.
2. Klik op het pictogram **afhankelijkheids analyse** .
3. Klik op **detectie van afhankelijkheid starten**.
3. Kies op de pagina **detectie van afhankelijkheid starten** het apparaat dat de relevante computers detecteert.
4. Selecteer de machines in de lijst met computers.
5. Klik op **detectie van afhankelijkheid starten**.

    ![Detectie van afhankelijkheid starten](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

U kunt afhankelijkheden 6 uur na het starten van de detectie van afhankelijkheden visualiseren.

## <a name="visualize-dependencies"></a>Afhankelijkheden visualiseren

1. Klik in **Azure migrate: Server evaluatie**op **gedetecteerde servers**.
2. Zoek naar de machine waarvoor u de afhankelijkheids toewijzing wilt weer geven.
3. Klik op **afhankelijkheden weer geven** in de kolom **afhankelijkheden** .
4. Wijzig de tijds periode waarvoor u de kaart wilt weer geven met de vervolg keuzelijst **tijd duur** .
5. Vouw de **client** groep uit om de computers weer te geven die een afhankelijkheid hebben op de geselecteerde computer. 
6. Vouw de **poort** groep uit om de computers weer te geven die een afhankelijkheid hebben van de geselecteerde computer. 
7. Als u naar de kaart weergave van een van de afhankelijke computers wilt gaan, klikt u op de naam van de computer en vervolgens op **Server toewijzing laden**

    ![Server poort groep uitvouwen en server toewijzing laden](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Client groep uitvouwen ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Vouw de geselecteerde machine uit om details op proces niveau weer te geven voor elke afhankelijkheid.

    ![Server uitvouwen om processen weer te geven](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Proces informatie voor een afhankelijkheid is niet altijd beschikbaar. Als deze niet beschikbaar is, wordt de afhankelijkheid weer gegeven met het proces dat is gemarkeerd als ' onbekend proces '.

## <a name="stop-dependency-discovery"></a>Detectie van afhankelijkheid stoppen

Kies de computers waarop u de detectie van afhankelijkheden wilt stoppen.

1. Klik in **Azure migrate: Server evaluatie**op **gedetecteerde servers**.
2. Klik op het pictogram **afhankelijkheids analyse** .
3. Klik op **detectie van afhankelijkheid stoppen**.
3. Kies op de pagina **detectie van afhankelijkheid stoppen** het **apparaat** dat de vm's detecteert waarop u de detectie van de afhankelijkheid wilt stoppen.
4. Selecteer de machines in de lijst met computers.
5. Klik op **detectie van afhankelijkheid stoppen**


## <a name="next-steps"></a>Volgende stappen

[De machines groeperen](how-to-create-a-group.md)
