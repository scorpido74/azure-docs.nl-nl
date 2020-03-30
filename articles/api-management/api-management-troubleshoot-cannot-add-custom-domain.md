---
title: Kan geen aangepast domein toevoegen met het Key Vault-certificaat
titleSuffix: Azure API Management
description: Meer informatie over het oplossen van het probleem waarbij u geen aangepast domein toevoegen aan Azure API Management met behulp van een key vault-certificaat.
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430581"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Kan api-servicehostnamen van API Management-service niet bijwerken

In dit artikel wordt de fout 'Kan u API Management-hostnamen niet bijwerken' beschreven die u ondervinden wanneer u een aangepast domein toevoegt voor de Azure API Management-service. In dit artikel vindt u stappen voor het oplossen van problemen om het probleem op te lossen.

## <a name="symptoms"></a>Symptomen

Wanneer u een aangepast domein voor uw API-beheerservice probeert toe te voegen met een certificaat uit Azure Key Vault, ontvangt u het volgende foutbericht:

- Kan de hostnamen van API Management-service niet bijwerken. Aanvraag omhttps://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0resources ' ' mislukt met StatusCode: Verboden voor RequestId: . Uitzonderingsbericht: Bewerking heeft een ongeldige statuscode 'Verboden' geretourneerd.

## <a name="cause"></a>Oorzaak

De API Management-service heeft geen toestemming om toegang te krijgen tot de sleutelkluis die u probeert te gebruiken voor het aangepaste domein.

## <a name="solution"></a>Oplossing

Volg deze stappen om dit probleem op te lossen:

1. Ga naar de [Azure-portal,](Https://portal.azure.com)selecteer de instantie API Beheer en selecteer **Beheerde identiteiten**. Controleer of de optie **Registreren met Azure Active Directory** is ingesteld op **Ja**. 
    ![Registreren bij Azure Active Director](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. Open in de Azure-portal de service **Sleutelkluizen** en selecteer de sleutelkluis die u probeert te gebruiken voor het aangepaste domein.
1. Selecteer **Toegangsbeleid**en controleer of er een serviceprincipal is die overeenkomt met de naam van de API-beheerserviceinstantie. Als dat het is, selecteert u de serviceprincipal en controleert u of de machtiging **Opvragen** wordt vermeld onder **Geheime machtigingen**.  
    ![Toegangsbeleid toevoegen voor serviceprincipal](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Als de API-beheerservice niet in de lijst staat, selecteert u **Toegangsbeleid toevoegen**en maakt u vervolgens het volgende toegangsbeleid:
    - **Configureren vanuit sjabloon:** Geen
    - **Hoofdmap selecteren:** zoek op de naam van de API-beheerservice en selecteer deze vervolgens in de lijst
    - **Belangrijkste machtigingen:** Geen
    - **Geheime machtigingen:** Get
    - **Certificaatmachtigingen**: Geen
1. Selecteer **OK** om het toegangsbeleid te maken.
1. Selecteer **Opslaan** om de wijzigingen op te slaan.

Controleer of het probleem is opgelost. Probeer hiervoor het aangepaste domein in de API-beheerservice te maken met behulp van het Key Vault-certificaat.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over apibeheerservice:

- Bekijk meer [video's](https://azure.microsoft.com/documentation/videos/index/?services=api-management) over API Management.
* Zie [Verificatie van wederzijds certificaat](api-management-howto-mutual-certificates.md)voor andere manieren om uw back-endservice te beveiligen.

* [Een API-beheerservice-instantie maken](get-started-create-service-instance.md).
* [Beheer uw eerste API.](import-and-publish.md)