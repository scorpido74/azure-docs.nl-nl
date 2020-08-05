---
title: AWS-VM's migreren naar Azure met de Azure Site Recovery-service | Microsoft Docs
description: In dit artikel wordt beschreven hoe u virtuele Windows-machines die worden uitgevoerd op Amazon Web Services (AWS) naar Azure migreert met behulp van Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dd91e99b45405cca10b9ddc2982674e72ad6bf86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281290"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>AWS-VM’s (Amazon Web Services) migreren naar Azure

In dit artikel worden de opties voor het migreren van Amazon Web Services-exemplaren (AWS) naar Azure beschreven.

## <a name="migrate-with-azure-migrate"></a>Migreren met Azure Migrate

We raden aan dat u AWS-exemplaren naar Azure migreert met behulp van de service [Azure Migrate](../migrate/migrate-services-overview.md). Azure Migrate biedt een gecentraliseerde hub voor de evaluatie en migratie van on-premises machines naar Azure met behulp van Azure Migrate, andere Azure-services en hulpprogramma’s van derden.

[Meer informatie](../migrate/tutorial-migrate-aws-virtual-machines.md) over het migreren van AWS-exemplaren met Azure Migrate. 


## <a name="migrate-with-site-recovery"></a>Migreren met Site Recovery

Site Recovery moet alleen worden gebruikt voor herstel na noodgevallen, niet voor migratie.

Als u Azure Site Recovery al gebruikt en u dit wilt blijven gebruiken voor AWS-migratie, volgt u dezelfde stappen die u gebruikt om [herstel van fysieke machines na noodgevallen](physical-azure-disaster-recovery.md) in te stellen.


> [!NOTE]
> Wanneer u een failover uitvoert voor herstel na noodgevallen, moet u als laatste stap de failover doorvoeren. Wanneer u AWS-exemplaren migreert, is de optie **Doorvoeren** niet relevant. Selecteer in plaats daarvan de optie **Migratie voltooien**. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Lees de veelgestelde vragen](../migrate/resources-faq.md) over Azure Migrate.
