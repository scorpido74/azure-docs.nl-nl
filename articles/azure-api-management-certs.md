---
title: Een Azure Service Management Certificate uploaden | Microsoft Documenten
description: Meer informatie over het uploaden van het Service Management-certificaat voor de Azure-portal.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: article
ms.date: 08/01/2017
ms.author: gwallace
ms.openlocfilehash: 4b49a9b391eeca2d2e249b171d99f231bda6fdff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329116"
---
# <a name="upload-an-azure-service-management-certificate"></a>Een Azure Service Management Certificate uploaden
Met beheercertificaten u zich verifiëren met het klassieke implementatiemodel van Azure. Veel programma's en hulpprogramma's (zoals Visual Studio of de Azure SDK) gebruiken deze certificaten om de configuratie en implementatie van verschillende Azure-services te automatiseren. 

> [!WARNING]
> Wees voorzichtig! Met deze typen certificaten kan iedereen die zich met hen verifieert, het abonnement beheren waaraan hij of zij is gekoppeld.
>
>

Zie [Certificatenoverzicht voor Azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)als u meer informatie wilt over Azure-certificaten (waaronder het maken van een zelfondertekend certificaat.

U [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) ook gebruiken om clientcode te verifiëren voor automatiseringsdoeleinden.

**Let op:** U moet een medebeheerder zijn bij het abonnement om bewerkingen uit te voeren onder Beheercertificaten. [Meer informatie](https://go.microsoft.com/fwlink/?linkid=849300) over het toevoegen of verwijderen van medebeheerders uit nieuwe Azure Portal 

## <a name="upload-a-management-certificate"></a>Een beheercertificaat uploaden
Zodra u een beheercertificaat hebt gemaakt (.cer-bestand met alleen de openbare sleutel) u deze uploaden naar de portal. Wanneer het certificaat beschikbaar is in de portal, kan iedereen met een overeenkomend certificaat (privésleutel) verbinding maken via de Beheer-API en toegang krijgen tot de bronnen voor het bijbehorende abonnement.

1. Log in bij de [Azure-portal](https://portal.azure.com).
2. Klik op **Alle services** onder in de Azure-servicelijst en selecteer **Abonnementen** in de servicegroep _Algemeen._

    ![Menu Abonnement](./media/azure-api-management-certs/subscriptions_menu.png)

3. Selecteer het juiste abonnement dat u aan het certificaat wilt koppelen.     
4. Nadat u het juiste abonnement hebt geselecteerd, drukt u op **Beheercertificaten** in de groep _Instellingen._

    ![Instellingen](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Druk op de knop **Uploaden.**

    ![Uploaden op de pagina certificaten](./media/azure-api-management-certs/certificates_page.png)
6. Vul de dialoogvenstergegevens in en druk op **Uploaden**.

    ![Instellingen](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Volgende stappen
Nu u een beheercertificaat aan een abonnement hebt gekoppeld, u (nadat u het overeenkomende certificaat lokaal hebt geïnstalleerd) programmatisch verbinding maken met de [klassieke implementatiemodel REST API](/azure/?pivot=sdkstools) en de verschillende Azure-bronnen automatiseren die ook aan dat abonnement zijn gekoppeld.
