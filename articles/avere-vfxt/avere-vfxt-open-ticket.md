---
title: Ondersteuning krijgen voor avere vFXT voor Azure
description: Uitleg over het openen van ondersteunings tickets over avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 27f32a922a1b063096b0ccf28a01a78d442e0271
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889287"
---
# <a name="get-help-with-your-system"></a>Hulp vragen bij uw systeem

Als u hulp nodig hebt bij uw avere vFXT voor Azure, kunt u de verschillende manieren om ondersteuning te verkrijgen:

* **Avere vFXT-probleem** : gebruik de Azure Portal om een ondersteunings ticket voor uw avere-vFXT te openen, zoals [hieronder](#open-a-support-ticket-for-your-avere-vfxt)wordt beschreven.
* **Quotum** : als u een quotum wilt hebben, moet u [een quotum toename aanvragen](#request-a-quota-increase)
* **Documentatie en voor beelden** : als u problemen ondervindt met deze documentatie of voor beelden, schuift u naar de onderkant van de pagina met het probleem en gebruikt u de sectie **feedback** om naar bestaande problemen te zoeken en een nieuwe te bestand, indien nodig.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Open een ondersteunings ticket voor uw avere-vFXT

Als u problemen ondervindt tijdens het implementeren of gebruiken van AVERE vFXT, vraagt u hulp via de Azure Portal.

Volg deze stappen om ervoor te zorgen dat uw ondersteunings ticket is gelabeld met een resource uit uw cluster. Als u het ticket labelt, kunnen we dit routeren naar de juiste ondersteunings resource.

1. Selecteer in [https://portal.azure.com](https://portal.azure.com) **resource groepen**.

   ![scherm afbeelding van Azure Portal linkermenu met ' resource groepen ' omcirkeld](media/avere-vfxt-ticket-rg.png)

1. Blader naar de resource groep die het vFXT-cluster bevat waarop het probleem zich heeft voorgedaan en klik op een van de virtuele avere-machines.

    ![scherm afbeelding van het deel venster Overzicht van Azure Portal-resource groep met een bepaalde VM omcirkeld](media/avere-vfxt-ticket-vm.png)

1. Schuif op de pagina VM omlaag naar de onderkant van het linkerdeel venster en klik op **nieuwe ondersteunings aanvraag**.

    ![Scherm afbeelding van de Azure Portal VM-pagina voor de virtuele machine van de vorige scherm afbeelding. Het menu links wordt naar beneden geschoven en ' nieuwe ondersteunings aanvraag ' wordt omcirkeld.](media/avere-vfxt-ticket-request.png)

1. Klik op de pagina een van de ondersteunings aanvraag op **alle services** en zoek onder **opslag** om **avere vFXT**te kiezen.

    ![scherm opname van het nieuwe venster voor de ondersteunings aanvraag in de Azure Portal met de koptekst ' basis principes ' en een cirkel rond het item ' service '. De knop alle services is geselecteerd en de vervolg keuzelijst veld heeft de waarde ' avere vFXT '](media/avere-vfxt-ticket-service.png)

1. Kies op pagina twee het probleem type en de categorie die het meest overeenkomt met uw probleem. Voeg een korte titel en beschrijving toe die de tijd bevat waarop het probleem zich voordeed.

   ![scherm opname van het nieuwe scherm met de ondersteunings aanvraag met de koptekst ' probleem ', dat veel velden bevat die moeten worden voltooid](media/avere-vfxt-ticket-problem.png)

1. Vul op de pagina drie uw contact gegevens in en klik op **maken**. Er wordt een bevestigings-en ticket nummer verzonden naar uw e-mail adres en een ondersteunings medewerker neemt contact met u op.

## <a name="request-a-quota-increase"></a>Een quotum verhoging aanvragen

Lees [quotum voor het vFXT-cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) voor meer informatie over de onderdelen die nodig zijn voor het implementeren van de avere VFXT voor Azure. U kunt [een quotum verhoging aanvragen](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) van de Azure Portal.
