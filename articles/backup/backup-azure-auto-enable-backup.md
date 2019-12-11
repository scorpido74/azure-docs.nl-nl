---
title: Automatisch back-ups inschakelen bij het maken van VM'S met Azure Policy
description: Een artikel met informatie over het gebruik van Azure Policy om back-up automatisch in te scha kelen voor alle Vm's die in een bepaald bereik zijn gemaakt
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: ea3c0d217c8495078e91e171caef695bb32d129b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980125"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Automatisch back-ups inschakelen bij het maken van VM'S met Azure Policy

Een van de belangrijkste verantwoordelijkheden van een back-up-of nalevings beheerder in een organisatie is ervoor te zorgen dat alle bedrijfs kritieke computers een back-up maken met de juiste Bewaar periode.

Momenteel biedt Azure Backup een ingebouwd beleid (met behulp van Azure Policy) dat kan worden toegewezen aan **alle virtuele Azure-machines in een opgegeven locatie binnen een abonnement of resource groep**. Wanneer dit beleid is toegewezen aan een bepaald bereik, worden alle nieuwe Vm's die in dat bereik zijn gemaakt, automatisch geconfigureerd voor back-up naar een **bestaande kluis op dezelfde locatie en hetzelfde abonnement**. De gebruiker kan de kluis en het Bewaar beleid opgeven waarnaar de back-ups van virtuele machines moeten worden gekoppeld.

## <a name="supported-scenarios"></a>Ondersteunde scenario 's 

* Het ingebouwde beleid wordt momenteel alleen ondersteund voor virtuele Azure-machines. Gebruikers moeten er zeker van zijn dat het Bewaar beleid dat tijdens de toewijzing is opgegeven, een VM-Bewaar beleid is. Raadpleeg [Dit](https://aka.ms/PolicySupportedSKUs) document voor een overzicht van alle VM-sku's die door dit beleid worden ondersteund.

* Het beleid kan per keer worden toegewezen aan één locatie en abonnement. Als u back-ups wilt maken voor Vm's op verschillende locaties en abonnementen, moet u meerdere exemplaren van de beleids toewijzing maken, één voor elke combi natie van locatie en abonnement.

* De opgegeven kluis en de virtuele machines die voor back-up zijn geconfigureerd, kunnen zich onder verschillende resource groepen bevindt.

* Het bereik van de beheer groep wordt momenteel niet ondersteund.

## <a name="using-the-built-in-policy"></a>Het ingebouwde beleid gebruiken

Volg de onderstaande stappen om het beleid toe te wijzen aan het vereiste bereik:

1. Meld u aan bij Azure Portal en navigeer naar het **beleids** dashboard.
2. Selecteer **definities** in het linkermenu om een lijst op te halen met alle ingebouwde beleids regels voor Azure-resources.
3. Filter de lijst voor **Category = backup**. U ziet dat de lijst wordt gefilterd op één beleid met de naam ' back-up configureren op Vm's van een locatie naar een bestaande centrale kluis op dezelfde locatie '.
Dash board voor ![beleid](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Klik op de naam van het beleid. U wordt omgeleid naar de gedetailleerde definitie van dit beleid.
Blade ![beleids definitie](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Klik boven aan de Blade op de knop **toewijzen** . Hiermee wordt u omgeleid naar de Blade **beleid toewijzen** .
6. Klik onder **basis principes**op de drie puntjes naast het veld **bereik** . Hiermee opent u een rechter context-Blade waar u het abonnement kunt selecteren waarop het beleid moet worden toegepast. U kunt desgewenst ook een resource groep selecteren, zodat het beleid alleen wordt toegepast op virtuele machines in een bepaalde resource groep.
Basis principes van ![beleids toewijzing](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Kies op het tabblad **para meters** een locatie in de vervolg keuzelijst en selecteer de kluis en het back-upbeleid waaraan de vm's in het bereik moeten worden gekoppeld.
![beleids toewijzings parameters](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Zorg ervoor dat het **effect** is ingesteld op deployIfNotExists.
9. Ga naar **controleren en maken** en klik op **maken**.

> [!NOTE]
>
> Azure Policy kunnen ook worden gebruikt op bestaande Vm's, met behulp van [herstellen](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).

> [!NOTE]
>
> Het wordt aanbevolen dat dit beleid per keer niet aan meer dan 200 Vm's wordt toegewezen. Als het beleid is toegewezen aan meer dan 200 Vm's, kan dit ertoe leiden dat de back-up een paar uur later wordt geactiveerd dan dat is opgegeven in de planning.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)