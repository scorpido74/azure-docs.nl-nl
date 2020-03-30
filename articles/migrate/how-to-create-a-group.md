---
title: Machines groeperen voor beoordeling met Azure Migrate | Microsoft Documenten
description: Beschrijft hoe u machines groepeert voordat u een beoordeling uitvoert met de Azure Migrate-service.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68301678"
---
# <a name="create-a-group-for-assessment"></a>Een groep maken voor beoordeling

In dit artikel wordt beschreven hoe u groepen machines maakt voor beoordeling met Azure Migrate: Server Assessment.

[Azure Migreren](migrate-services-overview.md) helpt u te migreren naar Azure. Azure Migrate biedt een gecentraliseerde hub om detectie, beoordeling en migratie van on-premises infrastructuur, toepassingen en gegevens naar Azure bij te houden. De hub biedt Azure-hulpprogramma's voor beoordeling en migratie, evenals isv-aanbiedingen (onafhankelijke softwareleveranciers) van derden. 

## <a name="grouping-machines"></a>Groepering sgroepering van machines

U verzamelt machines in groepen om te beoordelen of ze geschikt zijn voor migratie naar Azure en om Azure-schattingen en kostenschattingen voor hen te krijgen. Er zijn een paar manieren om groepen te maken:

- Als u weet welke machines samen moeten worden gemigreerd, u de groep handmatig maken in Azure Migrate.
- Als u niet zeker bent van de machines die moeten worden gegroepeerd, u de functionaliteit voor afhankelijkheidsvisualisatie in Azure Migreren gebruiken om groepen te maken. 

> [!NOTE]
> De functie voor visualisatie van afhankelijkheden is niet beschikbaar in Azure Government.

## <a name="create-a-group-manually"></a>Een groep handmatig maken

U een groep maken op hetzelfde moment dat u [een beoordeling maakt.](how-to-create-assessment.md)

Als u een groep handmatig wilt maken buiten het maken van een beoordeling, gaat u als volgt te werk:

1. Klik in het Azure Migrate-project > **Overzicht**op **Servers beoordelen en migreren**. Klik in **Azure Migrate: Serverbeoordeling**op **Groepen**
    - Als u het azure migrate: serverassessment-hulpprogramma nog niet hebt toegevoegd, klikt u om het toe te voegen. [Meer informatie](how-to-assess.md).
    - Als u nog geen Azure Migrate-project hebt gemaakt, [leest u meer](how-to-add-tool-first-time.md).

    ![Groepen selecteren](./media/how-to-create-a-group/select-groups.png)

2. Klik op het pictogram **Groeperen.**
3. Selecteer in **groep Maken**een groepsnaam en selecteer in **toestelnaam**het Azure Migrate-toestel dat u gebruikt voor machinedetectie.
1. Selecteer in de machinelijst de machines die u wilt toevoegen aan de groep > **Maken.**

    ![Groep maken](./media/how-to-create-a-group/create-group.png)

U deze groep nu gebruiken wanneer u [een beoordeling maakt.](how-to-create-assessment.md)

## <a name="refine-a-group-with-dependency-mapping"></a>Een groep verfijnen met afhankelijkheidstoewijzing

Met afhankelijkheidstoewijzing u afhankelijkheden op verschillende machines visualiseren. U gebruikt doorgaans afhankelijkheidstoewijzing wanneer u machinegroepen met hogere vertrouwensniveaus wilt beoordelen.
- Het helpt u om afhankelijkheden van machines te controleren voordat u een beoordeling uitvoert. 
- Het helpt ook om uw migratie naar Azure effectief te plannen, door ervoor te zorgen dat er niets achterblijft en zo verrassingsonderbrekingen tijdens migratie te voorkomen.
- U onderling afhankelijke systemen ontdekken die samen moeten migreren en bepalen of een lopend systeem gebruikers nog steeds bedient of een kandidaat is voor ontmanteling in plaats van migratie.

Als u [afhankelijkheidstoewijzing](how-to-create-group-machine-dependencies.md)al hebt ingesteld en een bestaande groep wilt verfijnen, gaat u als volgt te werk:

1. Klik op het tabblad **Servers** in **Azure Migreren:** tegel Serverbeoordeling op **Groepen**.
2. Klik op de groep die u wilt verfijnen.
    - Als u de afhankelijkheidstoewijzing nog niet hebt ingesteld, wordt in de kolom **Afhankelijkheden** de **installatiestatus Vereist** weergegeven. Klik op **Installatie vereist voor**elke virtuele machine waarvoor u afhankelijkheden wilt visualiseren. Installeer een paar agents op elke VM, voordat u de afhankelijkheden van de machine in kaart brengen. [Meer informatie](how-to-create-group-machine-dependencies.md).

        ![Afhankelijkheidstoewijzing toevoegen](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Als u al afhankelijkheidstoewijzing hebt ingesteld, klikt u op de groepspagina op **Afhankelijkheden weergeven** om de groepsafhankelijkheidskaart te openen.

3. Nadat u op **Afhankelijkheden weergeven**hebt geklikt, wordt in de groepsafhankelijkheidskaart het volgende weergegeven:

    - Binnenkomende (clients) en uitgaande (servers) TCP-verbindingen van en naar alle machines in de groep waarop de afhankelijkheidsagents zijn geïnstalleerd.
    - Afhankelijke machines die de afhankelijkheidsagents niet hebben geïnstalleerd, worden gegroepeerd op poortnummers.
    - Afhankelijke machines met geïnstalleerde afhankelijkheidsagents worden als afzonderlijke vakken weergegeven.
    - Processen die in de machine worden uitgevoerd. Vouw elke machinebox uit om de processen te bekijken.
    - Machine-eigenschappen (inclusief FQDN, besturingssysteem, MAC-adres). Klik op elk machinevak om de details te bekijken.

4. Als u afhankelijkheden wilt weergeven in een tijdsinterval van uw keuze, wijzigt u het tijdsbereik (standaard een uur) door begin- en einddatums of de duur op te geven.

    > [!NOTE]
    > Het tijdsbereik kan oplopen tot een uur. Als u een groter bereik nodig hebt, gebruikt u [Azure Monitor om afhankelijke gegevens](how-to-create-group-machine-dependencies.md) voor een langere periode op te vragen.

5. Nadat u de afhankelijkheden hebt geïdentificeerd waaraan u wilt toevoegen of uit de groep wilt verwijderen, u de groep wijzigen. Gebruik Ctrl+Klik om machines uit de groep toe te voegen of te verwijderen.

    - U alleen machines toevoegen die zijn ontdekt.
    - Het toevoegen en verwijderen van machines maakt eerdere beoordelingen voor een groep ongeldig.
    - U optioneel een nieuwe beoordeling maken wanneer u de groep wijzigt.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen en gebruiken van [afhankelijkheidstoewijzing](how-to-create-group-machine-dependencies.md) om groepen met een hoog vertrouwen te maken.

