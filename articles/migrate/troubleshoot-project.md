---
title: Problemen met Azure Migrate-projecten oplossen
description: Helpt u problemen op te lossen bij het maken en beheren van Azure Migrate-projecten.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: 3b8c2f6ec33965317d2aaa23a36b6becff11a54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75725726"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Problemen met Azure Migrate-projecten oplossen

Met dit artikel u problemen oplossen bij het maken en beheren van [Azure Migrate-projecten.](migrate-services-overview.md)

## <a name="how-to-add-new-project"></a>Hoe voegt u een nieuw project toe?

U meerdere Azure Migrate-projecten in een abonnement hebben. [Meer informatie over het](how-to-add-tool-first-time.md) maken van een project voor de eerste keer of [het toevoegen van extra](create-manage-projects.md#create-additional-projects) projecten.

## <a name="what-azure-permissions-are-needed"></a>Welke Azure-machtigingen zijn nodig?

U hebt inzender- of eigenaarmachtigingen in het abonnement machtigingen nodig om een Azure Migrate-project te maken.

## <a name="cant-find-a-project"></a>Kan geen project vinden

Het zoeken naar een bestaand Azure Migrate-project is afhankelijk van of u de huidige of oude versie van Azure Migrate gebruikt. [Volg](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Kan geen geografie vinden

U een Azure Migrate-project maken in [ondersteunde regio's.](migrate-support-matrix.md#supported-geographies) Houd er rekening mee dat de projectgeografie wordt gebruikt om gedetecteerde machinemetagegevens op te slaan. U machines ook op andere locaties beoordelen of migreren.

## <a name="what-are-vm-limits"></a>Wat zijn VM-limieten?

U maximaal 35.000 VMware VM's of maximaal 35.000 Hyper-V Vm's in één project beoordelen. Een project kan zowel VMware VM's als Hyper-V VM's bevatten, tot aan de beoordelingslimieten.

## <a name="can-i-upgrade-old-project"></a>Kan ik een upgrade van het oude project?

Projecten uit de vorige versie van Azure Migrate kunnen niet worden bijgewerkt. U moet [een nieuw project maken](how-to-add-tool-first-time.md)en er hulpprogramma's aan toevoegen.

## <a name="cant-create-a-project"></a>Kan geen project maken

Als u een project probeert te maken en een implementatiefout tegenkomt:

- Probeer het project opnieuw te maken voor het geval het een tijdelijke fout is. Klik in **Implementaties**op **Opnieuw implementeren** om het opnieuw te proberen.
- Controleer of u machtigingen voor inzender of eigenaar in het abonnement hebt.
- Als u in een nieuw toegevoegde geografie wordt geïmplementeerd, wacht u een korte tijd en probeert u het opnieuw.
- Als u de fout 'Aanvragen moeten gebruikersidentiteitskoppen' bevatten, kan dit erop wijzen dat u geen toegang hebt tot de Azure AD-tenant (Azure AD) van Azure Directory (Azure AD). In dat geval:
    - Wanneer u voor het eerst aan een Azure AD-tenant wordt toegevoegd, ontvangt u een e-mailuitnodiging om lid te worden van de tenant.
    - Accepteer de uitnodiging die aan de tenant moet worden toegevoegd.
    - Als u de e-mail niet zien, neemt u contact op met een gebruiker met toegang tot de tenant en vraagt u deze de uitnodiging opnieuw naar u te [verzenden.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)
    - Nadat u de uitnodigingse-mail hebt ontvangen, opent u deze en selecteert u de koppeling om de uitnodiging te accepteren. Meld u vervolgens af bij de Azure-portal en meld u opnieuw aan. (het vernieuwen van de browser werkt niet.) U vervolgens beginnen met het maken van het migratieproject.

## <a name="how-do-i-delete-a-project"></a>Hoe verwijder ik een project

[Volg deze instructies](create-manage-projects.md#delete-a-project) om een project te verwijderen. Houd er rekening mee dat wanneer u een project verwijdert, zowel het project als de metagegevens over gedetecteerde machines in het project worden verwijderd.

## <a name="added-tools-dont-show"></a>Toegevoegde gereedschappen worden niet weergegeven

Zorg ervoor dat u het juiste project hebt geselecteerd. Klik in de Azure Migrate-hub > **servers** of in **databases**op **Wijzigen** naast Project **migreren (Wijzigen)** in de rechterbovenhoek van het scherm. Kies het juiste abonnement en projectnaam > **OK**. De pagina moet worden vernieuwd met de toegevoegde gereedschappen van het geselecteerde project.

## <a name="next-steps"></a>Volgende stappen

[Beoordelings-](how-to-assess.md) of [migratiehulpprogramma's](how-to-migrate.md) toevoegen aan Azure Migrate-projecten.