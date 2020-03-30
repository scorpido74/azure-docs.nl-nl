---
title: Back-up automatisch inschakelen bij het maken van VM's met Azure Policy
description: Een artikel waarin wordt beschreven hoe u Azure Policy gebruiken om back-upautomatisch in te schakelen voor alle VM's die in een bepaald bereik zijn gemaakt
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584265"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Back-up automatisch inschakelen bij het maken van VM's met Azure Policy

Een van de belangrijkste verantwoordelijkheden van een back-up- of compliancebeheerder in een organisatie is ervoor te zorgen dat alle bedrijfskritieke machines worden geback-upt met de juiste retentie.

Vandaag biedt Azure Backup een ingebouwd beleid (met Azure Policy) dat kan worden toegewezen aan **alle Azure VM's op een opgegeven locatie binnen een abonnement of resourcegroep.** Wanneer dit beleid is toegewezen aan een bepaald bereik, worden alle nieuwe VM's die in dat bereik zijn gemaakt, automatisch geconfigureerd voor back-up naar een **bestaande kluis op dezelfde locatie en een abonnement.** De gebruiker kan de kluis en het bewaarbeleid opgeven waaraan de gekoppelde VM's moeten worden gekoppeld.

## <a name="supported-scenarios"></a>Ondersteunde scenario 's

* Het ingebouwde beleid wordt momenteel alleen ondersteund voor Azure VM's. Gebruikers moeten ervoor zorgen dat het bewaarbeleid dat tijdens de toewijzing is opgegeven, een VM-bewaarbeleid is. Raadpleeg [dit](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus) document om alle VM SKU's te zien die door dit beleid worden ondersteund.

* Het beleid kan worden toegewezen aan één locatie en abonnement tegelijk. Om back-ups voor VM's op verschillende locaties en abonnementen in te schakelen, moeten meerdere exemplaren van de beleidstoewijzing worden gemaakt, één voor elke combinatie van locatie en abonnement.

* De opgegeven kluis en de VM's die zijn geconfigureerd voor back-up, kunnen zich onder verschillende brongroepen bevindt.

* Het bereik van de beheergroep wordt momenteel niet ondersteund.

* Het ingebouwde beleid is momenteel niet beschikbaar in nationale clouds.

## <a name="using-the-built-in-policy"></a>Het ingebouwde beleid gebruiken

Volg de onderstaande stappen om het beleid aan het vereiste bereik toe te wijzen:

1. Meld u aan bij de Azure Portal en navigeer naar het **beleidsdashboard.**
2. Selecteer **Definities** in het linkermenu om een lijst met alle ingebouwde beleidsregels in Azure Resources te krijgen.
3. Filter de lijst voor **Categorie=Back-up**. U ziet de lijst gefilterd naar één beleid met de naam 'Back-up configureren op VM's van een locatie naar een bestaande centrale Vault op dezelfde locatie'.
![Beleidsdashboard](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Klik op de naam van het beleid. U wordt doorgestuurd naar de gedetailleerde definitie voor dit beleid.
![Blad beleidsdefinitie](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Klik op de knop **Toewijzen** boven aan het blad. Hiermee wordt u omgeleid naar het **blad Beleid toewijzen.**
6. Klik **onder Basisbeginselen**op de drie puntjes naast het veld **Bereik.** Dit opent een rechter contextblad waar u het abonnement selecteren voor het beleid waarop u moet worden toegepast. U ook optioneel een resourcegroep selecteren, zodat het beleid alleen wordt toegepast voor VM's in een bepaalde resourcegroep.
![Basisbeginselen van beleidstoewijzing](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Kies op het tabblad **Parameters** een locatie in de vervolgkeuzelijst en selecteer het kluis- en back-upbeleid waaraan de VM's in het bereik moeten worden gekoppeld.
![Parameters beleidstoewijzing](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Controleer of **Effect** is ingesteld op implementatieIfNotExists.
9. Navigeer naar **Controleren+maken** en klik op **Maken**.

> [!NOTE]
>
> Azure Policy kan ook worden gebruikt op bestaande VM's, met behulp van [herstel.](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)

> [!NOTE]
>
> Het wordt aanbevolen dat dit beleid niet wordt toegewezen aan meer dan 200 VM's tegelijk. Als het beleid is toegewezen aan meer dan 200 VM's, kan dit ertoe leiden dat de back-up een paar uur later wordt geactiveerd dan die welke door de planning is opgegeven.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)
