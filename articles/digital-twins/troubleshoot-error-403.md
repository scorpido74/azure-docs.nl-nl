---
title: 'De Azure Digital Apparaatdubbels-aanvraag is mislukt met de status: 403 (verboden)'
description: "Oorzaken en oplossingen voor de service aanvraag zijn mislukt. Status: 403 (verboden) ' op Azure Digital Apparaatdubbels."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: 95372d69f58249afc946f12ec74696507429e5c6
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125282"
---
# <a name="service-request-failed-status-403-forbidden"></a>Service aanvraag mislukt. Status: 403 (verboden)

In dit artikel worden oorzaken en oplossings stappen beschreven voor het ontvangen van een 403-fout van service aanvragen naar Azure Digital Apparaatdubbels. 

## <a name="symptoms"></a>Symptomen

Deze fout kan optreden bij veel typen service aanvragen waarvoor verificatie is vereist. Het gevolg is dat de API-aanvraag mislukt en een fout status retourneert van `403 (Forbidden)` .

## <a name="causes"></a>Oorzaken

### <a name="cause-1"></a>Oorzaak #1

Deze fout geeft meestal aan dat uw RBAC-machtigingen (op rollen gebaseerd toegangs beheer) voor de service niet juist zijn ingesteld. Voor veel acties voor een Azure Digital Apparaatdubbels-exemplaar moet u de rol *Azure Digital Apparaatdubbels Owner (preview)* hebben **voor het exemplaar dat u wilt beheren**. 

### <a name="cause-2"></a>Oorzaak #2

Als u een client-app gebruikt om te communiceren met Azure Digital Apparaatdubbels, kan deze fout optreden omdat uw [Azure Active Directory (Azure AD)-app-](../active-directory/fundamentals/active-directory-whatis.md) registratie geen machtigingen heeft ingesteld voor de Azure Digital apparaatdubbels-service.

Voor de app-registratie is vereist dat er toegangs machtigingen zijn geconfigureerd voor de Azure Digital Apparaatdubbels-Api's. Wanneer uw client-app vervolgens wordt geverifieerd op basis van de app-registratie, worden de machtigingen verleend die de app-registratie heeft geconfigureerd.

## <a name="solutions"></a>Oplossingen

### <a name="solution-1"></a>#1 oplossing

De eerste oplossing is om te controleren of uw Azure-gebruiker de rol _**Azure Digital Apparaatdubbels Owner (preview)**_ heeft voor het exemplaar dat u wilt beheren. Als u deze functie niet hebt, stelt u deze in.

Houd er rekening mee dat deze rol afwijkt van...
* de rol van *eigenaar* van het hele Azure-abonnement. De *Azure Digital apparaatdubbels-eigenaar (preview)* is een rol binnen Azure Digital apparaatdubbels en is afgestemd op dit individuele Azure Digital apparaatdubbels-exemplaar.
* de rol van *eigenaar* in azure Digital apparaatdubbels. Dit zijn twee verschillende Azure Digital Apparaatdubbels management-rollen en de *Azure Digital Apparaatdubbels Owner (preview)* is de rol die tijdens de preview-periode moet worden gebruikt voor beheer.

#### <a name="check-current-setup"></a>Huidige instellingen controleren

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Problemen oplossen 

Als u deze roltoewijzing niet hebt, moet iemand met een eigenaar van uw Azure- **abonnement** de volgende opdracht uitvoeren om uw Azure-gebruiker de rol van Azure *Digital Apparaatdubbels Owner (preview)* te geven op de **Azure Digital apparaatdubbels-instantie**. 

Als u een eigenaar van het abonnement bent, kunt u deze opdracht zelf uitvoeren. Als dat niet het geval is, neemt u contact op met een eigenaar om deze opdracht namens u uit te voeren.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Voor meer informatie over deze functie vereiste en het toewijzings proces, zie de [sectie *toegangs machtigingen van uw gebruiker instellen* ](how-to-set-up-instance-manual.md#set-up-your-users-access-permissions) voor *instructies: een exemplaar en authenticatie instellen (hand matig)*.

Als u deze roltoewijzing al hebt en nog steeds het 403-probleem ondervindt, gaat u verder met de volgende oplossing.

### <a name="solution-2"></a>#2 oplossing

De tweede oplossing is om te controleren of de Azure AD-App-registratie machtigingen heeft die zijn geconfigureerd voor de Azure Digital Apparaatdubbels-service. Als deze niet is geconfigureerd, stelt u deze in op.

#### <a name="check-current-setup"></a>Huidige instellingen controleren

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Controleer eerst of de instellingen voor de Azure Digital Apparaatdubbels-machtigingen correct zijn ingesteld voor de registratie. Als u dit wilt doen, selecteert u *manifest* in de menu balk om de manifest code van de app-registratie weer te geven. Ga naar de onderkant van het code venster en zoek deze velden onder `requiredResourceAccess` . De waarden moeten overeenkomen met die in de onderstaande scherm afbeelding:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

#### <a name="fix-issues"></a>Problemen oplossen

Als een van deze voor beeld anders lijkt dan beschreven, volgt u de instructies voor het instellen van een app-registratie in het [gedeelte *toegangs machtigingen instellen voor client toepassingen* ](how-to-set-up-instance-manual.md#set-up-access-permissions-for-client-applications) van *instructies: een exemplaar en verificatie instellen (hand matig)*.

## <a name="next-steps"></a>Volgende stappen

Lees de installatie stappen voor het maken en verifiëren van een nieuw exemplaar van Azure Digital Apparaatdubbels:
* [*Instructies: een exemplaar en authenticatie instellen (hand matig)*](how-to-set-up-instance-manual.md)

Lees meer over beveiliging en machtigingen op Azure Digital Apparaatdubbels:
* [*Concepten: beveiliging voor Azure Digital Apparaatdubbels Solutions*](concepts-security.md)