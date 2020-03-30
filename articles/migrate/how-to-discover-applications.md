---
title: Apps, rollen en functies ontdekken op on-premises servers met Azure Migrate
description: Meer informatie over het ontdekken van apps, rollen en functies op on-premises servers met Azure Migrate Server Assessment.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453579"
---
# <a name="discover-machine-apps-roles-and-features"></a>Machine-apps, -rollen en -functies ontdekken

In dit artikel wordt beschreven hoe u toepassingen, rollen en functies op on-premises servers detecteren met Azure Migrate: Server Assessment.

Als u de voorraad apps en rollen/functies ontdekt die op uw on-premises machines worden uitgevoerd, u een migratiepad naar Azure identificeren en plannen dat is afgestemd op uw workloads.

> [!NOTE]
> App-detectie is momenteel alleen in preview voor VMware VM's en is beperkt tot alleen detectie. We bieden nog geen app-gebaseerde beoordeling aan. Machinegebaseerde beoordeling voor on-premises VM's vmware, Hyper-V VM's en fysieke servers.

App-detectie met Azure Migrate: Serverassessment is agentloos. Niets is geïnstalleerd op machines en VM's. Serverassessment gebruikt het Azure Migrate-toestel om detectie uit te voeren, samen met de referenties van machinegasten. Het toestel heeft op afstand toegang tot de VMware-machines met VMware API's.


## <a name="before-you-start"></a>Voordat u begint

1. Zorg ervoor dat u een Azure Migrate-project hebt [gemaakt.](how-to-add-tool-first-time.md)
2. Controleer of u het hulpprogramma Azure Migrate: Server Assessment aan een project hebt [toegevoegd.](how-to-assess.md)
4. Controleer de [VMware-vereisten](migrate-support-matrix-vmware.md#vmware-requirements) voor het ontdekken en beoordelen van VMware-VM's met het Azure Migrate-toestel.
5. Controleer de [vereisten](migrate-appliance.md) voor het implementeren van het Azure Migrate-toestel.
6. [Controleer de ondersteuning en vereisten](migrate-support-matrix-vmware.md#application-discovery) voor het ontdekken van toepassingen.

## <a name="prepare-for-app-discovery"></a>Voorbereiden op app-detectie

1. [Bereid u voor op de implementatie van apparaten](tutorial-prepare-vmware.md). De voorbereiding omvat het verifiëren van toestelinstellingen en het instellen van een account dat het toestel zal gebruiken om toegang te krijgen tot vCenter Server.
2. Zorg ervoor dat u een gebruikersaccount hebt (elk voor Windows- en Linux-servers) met beheerdersmachtigingen voor machines waarop u apps, rollen en functies wilt ontdekken.
3. [Implementeer het Azure Migrate-toestel](how-to-set-up-appliance-vmware.md) om detectie te starten. Als u het toestel wilt implementeren, downloadt en importeert u een OVA-sjabloon in VMware om het toestel als VMware-VM te maken. U configureert het toestel en registreert het vervolgens bij Azure Migrate.
2. Wanneer u het toestel implementeert, geeft u het volgende op als u continu detectie wilt uitvoeren:
    - De naam van de vCenterserver waarmee u verbinding wilt maken.
    - Referenties die u hebt gemaakt om verbinding te maken met vCenter Server.
    - De accountreferenties die u hebt gemaakt voor het toestel om verbinding te maken met Windows/Linux VM's.

Nadat het toestel is geïmplementeerd en u referenties hebt verstrekt, start het toestel met continue detectie van VM-metagegevens en prestatiegegevens, samen met en ontdekking van apps, functies en rollen.  De duur van app-detectie is afhankelijk van het aantal VM's dat u hebt. Het duurt meestal een uur voor app-detectie van 500 VM's.

## <a name="review-and-export-the-inventory"></a>De voorraad controleren en exporteren

Als u na het einde van de detectie referenties voor app-detectie hebt opgegeven, u de app-voorraad in de Azure-portal controleren en exporteren.

1. Klik in **Azure Migrate - Servers** > **Azure Migrate: Server Assessment**op het weergegeven aantal om de pagina **Gedetecteerde servers** te openen.

    > [!NOTE]
    > In dit stadium u ook optioneel afhankelijkheidstoewijzing instellen voor gedetecteerde machines, zodat u afhankelijkheden visualiseren tussen machines die u wilt beoordelen. [Meer informatie](how-to-create-group-machine-dependencies.md).

2. Klik in **gedetecteerde toepassingen**op het weergegeven aantal.
3. In **toepassingsinventaris**u de gedetecteerde apps, rollen en functies bekijken.
4. Als u de voorraad wilt exporteren, klikt u in **Gedetecteerde servers**op **App-inventaris exporteren**.

De app-inventaris wordt geëxporteerd en gedownload in Excel-indeling. Op het overzicht **Toepassingsinventaris** worden alle apps weergegeven die op alle machines zijn ontdekt.

## <a name="next-steps"></a>Volgende stappen

- [Maak een beoordeling](how-to-create-assessment.md) voor lift- en shiftmigratie van de gedetecteerde servers.
- Een SQL Server-databases beoordelen met [Azure Migrate: Databaseassessment](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
