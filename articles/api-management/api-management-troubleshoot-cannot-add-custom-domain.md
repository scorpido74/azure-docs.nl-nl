---
title: Kan geen aangepast domein toevoegen met behulp van Key Vault certificaat in azure API Management | Microsoft Docs
description: Meer informatie over het oplossen van het probleem waarbij u een aangepast domein in azure API Management niet kunt toevoegen met behulp van een sleutel kluis certificaat.
services: api-management
documentationcenter: ''
author: genlin
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: 30dfd864c6c237024c72325da813691ac5043bd0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072097"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Kan de hostnamen van de API Management-service niet bijwerken

In dit artikel wordt de fout ' kan de hostnamen van de API Management-service niet bijwerken ' beschreven die zich voordoen wanneer u een aangepast domein toevoegt voor de Azure API Management-service. Dit artikel bevat stappen voor het oplossen van problemen die u kunnen helpen bij het oplossen van het probleem.

## <a name="symptoms"></a>Symptomen

Wanneer u een aangepast domein probeert toe te voegen voor uw API Management-service met behulp van een certificaat van Azure Key Vault, wordt het volgende fout bericht weer gegeven:

- Kan de hostnamen van de API Management-service niet bijwerken. De aanvraag voor de https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0 resource is mislukt met status code: Niet toegestaan voor aanvraag-{0}:. Uitzonderings bericht: De bewerking heeft een ongeldige status code ' verboden ' geretourneerd.

## <a name="cause"></a>Oorzaak

De API Management-service heeft geen machtiging voor toegang tot de sleutel kluis die u wilt gebruiken voor het aangepaste domein.

## <a name="solution"></a>Oplossing

Volg deze stappen om dit probleem op te lossen:

1. Ga naar de [Azure Portal](Https://portal.azure.com), selecteer uw API Management exemplaar en selecteer vervolgens **beheerde identiteiten**. Zorg ervoor dat de optie **registreren met Azure Active Directory** is ingesteld op **Ja**. 
    ![Registreren bij Azure Active Director](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. Open in de Azure Portal de service **sleutel kluizen** en selecteer de sleutel kluis die u wilt gebruiken voor het aangepaste domein.
1. Selecteer **toegangs beleid**en controleer of er een Service-Principal is die overeenkomt met de naam van het API Management service-exemplaar. Als dat het geval is, selecteert u de Service-Principal en zorgt u ervoor dat de machtiging ' **Get** ' wordt weer gegeven onder **geheime machtigingen**.  
    ![Toegangs beleid voor Service-Principal toevoegen](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Als de API Management-service zich niet in de lijst bevindt, selecteert u **toegangs beleid toevoegen**en maakt u vervolgens het volgende toegangs beleid:
    - **Configureren op basis van sjabloon**: Geen
    - **Principal selecteren**: Zoek de naam van de API Management-service en selecteer deze in de lijst
    - **Sleutel machtigingen**: Geen
    - **Geheime machtigingen**: Ophalen
    - **Certificaat machtigingen**: Geen
1. Selecteer **OK** om het toegangs beleid te maken.
1. Selecteer **opslaan** de wijzigingen op te slaan.

Controleer of het probleem opgelost is. Om dit te doen, probeert u het aangepaste domein te maken in de API Management-service door het Key Vault certificaat te gebruiken.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over API Management-service:

- Bekijk meer [Video's](https://azure.microsoft.com/documentation/videos/index/?services=api-management) over API management.
* Zie [wederzijdse verificatie van certificaten](api-management-howto-mutual-certificates.md)voor andere manieren om uw back-end-service te beveiligen.

* [Een API Management service-exemplaar maken](get-started-create-service-instance.md).
* [Uw eerste API beheren](import-and-publish.md).