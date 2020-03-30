---
title: Ondersteuning voor Avere vFXT voor Azure
description: Uitleg over het openen van ondersteuningstickets over Avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252556"
---
# <a name="get-help-with-your-system"></a>Hulp krijgen met uw systeem

Voor hulp bij uw Avere vFXT voor Azure-systeem u dit onder andere onder andere ondersteunen:

* **Avere vFXT-probleem** - Gebruik de Azure-portal om een ondersteuningsticket te openen voor uw Avere vFXT zoals [hieronder](#open-a-support-ticket-for-your-avere-vfxt)beschreven.
* **Quotum** - Als u een probleem met quota hebt, [vraagt u een quotumverhoging aan](#request-a-quota-increase)
* **Documentatie en voorbeelden** - Als u problemen vindt met deze documentatie of voorbeelden, bladert u naar de onderkant van de pagina met het probleem en gebruikt u de sectie **Feedback** om te zoeken naar bestaande problemen en indien nodig een nieuwe op te nemen.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Open een supportticket voor uw Avere vFXT

Als u problemen ondervindt tijdens het implementeren of gebruiken van Avere vFXT, vraagt u hulp via de Azure-portal.

Volg deze stappen om ervoor te zorgen dat uw ondersteuningsticket is getagd met een bron uit uw cluster. Door het ticket te taggen, kunnen we het doorsturen naar de juiste ondersteuningsbron.

1. Selecteer [https://portal.azure.com](https://portal.azure.com) **Resourcegroepen**. Blader naar de brongroep die het vFXT-cluster bevat waar het probleem zich heeft voorgedaan en klik op een van de virtuele machines van het Avere-cluster.

    ![schermafbeelding van het deelvenster 'overzicht' van Azure-portalbrongroep met een bepaalde virtuele machine omcirkeld](media/avere-vfxt-ticket-vm.png)

1. Schuif op de VM-pagina omlaag naar de onderkant van het linkerdeelvenster en klik op **Nieuw ondersteuningsverzoek**.

    ![Schermafbeelding van de VM-pagina voor Azure-portal voor de VM uit de vorige schermafbeelding. Het linkermenu wordt naar beneden gebladerd en 'Nieuw ondersteuningsverzoek' is omcirkeld.](media/avere-vfxt-ticket-request.png)

1. Kies op de eerste pagina van het ondersteuningsverzoek het probleemtype en controleer of het juiste abonnement is geselecteerd.

   Klik **onder Service**op Alle **services** en kijk onder **Opslag** om **Avere vFXT**te kiezen.

   Voeg een korte samenvatting toe en selecteer het probleemtype.

    ![schermafbeelding van een nieuw ondersteuningsaanvraagscherm in de Azure-portal. Het tabblad Basisiser is geselecteerd. Schermitems zijn probleemtype, abonnement, service, overzicht en probleemtype.](media/ticket-basics.png)

   Klik op **Volgende** om verder te gaan.

1. De tweede pagina van het ondersteuningsformulier bevat suggesties voor het oplossen van het probleem op basis van uw overzichtsbeschrijving. Klik op de knop **Volgende** onderaan als je nog steeds een ondersteuningsticket moet maken.

   ![schermafbeelding van het nieuwe ondersteuningsaanvraagscherm met het tabblad Oplossingen geselecteerd. Een tekstveld in het midden heeft de titel 'Aanbevolen oplossing' en legt mogelijke remedies uit.](media/ticket-solutions.png)

1. Geef op de derde pagina details op: dit bevat informatie over uw cluster, de tijd dat het probleem zich heeft voorgedaan, de ernst en hoe u contact met u opnemen. Vul de informatie in en klik op de knop **Volgende** onderaan.

   ![schermafbeelding van het nieuwe ondersteuningsaanvraagscherm met het tabblad Details geselecteerd. Informatievelden zijn ingedeeld in de categorieën 'Probleemgegevens', 'Ondersteuningsmethode' en 'Contactgegevens'.](media/ticket-details.png)

1. Bekijk de informatie op de laatste pagina en klik op **Maken**. Er wordt een bevestiging en ticketnummer naar uw e-mailadres gestuurd en een medewerker van de ondersteuning neemt contact met u op.

## <a name="request-a-quota-increase"></a>Een quotumverhoging aanvragen

Lees [Quota voor het vFXT-cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) om te zien welke componenten nodig zijn om de Avere vFXT voor Azure te implementeren. U [een quotumverhoging aanvragen](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) bij de Azure-portal.
