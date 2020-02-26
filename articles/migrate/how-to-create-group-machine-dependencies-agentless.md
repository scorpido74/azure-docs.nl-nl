---
title: Visualisatie van de afhankelijkheid van agents instellen in Azure Migrate
description: Stel groepen in met behulp van een afhankelijkheids visualisatie zonder agents in Azure Migrate server beoordeling.
ms.topic: article
ms.date: 2/24/2020
ms.openlocfilehash: c9425ad1fa78f14a194d3fe13c259dadf4eb5eb6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589127"
---
# <a name="set-up-agentless-dependency-visualization"></a>Visualisatie van de afhankelijkheid van agents instellen 

In dit artikel wordt beschreven hoe u afhankelijkheids visualisatie instelt in Azure Migrate: Server Assessment. Met de [visualisatie van afhankelijkheden](concepts-dependency-visualization.md#what-is-dependency-visualization) kunt u afhankelijkheden identificeren en begrijpen tussen computers die u wilt beoordelen en migreren naar Azure.

Met de visualisatie van de afhankelijkheid van agents kunt u computer afhankelijkheden identificeren zonder agents op computers te installeren. Het werkt door de TCP-verbindings gegevens vast te leggen van de computers waarvoor deze is ingeschakeld.

> [!IMPORTANT]
> Visualisatie zonder agents is momenteel alleen beschikbaar als preview-versie van Azure VMware Vm's, gedetecteerd met het Azure Migrate: Server assessment tool.
> Functies zijn mogelijk beperkt of onvolledig.
> Deze preview wordt gedekt door de klant ondersteuning en kan worden gebruikt voor werk belastingen voor de productie.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="current-limitations"></a>Huidige beperkingen

- Nu kunt u een server niet toevoegen aan of verwijderen uit een groep in de weer gave afhankelijkheids analyse.
- Een afhankelijkheids toewijzing voor een groep servers is momenteel niet beschikbaar.
- De afhankelijkheids gegevens kunnen momenteel niet worden gedownload in de indeling tabellair.

## <a name="before-you-start"></a>Voordat u begint

- [Bekijk](concepts-dependency-visualization.md#agentless-visualization) de vereisten en kosten die zijn gekoppeld aan de visualisatie van de afhankelijkheid van agents.
- Bekijk de [ondersteunings vereisten](migrate-support-matrix-vmware.md#agentless-dependency-visualization) voor het instellen van de visualisatie van agentloze afhankelijkheden.
- Zorg ervoor dat u een Azure Migrate project hebt [gemaakt](how-to-add-tool-first-time.md) .
- Als u al een project hebt gemaakt, moet u ervoor zorgen dat u het Azure Migrate: Server Assessment Tool hebt [toegevoegd](how-to-assess.md) .
- Zorg ervoor dat u een [Azure migrate apparaat](migrate-appliance.md) hebt ingesteld om uw on-premises machines te detecteren. Meer informatie over het instellen van een apparaat voor [VMware](how-to-set-up-appliance-vmware.md) -vm's. Het apparaat detecteert on-premises machines en verstuurt meta gegevens en prestatie gegevens naar Azure Migrate: Server evaluatie.


## <a name="create-a-user-account-for-discovery"></a>Een gebruikers account maken voor detectie

Stel een gebruikers account in zodat de server evaluatie toegang kan krijgen tot de virtuele machine voor detectie. U kunt één gebruikers account opgeven.

- **Windows-vm's**: het gebruikers account moet een lokale of een domein beheerder zijn.
- **Linux-vm's**: de hoofd bevoegdheid is vereist voor het account. De gebruikers account vereist ook deze twee mogelijkheden op/bin/netstat-en/bin/ls-bestanden: CAP_DAC_READ_SEARCH en CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Het gebruikers account toevoegen aan het apparaat

Voeg het gebruikers account toe aan het apparaat.

1. Open de app voor het beheren van apparaten. 
2. Navigeer naar het deel venster **vCenter-gegevens opgeven** .
3. Klik in **toepassing en afhankelijkheden van Vm's detecteren**op **referenties toevoegen**
3. Kies het **besturings systeem**, geef een beschrijvende naam op voor het account en de **gebruikers naam**/**wacht woord**
6. Klik op **Opslaan**.
7. Klik op **opslaan en detectie starten**.

    ![VM-gebruikers account toevoegen](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Detectie van afhankelijkheid starten

Kies de computers waarop u detectie van afhankelijkheden wilt inschakelen.

1. Klik in **Azure migrate: Server evaluatie**op **gedetecteerde servers**.
2. Klik op het pictogram **afhankelijkheids analyse** .
3. Klik op **servers toevoegen**.
3. Kies op de pagina **servers toevoegen** het apparaat dat de relevante computers detecteert.
4. Selecteer de machines in de lijst met computers.
5. Klik op **servers toevoegen**.

    ![Detectie van afhankelijkheid starten](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

U kunt afhankelijkheden ongeveer zes uur na het starten van de detectie van afhankelijkheden visualiseren.

## <a name="visualize-dependencies"></a>Afhankelijkheden visualiseren

1. Klik in **Azure migrate: Server evaluatie**op **gedetecteerde servers**.
2. Zoek naar de computer die u wilt weer geven.
3. Klik in de kolom **afhankelijkheden** op **afhankelijkheden weer geven**
4. Wijzig de tijds periode waarvoor u de kaart wilt weer geven met de vervolg keuzelijst **tijd duur** .
5. Vouw de **client** groep uit om de machines met een afhankelijkheid op de geselecteerde computer weer te geven.
6. Vouw de **poort** groep uit om de computers weer te geven die een afhankelijkheid hebben van de geselecteerde computer.
7. Als u wilt navigeren naar de kaart weergave van een van de afhankelijke machines, klikt u op de computer naam > **Server toewijzing laden**

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
3. Klik op **servers verwijderen**.
3. Kies op de pagina **servers verwijderen** het **apparaat** dat de vm's detecteert waarop u de detectie van de afhankelijkheid wilt stoppen.
4. Selecteer de machines in de lijst met computers.
5. Klik op **servers verwijderen**.


## <a name="next-steps"></a>Volgende stappen

[Groepeert de machines](how-to-create-a-group.md) voor evaluatie.
