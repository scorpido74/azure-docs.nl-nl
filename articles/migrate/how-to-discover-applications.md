---
title: Ontdek apps, functies en onderdelen op on-premises servers met Azure Migrate
description: Meer informatie over het detecteren van apps, functies en onderdelen op on-premises servers met Azure Migrate server-evaluatie.
ms.topic: article
ms.date: 11/20/2019
ms.openlocfilehash: adc22925d1152639babe2377a1eae440e0ce418e
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029070"
---
# <a name="discover-machine-apps-roles-and-features"></a>Machine-apps,-functies en-functies detecteren

In dit artikel wordt beschreven hoe u toepassingen, functies en onderdelen detecteert op on-premises servers met behulp van Azure Migrate: Server evaluatie.

Het detecteren van de inventaris van apps en rollen/functies die op uw on-premises machines worden uitgevoerd, helpt u bij het identificeren en plannen van een migratie naar Azure dat is afgestemd op uw workloads.

> [!NOTE]
> App-detectie wordt momenteel alleen ondersteund voor virtuele VMware-machines en is beperkt tot detectie. We bieden nog geen evaluatie op basis van een app.  Momenteel wordt door server evaluatie op dit moment een overzicht van on-premises VMware-Vm's, virtuele Hyper-V-machines en fysieke servers op computer niveau geëvalueerd voor liften en verschuivingen.

App-detectie met Azure Migrate: Server evaluatie is zonder agent. Er hoeft niets te worden geïnstalleerd op machines en Vm's. Server Assessment gebruikt het Azure Migrate apparaat om detectie uit te voeren samen met de referenties van de computer gast. Het apparaat heeft op afstand toegang tot de VMware-machines met behulp van VMware-Api's.


## <a name="before-you-start"></a>Voordat u begint

1. Bekijk de [ondersteunings beperkingen](migrate-support-matrix-vmware.md#application-discovery) voor detectie op app-niveau.
2. Zorg ervoor dat u een Azure Migrate project hebt [gemaakt](how-to-add-tool-first-time.md) .
3. Als u al een project hebt gemaakt, moet u ervoor zorgen dat u het Azure Migrate: Server Assessment Tool hebt [toegevoegd](how-to-assess.md) .
4. Raadpleeg de [VMware-vereisten](migrate-support-matrix-vmware.md#vmware-requirements) voor het detecteren en beoordelen van virtuele VMware-machines met het Azure migrate apparaat.
4. Controleer de [vereisten](migrate-appliance.md) voor het implementeren van het Azure migrate apparaat.

## <a name="prepare-for-app-discovery"></a>App-detectie voorbereiden

1. [Bereid u voor op de implementatie van het apparaat](tutorial-prepare-vmware.md). Voor bereiding omvat het controleren van de instellingen van het apparaat en het instellen van een account dat door het apparaat wordt gebruikt voor toegang tot vCenter Server.
2. Zorg ervoor dat u een gebruikers account (één voor Windows-en Linux-servers) hebt met beheerders machtigingen voor computers waarop u apps, functies en onderdelen wilt detecteren.
3. [Implementeer het Azure migrate apparaat om de](how-to-set-up-appliance-vmware.md) detectie te starten. Als u het apparaat wilt implementeren, downloadt en importeert u een eicellen-sjabloon in VMware om het apparaat als een virtuele VMware-machine te maken. U configureert het apparaat en registreert het vervolgens met Azure Migrate.
2. Wanneer u het apparaat implementeert, moet u het volgende opgeven om de continue detectie te starten:
    - De naam van de vCenter Server waarmee u verbinding wilt maken.
    - Referenties die u hebt gemaakt voor het apparaat om verbinding te maken met vCenter Server.
    - De account referenties die u hebt gemaakt voor het apparaat om verbinding te maken met Vm's van Windows/Linux.

Nadat het apparaat is geïmplementeerd en u referenties hebt gegeven, start het apparaat doorlopende detectie van VM-meta gegevens en prestatie gegevens, samen met en detectie van apps, functies en rollen.  De duur van het detecteren van apps hangt af van het aantal Vm's dat u hebt. Het duurt doorgaans een uur voor app-detectie van 500 Vm's.

## <a name="review-and-export-the-inventory"></a>De inventaris controleren en exporteren

Nadat de detectie is voltooid, kunt u de app-inventaris in het Azure Portal bekijken en exporteren als u referenties hebt ingevoerd voor app-detectie.

1. Klik in **Azure migrate-Servers** > **Azure migrate: Server evaluatie**op de weer gegeven aantal om de pagina **gedetecteerde servers** te openen.

    > [!NOTE]
    > U kunt in deze fase ook optioneel afhankelijkheids toewijzing instellen voor gedetecteerde computers, zodat u afhankelijkheden kunt visualiseren tussen computers die u wilt beoordelen. [Meer informatie](how-to-create-group-machine-dependencies.md).

2. In **gedetecteerde toepassingen**klikt u op het aantal weer gegeven.
3. In de **toepassings inventaris**kunt u de gedetecteerde apps, rollen en functies bekijken.
4. Als u de inventaris wilt exporteren, klikt u op **app-inventaris exporteren**in **gedetecteerde servers**.

De app-inventarisatie wordt geëxporteerd en gedownload in Excel-indeling. Op de **inventarisatie** pagina van de toepassing worden alle apps weer gegeven die op alle computers zijn gedetecteerd.

## <a name="next-steps"></a>Volgende stappen

- [Een evaluatie maken](how-to-create-assessment.md) voor lift-en Shift-migratie van de gedetecteerde servers.
- Beoordeling van een SQL Server data bases met behulp van [Azure migrate: data base-evaluatie](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
