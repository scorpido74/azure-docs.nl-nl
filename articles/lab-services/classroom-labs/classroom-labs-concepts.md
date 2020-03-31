---
title: Classroom Labs-concepten - Azure Lab Services | Microsoft Documenten
description: Leer de basisconcepten van Lab Services en hoe het eenvoudig is om laboratoria te maken en te beheren.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 15fd3c18d059466c2b2bd5e2431013f393092b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526226"
---
# <a name="classroom-labs-concepts"></a>Concepten van leslokaallabs

De volgende lijst bevat de belangrijkste concepten en definities van Lab Services:

## <a name="quota"></a>Quota

Quotum is de tijdslimiet (in uren) die een docent kan instellen voor een student om een lab-vm te gebruiken. Het kan worden ingesteld op 0, of een specifiek aantal uren. Als het quotum is ingesteld op 0, kan een student de virtuele machine alleen gebruiken wanneer een schema wordt uitgevoerd of wanneer een docent handmatig de virtuele machine voor de student inschakelt.  

Quota-uren worden geteld wanneer de student de lab-vm zelf start.  Als een docent de lab-vm handmatig start voor een student, worden quota-uren niet gebruikt voor die student.

## <a name="schedules"></a>Planningen

Schema's zijn de tijdvakken die een docent voor de klas kan maken, zodat de vm's van de student beschikbaar zijn voor de lestijd.  Schema's kunnen eenmalig of terugkerend zijn.  Quota-uren worden niet gebruikt wanneer een planning wordt uitgevoerd.

Er zijn drie soorten schema's: Standaard, Alleen starten en Alleen stoppen.

- **Standaard**.  Dit schema start alle vm's voor studenten op de opgegeven begintijd en sluit alle VM's van studenten af op de opgegeven stoptijd.
- **Begin alleen**.   Dit schema start alle vm's voor studenten op het opgegeven tijdstip.  Vm's voor studenten worden pas gestopt als een student de VM stopt via de Azure Lab Services-portal of er alleen een stopschema optreedt.
- **Stop alleen**maar .  Dit schema stopt alle vm's voor studenten op het opgegeven tijdstip.  

## <a name="template-virtual-machine"></a>Virtuele sjabloonmachine

Een sjabloon virtuele machine in een lab is een basis virtuele machine beeld waaruit alle virtuele machines van gebruikers zijn gemaakt. Trainers / lab makers het opzetten van de sjabloon virtuele machine en configureren met de software die ze willen bieden aan de opleiding deelnemers om labs te doen. Wanneer u een sjabloon-VM publiceert, maakt of werkt Azure Lab Services laboratoriumVM's aan op basis van de sjabloon-VM.

## <a name="user-profiles"></a>Gebruikersprofielen

In dit artikel worden de verschillende gebruikersprofielen in Azure Lab Services beschreven.

### <a name="lab-account-owner"></a>Labeigenaar

Meestal fungeert een IT-beheerder van de cloudresources van de organisatie, die ook eigenaar is van het Azure-abonnement, als de eigenaar van een lab-account. Deze persoon is verantwoordelijk voor de volgende taken:

- Een lab-account instellen voor uw organisatie.
- Beleidsregels beheren en configureren voor alle labs.
- Machtigingen verlenen aan personen binnen de organisatie voor het maken van een lab onder het lab-account.

### <a name="professor"></a>Professor

Normaal gesproken maken gebruikers, bijvoorbeeld een leraar of onlinedocent leslokaallabs in een labaccount. Een docent voert de volgende taken uit:

- Leslokaallab maken.
- Virtuele machines maken in het lab.
- De juiste software installeren op virtuele machines.
- Instellen wie toegang heeft tot het lab.
- Registratiekoppeling naar het lab doorgeven aan studenten.

### <a name="student"></a>Student

Een student voert de volgende taken uit:

- De registratiekoppeling volgen die is verstuurd door een labmaker om zich te registreren bij het lab.
- Verbinding maken met een virtuele machine in het lab en deze gebruiken voor het maken van klassikale oefeningen, huiswerk en projecten.

## <a name="next-steps"></a>Volgende stappen

Aan de slag met het instellen van een labaccount dat is vereist voor het maken van een leslokaallab met Azure Lab Services:

- [Een lab-account maken](tutorial-setup-lab-account.md)
