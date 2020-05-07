---
title: Ontdek apps, functies en onderdelen op on-premises servers met Azure Migrate
description: Meer informatie over het detecteren van apps, functies en onderdelen op on-premises servers met Azure Migrate server-evaluatie.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: ff9f5489b513cd1405e6b093d7537e4cbcead041
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744619"
---
# <a name="discover-machine-apps-roles-and-features"></a>Machine-apps,-functies en-functies detecteren

In dit artikel wordt beschreven hoe u toepassingen, functies en onderdelen detecteert op on-premises servers met behulp van Azure Migrate: Server evaluatie.

Het detecteren van de inventaris van apps en rollen/functies die op uw on-premises machines worden uitgevoerd, helpt u bij het identificeren en plannen van een migratie naar Azure dat is afgestemd op uw workloads.

> [!NOTE]
> App-detectie is momenteel alleen beschikbaar als Preview voor VMware-Vm's en is beperkt tot alleen detectie. We bieden nog geen evaluatie op basis van een app. Evaluatie op basis van een machine voor on-premises virtuele VMware-machines, Hyper-V-Vm's en fysieke servers.

App-detectie met Azure Migrate: Server evaluatie is zonder agent. Er is niets geïnstalleerd op machines en Vm's. Server Assessment gebruikt het Azure Migrate apparaat om detectie uit te voeren samen met de referenties van de computer gast. Het apparaat heeft op afstand toegang tot de VMware-machines met behulp van VMware-Api's.


## <a name="before-you-start"></a>Voordat u begint

1. Zorg ervoor dat u een Azure Migrate project hebt [gemaakt](how-to-add-tool-first-time.md) .
2. Zorg ervoor dat u het hulp programma voor de Azure Migrate: Server evaluatie hebt [toegevoegd](how-to-assess.md) aan een project.
4. Raadpleeg de [VMware-vereisten](migrate-support-matrix-vmware.md#vmware-requirements) voor het detecteren en beoordelen van virtuele VMware-machines met het Azure migrate apparaat.
5. Controleer de [vereisten](migrate-appliance.md) voor het implementeren van het Azure migrate apparaat.
6. [Controleer de ondersteuning en vereisten](migrate-support-matrix-vmware.md#application-discovery) voor toepassings detectie.



## <a name="deploy-the-azure-migrate-appliance"></a>Het Azure Migrate apparaat implementeren

1. [Bekijk](migrate-appliance.md#appliance---vmware) de vereisten voor het implementeren van het Azure migrate apparaat.
2. Bekijk de Azure-Url's die het apparaat nodig heeft voor toegang tot de [open bare](migrate-appliance.md#public-cloud-urls) en [overheids Clouds](migrate-appliance.md#government-cloud-urls).
3. [Bekijk gegevens](migrate-appliance.md#collected-data---vmware) die door het apparaat worden verzameld tijdens de detectie en evaluatie.
4. [Noteer](migrate-support-matrix-vmware.md#port-access) de toegangs vereisten voor poorten voor het apparaat.
5. [Implementeer het Azure migrate apparaat om de](how-to-set-up-appliance-vmware.md) detectie te starten. Als u het apparaat wilt implementeren, downloadt en importeert u een eicellen-sjabloon in VMware om het apparaat als een virtuele VMware-machine te maken. U configureert het apparaat en registreert het vervolgens met Azure Migrate.
6. Wanneer u het apparaat implementeert, moet u het volgende opgeven om de continue detectie te starten:
    - De naam van de vCenter Server waarmee u verbinding wilt maken.
    - Referenties die u hebt gemaakt voor het apparaat om verbinding te maken met vCenter Server.
    - De account referenties die u hebt gemaakt voor het apparaat om verbinding te maken met Vm's van Windows/Linux.

Nadat het apparaat is geïmplementeerd en u referenties hebt gegeven, start het apparaat doorlopende detectie van VM-meta gegevens en prestatie gegevens, samen met en detectie van apps, functies en rollen.  De duur van het detecteren van apps hangt af van het aantal Vm's dat u hebt. Het duurt doorgaans een uur voor app-detectie van 500 Vm's.

## <a name="prepare-a-user-account"></a>Een gebruikers account voorbereiden

Maak een account om te gebruiken voor detectie en voeg het toe aan het apparaat.

### <a name="create-a-user-account-for-discovery"></a>Een gebruikers account maken voor detectie

Stel een gebruikers account in zodat de server evaluatie toegang kan krijgen tot de virtuele machine voor detectie. [Meer informatie](migrate-support-matrix-vmware.md#application-discovery) over account vereisten.


### <a name="add-the-user-account-to-the-appliance"></a>Het gebruikers account toevoegen aan het apparaat

Voeg het gebruikers account toe aan het apparaat.

1. Open de app voor het beheren van apparaten. 
2. Navigeer naar het deel venster **vCenter-gegevens opgeven** .
3. Klik in **toepassing en afhankelijkheden van Vm's detecteren**op **referenties toevoegen**
3. Kies het **besturings systeem**, geef een beschrijvende naam op voor het account en het**wacht woord** voor de **gebruikers naam**/
6. Klik op **Opslaan**.
7. Klik op **opslaan en detectie starten**.

    ![VM-gebruikers account toevoegen](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)





## <a name="review-and-export-the-inventory"></a>De inventaris controleren en exporteren

Nadat de detectie is beëindigd, kunt u de app-inventaris bekijken en exporteren in de Azure Portal als u referenties hebt ingesteld voor app-detectie.

1. Klik in **Azure migrate servers** > **Azure migrate: Server evaluatie**op de weer gegeven aantal om de pagina **gedetecteerde servers** te openen.

    > [!NOTE]
    > U kunt in deze fase ook optioneel afhankelijkheids toewijzing instellen voor gedetecteerde computers, zodat u afhankelijkheden kunt visualiseren tussen computers die u wilt beoordelen. [Meer informatie](how-to-create-group-machine-dependencies.md).

2. In **gedetecteerde toepassingen**klikt u op het aantal weer gegeven.
3. In de **toepassings inventaris**kunt u de gedetecteerde apps, rollen en functies bekijken.
4. Als u de inventaris wilt exporteren, klikt u op **app-inventaris exporteren**in **gedetecteerde servers**.

De app-inventarisatie wordt geëxporteerd en gedownload in Excel-indeling. Op de **inventarisatie** pagina van de toepassing worden alle apps weer gegeven die op alle computers zijn gedetecteerd.

## <a name="next-steps"></a>Volgende stappen

- [Een evaluatie maken](how-to-create-assessment.md) voor lift-en Shift-migratie van de gedetecteerde servers.
- Beoordeling van een SQL Server data bases met behulp van [Azure migrate: data base-evaluatie](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
