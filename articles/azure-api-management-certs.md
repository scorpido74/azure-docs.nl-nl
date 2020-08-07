---
title: Een Azure-Service beheer certificaat uploaden | Microsoft Docs
description: Meer informatie over het uploaden van het Service beheer certificaat voor de Azure Portal. Met beheer certificaten kunt u verifiëren met het klassieke implementatie model.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: article
ms.date: 08/01/2017
ms.author: gwallace
ms.openlocfilehash: 0d2436b524ba6df68b12206bcb545e54a405b731
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848550"
---
# <a name="upload-an-azure-service-management-certificate"></a>Een Azure Service Management-certificaat uploaden
Met beheer certificaten kunt u verifiëren met het klassieke implementatie model van Azure. Voor veel programma's en hulpprogramma's (zoals Visual Studio of de Azure SDK) worden deze certificaten gebruikt om de configuratie en implementatie van verschillende Azure-services te automatiseren. 

> [!WARNING]
> Wees voorzichtig! Met deze typen certificaten kunnen gebruikers die met hen worden geverifieerd, het abonnement beheren waaraan ze zijn gekoppeld.
>
>

Zie [Certificaten-overzicht voor azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)als u meer informatie wilt over Azure-certificaten (inclusief het maken van een zelfondertekend certificaat).

U kunt ook [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) gebruiken om client code te verifiëren voor automatiserings doeleinden.

**Opmerking:** U moet een mede beheerder zijn van het abonnement om bewerkingen onder beheer certificaten uit te voeren. [Meer informatie](https://go.microsoft.com/fwlink/?linkid=849300) over het toevoegen of verwijderen van co-beheerders vanuit een nieuwe Azure-Portal 

## <a name="upload-a-management-certificate"></a>Een beheer certificaat uploaden
Zodra u een beheer certificaat hebt gemaakt (. cer-bestand met alleen de open bare sleutel), kunt u het uploaden naar de portal. Wanneer het certificaat beschikbaar is in de portal, kan iedereen met een overeenkomend certificaat (persoonlijke sleutel) verbinding maken via de beheer-API en toegang krijgen tot de resources voor het gekoppelde abonnement.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik op **alle services** onder de lijst van de Azure-service en selecteer vervolgens **abonnementen** in de _algemene_ service groep.

    ![Menu abonnement](./media/azure-api-management-certs/subscriptions_menu.png)

3. Zorg ervoor dat u het juiste abonnement selecteert dat u wilt koppelen aan het certificaat.     
4. Nadat u het juiste abonnement hebt geselecteerd, drukt u op **beheer certificaten** in de groep _instellingen_ .

    ![Instellingen](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Druk op de knop **uploaden** .

    ![Pagina uploads op certificaten](./media/azure-api-management-certs/certificates_page.png)
6. Vul de dialoog gegevens in en druk op **uploaden**.

    ![Instellingen](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Volgende stappen
Nu u een beheer certificaat aan een abonnement hebt gekoppeld, kunt u (nadat u het overeenkomende certificaat lokaal hebt geïnstalleerd) via een programma verbinding maken met het [klassieke implementatie model rest API](/azure/?pivot=sdkstools) en de verschillende Azure-resources automatiseren die ook aan het abonnement zijn gekoppeld.
