---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903710"
---
Maar als u afbeeldingen buiten uw Azure-tenant wilt delen, moet u op schaal een app-registratie maken om het delen te vergemakkelijken.  Als u een app-registratie gebruikt, kunnen complexere scenario's voor delen mogelijk worden, zoals: 

* Gedeelde afbeeldingen beheren wanneer het ene bedrijf een ander bedrijf verwerft en de Azure-infrastructuur is verspreid over afzonderlijke tenants. 
* Azure Partners beheren Azure-infrastructuur namens hun klanten. Het aanpassen van afbeeldingen gebeurt binnen de partnertenant, maar de implementaties van de infrastructuur vinden plaats in de tenant van de klant. 


## <a name="create-the-app-registration"></a>De app-registratie maken

Maak een toepassingsregistratie die door beide tenants wordt gebruikt om de bronnen van de afbeeldingsgalerie te delen.
1. Open de [app-registraties (voorbeeld) in de Azure-portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Selecteer **Nieuwe registratie** in het menu boven aan de pagina.
1. Typ *myGalleryApp*in **naam**.
1. Selecteer **accounts in een organisatiemap en persoonlijke Microsoft-accounts in**ondersteunde **accounttypen**.
1. Typ in Uri *https://www.microsoft.com* **omleiden**en selecteer Vervolgens **Registreren**. Nadat de app-registratie is gemaakt, wordt de overzichtspagina geopend.
1. Kopieer op de overzichtspagina de **id van toepassing (client)** en spaar later voor gebruik.   
1. Selecteer **Certificaten & geheimen**en selecteer Vervolgens Nieuw **clientgeheim**.
1. Typ in Beschrijving *het geheim van de gedeelde afbeeldingsgalerie met meerdere tenant 's..* **Description**
1. Laat **in Verloopt**de standaardwaarde van In 1 **jaar** achter en selecteer Vervolgens **Toevoegen**.
1. Kopieer de waarde van het geheim en bewaar het op een veilige plaats. U het niet ophalen nadat u de pagina hebt verlaten.


Geef de app-registratie toestemming om de gedeelde afbeeldingsgalerie te gebruiken.
1. Selecteer in de Azure-portal de gedeelde afbeeldingsgalerie die u met een andere tenant wilt delen.
1. Selecteer **Access-besturingselement (IAM)** en selecteer onder **Roltoewijzing toevoegen** *selecteer Toevoegen*. 
1. Selecteer **Reader**onder **Rol**.
1. Onder **Toegang toewijzen aan:** Laat dit als **Azure AD-gebruiker, groep of serviceprincipal .**
1. Typ *myGalleryApp* **onder Selecteren**en selecteer deze wanneer deze in de lijst wordt weergegeven. Selecteer **Opslaan** wanneer u klaar bent.


## <a name="give-tenant-2-access"></a>Tenant 2 toegang geven

Geef Tenant 2 toegang tot de toepassing door een aanmelding aan te vragen via een browser. Vervang * \<Tenant2-id->* door de tenant-id voor de tenant waarmee u uw afbeeldingsgalerie wilt delen. De * \<>van de toepassings-id* vervangen door de toepassings-id van de app-registratie die u hebt gemaakt. Wanneer u klaar bent met het maken van de vervangingen, plakt u de URL in een browser en volgt u de aanmeldingsprompts om u aan te melden bij Tenant 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

Geef de app-registratie in de [Azure-portal](https://portal.azure.com) aanmelden als Tenant 2 en geef de app-registratie toegang tot de brongroep waar u de VM wilt maken.

1. Selecteer de brongroep en selecteer **vervolgens Toegangsbeheer (IAM)**. Selecteer **onder Roltoewijzing toevoegen** selecteer **Toevoegen**. 
1. Typ **Contribuant**onder **Rol**.
1. Onder **Toegang toewijzen aan:** Laat dit als **Azure AD-gebruiker, groep of serviceprincipal .**
1. Selecteer onder **Type** *myGalleryApp* selecteren en selecteer deze wanneer deze in de lijst wordt weergegeven. Selecteer **Opslaan** wanneer u klaar bent.

> [!NOTE]
> U moet wachten tot de afbeeldingsversie volledig is voltooid en wordt gerepliceerd voordat u dezelfde beheerde afbeelding gebruiken om een andere afbeeldingsversie te maken.

