---
title: Een lab opzetten om databasebeheer voor relationele databases te onderwijzen | Microsoft Documenten
description: Meer informatie over het opzetten van een lab om het beheer van relationele databases te onderwijzen.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 4c375487b30595251753021033c98cf0ca1e8dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469915"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Een lab opzetten om databasebeheer voor relationele databases te onderwijzen

In dit artikel wordt beschreven hoe u een lab instelt voor een basisbeheerklasse voor databases in Azure Lab Services. Databases concepten zijn een van de inleidende cursussen onderwezen in de meeste van de Informatica afdelingen op de universiteit. Structured Query Language (SQL) is een internationale standaard. SQL is de standaardtaal voor relatiedatabasebeheer, inclusief het toevoegen, openen en beheren van inhoud in een database.  Het is het meest bekend om zijn snelle verwerking, bewezen betrouwbaarheid, gemak en flexibiliteit van het gebruik.

In dit artikel laten we zien hoe je een virtuele machinesjabloon in een lab instellen met zowel MySQL Database Server als SQL Server 2019-server.  [MySQL](https://www.mysql.com/) is een vrij beschikbaar open source Relational Database Management System (RDBMS).  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) is de nieuwste versie van Microsoft's RDBMS.

## <a name="lab-configuration"></a>Labconfiguratie

Om dit lab in te stellen, hebt u een Azure-abonnement en labaccount nodig om aan de slag te gaan. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. Zodra u een Azure-abonnement hebt, u een nieuw labaccount maken in Azure Lab Services. Zie [Zelfstudie om een Lab-account op te zetten voor](tutorial-setup-lab-account.md)meer informatie over het maken van een nieuw labaccount.  U ook een bestaand labaccount gebruiken.

### <a name="lab-account-settings"></a>Lab-accountinstellingen

Schakel de instellingen in die in de onderstaande tabel worden beschreven voor het labaccount. Zie Marketplace-afbeeldingen opgeven die beschikbaar zijn voor makers van het lab voor meer informatie over het inschakelen [van marketplace-afbeeldingen.](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)

| Lab-accountinstelling | Instructies |
| ------------------- | ------------ |
|Afbeelding van Marktplaats| Schakel de afbeelding 'SQL Server 2019 Standard on Windows Server 2019' in voor gebruik binnen uw labaccount.|

### <a name="lab-settings"></a>Lab-instellingen

Gebruik de instellingen in de onderstaande tabel bij het instellen van een klaslokaallab.  Zie een lesprogramma voor een klaslokaal voor meer informatie over het maken van een [leslokaallab.](tutorial-setup-classroom-lab.md)

| Lab-instellingen | Waarde/instructies |
| ------------ | ------------------ |
|Virtuele machinegrootte| Gemiddeld. Deze grootte is het meest geschikt voor relationele databases, in-memory caching en analytics.|
|Afbeelding van virtuele machine| SQL Server 2019 Standaard op Windows Server 2019|

## <a name="template-machine-configuration"></a>Sjabloonmachineconfiguratie

Als u MySQL op Windows Server 2019 wilt installeren, u de stappen volgen die worden genoemd in [Het installeren en uitvoeren van MySQL Community Server op een virtuele machine.](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)

SQL Server 2019 is vooraf geïnstalleerd in het beeld van de virtuele machine dat we hebben gekozen bij het maken van het nieuwe lab.

## <a name="cost-estimate"></a>Kostenraming

Laten we een mogelijke kostenraming voor deze klasse bespreken.  We gebruiken een klas van 25 studenten.  Er zijn 20 uur geplande lestijd.  Ook krijgt elke student een quotum van 10 uur voor huiswerk of opdrachten buiten de geplande lestijd.  De virtuele machine grootte die we kozen was medium, dat is 42 lab eenheden.

Hier volgt een voorbeeld van een mogelijke kostenraming voor deze klasse:

25 \* studenten (20 geplande uren + \* 10 quota-uren) 0,42 USD per uur = 315,00 USD

Meer informatie over prijzen, zie [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusie

In dit artikel hebt u de stappen doorlopen die nodig zijn om een lab te maken voor basisconcepten voor databasebeheer met behulp van zowel MySQL als SQL Server. U een vergelijkbare instelling gebruiken voor andere databasesklassen.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het opzetten van een lab.

- [Een sjabloon maken en beheren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quotum instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een schema instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Koppelingen naar e-mailregistratie naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)
