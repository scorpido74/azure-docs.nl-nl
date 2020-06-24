---
title: Op Azure Role gebaseerde Access Control (RBAC) voor Azure API configureren voor FHIR
description: In dit artikel wordt beschreven hoe u Azure RBAC kunt configureren voor de Azure API voor FHIR-gegevens vlak
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: mihansen
ms.openlocfilehash: bf05ede858c86fa82e199c800ea42747745c9c19
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "84871915"
---
# <a name="configure-azure-rbac-for-fhir"></a>Azure RBAC configureren voor FHIR 

In dit artikel leert u hoe u op [Azure Role Based Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/) kunt gebruiken om toegang toe te wijzen aan de Azure API voor FHIR data-vlak. Azure RBAC is de voorkeurs methoden voor het toewijzen van toegang tot gegevens vlak wanneer gegevenslaag gebruikers worden beheerd in de Azure Active Directory Tenant die aan uw Azure-abonnement is gekoppeld. Als u een externe Azure Active Directory Tenant gebruikt, raadpleegt u de [lokale RBAC-toewijzings verwijzing](configure-local-rbac.md).

## <a name="confirm-azure-rbac-mode"></a>De Azure RBAC-modus bevestigen

Als u Azure RBAC wilt gebruiken, moet uw Azure-API voor FHIR worden geconfigureerd om de Tenant van uw Azure-abonnement te gebruiken voor gegevens vlak en er mag geen id-object-Id's zijn toegewezen. U kunt uw instellingen controleren door de Blade **verificatie** van de Azure API voor FHIR te controleren:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="De Azure RBAC-modus bevestigen":::

De **instantie** moet worden ingesteld op de Azure Active Directory-Tenant die is gekoppeld aan uw abonnement en er mogen geen guid's zijn in het vak met de naam **toegestane object-id's**. U ziet ook dat het vak is uitgeschakeld en een label geeft aan dat Azure RBAC moet worden gebruikt voor het toewijzen van rollen van het gegevens vlak.

## <a name="assign-roles"></a>Rollen toewijzen

Als u gebruikers, service-principals of groepen toegang tot het FHIR-gegevens vlak wilt verlenen, klikt u op **toegangs beheer (IAM)**, klikt u vervolgens op **Roltoewijzingen en klikt** u op **+ toevoegen**:

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Toewijzing van Azure RBAC-rol toevoegen":::

Zoek in de selectie van de **rol** naar een van de ingebouwde rollen voor het FHIR-gegevens vlak:

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="Ingebouwde FHIR-gegevens rollen":::

U kunt kiezen uit:

* FHIR-gegevens lezer: kan FHIR-gegevens lezen (en zoeken).
* FHIR-gegevens schrijver: kan FHIR-gegevens lezen, schrijven en zacht verwijderen.
* FHIR-gegevens exporteur: kan operator gegevens lezen en exporteren `$export` .
* FHIR data contributor: kan alle gegevensvlak bewerkingen uitvoeren.

Als deze rollen niet voldoende zijn voor uw behoeften, kunt u ook [aangepaste rollen maken](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell).

Zoek in het **selectie** vakje naar een gebruiker, Service-Principal of groep waaraan u de rol wilt toewijzen.

## <a name="caching-behavior"></a>Cache gedrag

De Azure API voor FHIR neemt beslissingen tot vijf minuten in de cache. Als u een gebruiker toegang verleent tot de FHIR-server door deze toe te voegen aan de lijst met toegestane object-Id's, of als u ze uit de lijst verwijdert, moet u er rekening mee houden dat het tot vijf minuten duurt voordat wijzigingen in de machtigingen worden door gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Azure RBAC-rollen kunt toewijzen voor het FHIR-gegevens vlak. Meer informatie over aanvullende instellingen voor de Azure API voor FHIR:
 
>[!div class="nextstepaction"]
>[Aanvullende instellingen Azure API voor FHIR](azure-api-for-fhir-additional-settings.md)

