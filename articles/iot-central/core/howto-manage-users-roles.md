---
title: Gebruikers en rollen beheren in Azure IoT Central-toepassing | Microsoft Documenten
description: Als beheerder u gebruikers en rollen beheren in uw Azure IoT Central-toepassing
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: c00f9d8baa55ef0d0cf6322ee71f22e739e6acdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365501"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gebruikers en rollen beheren in uw IoT Central-toepassing

In dit artikel wordt beschreven hoe u als beheerder gebruikers toevoegen, bewerken en verwijderen in uw Azure IoT Central-toepassing. In het artikel wordt ook beschreven hoe u rollen in uw Azure IoT Central-toepassing beheert.

Als u de sectie **Beheer** wilt openen en gebruiken, moet u zich in de **functie Administrator** bevinden voor een Azure IoT Central-toepassing. Als u een Azure IoT Central-toepassing maakt, wordt u automatisch toegevoegd aan de **beheerdersrol** voor die toepassing.

## <a name="add-users"></a>Gebruikers toevoegen

Elke gebruiker moet een gebruikersaccount hebben voordat hij zich kan aanmelden en toegang heeft tot een Azure IoT Central-toepassing. Microsoft-accounts en Azure Active Directory-accounts worden ondersteund in Azure IoT Central. Azure Active Directory-groepen worden momenteel niet ondersteund in Azure IoT Central.

Zie [Help voor Microsoft-account](https://support.microsoft.com/products/microsoft-account?category=manage-account) en [Quickstart: Nieuwe gebruikers toevoegen aan Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)voor meer informatie.

1. Als u een gebruiker wilt toevoegen aan een IoT Central-toepassing, gaat u naar de pagina **Gebruikers** in de sectie **Beheer.**
    
    > [!div class="mx-imgBorder"]
    >![Gebruikers beheren](media/howto-manage-users-roles/manage-users-pnp.png)

1. Als u een gebruiker wilt toevoegen, kiest u op de pagina **Gebruikers** de optie **+ Gebruiker toevoegen**.

1. Kies een rol voor de gebruiker in het vervolgkeuzemenu **Rol.** Meer informatie over rollen in het gedeelte [Rollen beheren](#manage-roles) van dit artikel.

    > [!div class="mx-imgBorder"]
    >![Gebruiker toevoegen en een rol selecteren](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Een gebruiker die zich in een aangepaste rol bevindt en die hen de toestemming geeft om andere gebruikers toe te voegen, kan alleen gebruikers toevoegen aan een rol met dezelfde of minder machtigingen dan zijn eigen rol.

Als een IoT Central-gebruikers-id wordt verwijderd uit Azure Active Directory en vervolgens opnieuw wordt toegevoegd, kan de gebruiker zich niet aanmelden bij de IoT Central-toepassing. Om de toegang opnieuw in te schakelen, moet de IoT Central-beheerder de gebruiker in de toepassing verwijderen en lezen.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>De rollen bewerken die aan gebruikers zijn toegewezen

Rollen kunnen niet worden gewijzigd nadat ze zijn toegewezen. Als u de rol wilt wijzigen die aan een gebruiker is toegewezen, verwijdert u de gebruiker en voegt u de gebruiker opnieuw toe met een andere rol.

> [!NOTE]
> De toegewezen rollen zijn specifiek voor de IoT Central-toepassing en kunnen niet worden beheerd vanuit de Azure Portal.

## <a name="delete-users"></a>Gebruikers verwijderen

Als u gebruikers wilt verwijderen, schakelt u een of meer selectievakjes in op de pagina **Gebruikers.** Selecteer vervolgens **Verwijderen**.

## <a name="manage-roles"></a>Rollen beheren

Met rollen u bepalen wie binnen uw organisatie verschillende taken in IoT Central mag uitvoeren. Er zijn drie ingebouwde rollen die u toewijzen aan gebruikers van uw toepassing. U ook [aangepaste rollen maken](#create-a-custom-role) als u een fijnmazige besturingselement nodig hebt.

> [!div class="mx-imgBorder"]
> ![Rollenselecteren beheren](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Beheerder

Gebruikers in de rol **Administrator** kunnen elk onderdeel van de toepassing beheren en beheren, inclusief facturering.

De gebruiker die een toepassing maakt, wordt automatisch toegewezen aan de **administratorrol.** Er moet altijd ten minste één gebruiker in de **beheerdersrol** zijn.

### <a name="builder"></a>Opbouwfunctie

Gebruikers in de rol **Builder** kunnen elk onderdeel van de app beheren, maar kunnen geen wijzigingen aanbrengen op de tabbladen Beheer of Continue gegevensexporteren.

### <a name="operator"></a>Operator

Gebruikers in **de** operatorrol kunnen de status en status van het apparaat controleren. Het is niet toegestaan om wijzigingen aan te brengen in apparaatsjablonen of om de toepassing te beheren. Operators kunnen apparaten toevoegen en verwijderen, apparaatsets beheren en analyses en taken uitvoeren. 

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

Als uw oplossing toegangsbesturingselementen met fijnere korrel vereist, u aangepaste rollen maken met aangepaste machtigingensets. Als u een aangepaste rol wilt maken, navigeert u naar de pagina **Rollen** in de sectie **Beheer** van uw toepassing. Selecteer vervolgens **+ Nieuwe rol**en voeg een naam en beschrijving toe voor uw rol. Selecteer de machtigingen die uw rol vereist en selecteer **Opslaan**.

U gebruikers toevoegen aan uw aangepaste rol op dezelfde manier als gebruikers toevoegen aan een ingebouwde rol.

> [!div class="mx-imgBorder"]
> ![Een aangepaste rol bouwen](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Aangepaste rolopties

Wanneer u een aangepaste rol definieert, kiest u de set machtigingen die een gebruiker krijgt als deze lid is van de rol. Sommige machtigingen zijn afhankelijk van andere. Als u bijvoorbeeld de machtiging **Voor toepassingsdashboards bijwerken** toevoegt aan een rol, wordt de machtiging **Dashboards voor toepassingen weergeven** automatisch toegevoegd. In de volgende tabellen worden de beschikbare machtigingen en de afhankelijkheden ervan samengevat die u gebruiken bij het maken van aangepaste rollen.

#### <a name="managing-devices"></a>Apparaten beheren

**Machtigingen voor apparaatsjablonen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Beheren | Weergave <br/> Andere afhankelijkheden: apparaatinstanties weergeven  |
| Volledig beheer | Bekijken, beheren <br/> Andere afhankelijkheden: apparaatinstanties weergeven |

**Machtigingen voor apparaatinstantie**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: apparaatsjablonen en apparaatgroepen weergeven |
| Update | Weergave <br/> Andere afhankelijkheden: apparaatsjablonen en apparaatgroepen weergeven  |
| Maken | Weergave <br/> Andere afhankelijkheden: apparaatsjablonen en apparaatgroepen weergeven  |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: apparaatsjablonen en apparaatgroepen weergeven  |
| Opdrachten uitvoeren | Bijwerken, weergeven <br/> Andere afhankelijkheden: apparaatsjablonen en apparaatgroepen weergeven  |
| Volledig beheer | Opdrachten weergeven, bijwerken, maken, verwijderen, uitvoeren <br/> Andere afhankelijkheden: apparaatsjablonen en apparaatgroepen weergeven  |

**Machtigingen voor apparaatgroepen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: apparaatsjablonen en apparaatinstanties weergeven |
| Update | Weergave <br/> Andere afhankelijkheden: apparaatsjablonen en apparaatinstanties weergeven   |
| Maken | Weergeven, bijwerken <br/> Andere afhankelijkheden: apparaatsjablonen en apparaatinstanties weergeven   |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: apparaatsjablonen en apparaatinstanties weergeven   |
| Volledig beheer | Weergeven, bijwerken, maken, verwijderen <br/> Andere afhankelijkheden: apparaatsjablonen en apparaatinstanties weergeven |

**Machtigingen voor beheer van apparaatconnectiviteit**

| Name | Afhankelijkheden |
| ---- | -------- |
| Instantie lezen | Geen <br/> Andere afhankelijkheden: apparaatsjablonen, apparaatgroepen, apparaatinstanties weergeven |
| Instantie beheren | Geen |
| Lees globaal | Geen   |
| Globale beheren | Lees Globaal |
| Volledig beheer | Lees bijvoorbeeld, instantie beheren, Globaal lezen, Globaal beheren. <br/> Andere afhankelijkheden: apparaatsjablonen, apparaatgroepen, apparaatinstanties weergeven |

**Machtigingen voor taken**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: apparaatsjablonen, apparaatinstanties en apparaatgroepen weergeven |
| Update | Weergave <br/> Andere afhankelijkheden: apparaatsjablonen, apparaatinstanties en apparaatgroepen weergeven |
| Maken | Weergeven, bijwerken <br/> Andere afhankelijkheden: apparaatsjablonen, apparaatinstanties en apparaatgroepen weergeven |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: apparaatsjablonen, apparaatinstanties en apparaatgroepen weergeven |
| Uitvoeren | Weergave <br/> Andere afhankelijkheden: apparaatsjablonen, apparaatinstanties en apparaatgroepen weergeven; Apparaatexemplaren bijwerken; Opdrachten uitvoeren op apparaatinstanties |
| Volledig beheer | Weergeven, bijwerken, maken, verwijderen, uitvoeren <br/> Andere afhankelijkheden: apparaatsjablonen, apparaatinstanties en apparaatgroepen weergeven; Apparaatexemplaren bijwerken; Opdrachten uitvoeren op apparaatinstanties |

**Regels machtigingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: apparaatsjablonen weergeven |
| Update | Weergave <br/> Andere afhankelijkheden: apparaatsjablonen weergeven |
| Maken | Weergeven, bijwerken <br/> Andere afhankelijkheden: apparaatsjablonen weergeven |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: apparaatsjablonen weergeven |
| Volledig beheer | Weergeven, bijwerken, maken, verwijderen <br/> Andere afhankelijkheden: apparaatsjablonen weergeven |

#### <a name="managing-the-app"></a>De app beheren

**Machtigingen voor toepassingsinstellingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Update | Weergave   |
| Exemplaar | Weergave <br/> Andere afhankelijkheden: apparaatsjablonen, apparaatinstanties, apparaatgroepen, dashboards, gegevensexport, branding, helpkoppelingen, aangepaste rollen, regels weergeven |
| Verwijderen | Weergave   |
| Volledig beheer | Weergeven, bijwerken, kopiëren, verwijderen <br/> Andere afhankelijkheden: apparaatsjablonen, apparaatgroepen, toepassingsdashboards, gegevensexport, branding, helpkoppelingen, aangepaste rollen, regels weergeven |

**Exportmachtigingen voor toepassingssjablonen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Exporteren | Weergave <br/> Andere afhankelijkheden: apparaatsjablonen, apparaatinstanties, apparaatgroepen, dashboards, gegevensexport, branding, helpkoppelingen, aangepaste rollen, regels weergeven |
| Volledig beheer | Bekijken, exporteren <br/> Andere afhankelijkheden: apparaatsjablonen, apparaatgroepen, toepassingsdashboards, gegevensexport, branding, helpkoppelingen, aangepaste rollen, regels weergeven |

**Factureringsmachtigingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Beheren | Geen     |
| Volledig beheer | Beheren |

#### <a name="managing-users-and-roles"></a>Gebruikers en rollen beheren

**Machtigingen voor aangepaste rollen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen |
| Update | Weergave |
| Maken | Weergeven, bijwerken |
| Verwijderen | Weergave |
| Volledig beheer | Weergeven, bijwerken, maken, verwijderen |

**Machtigingen voor gebruikersbeheer**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: aangepaste rollen weergeven |
| Toevoegen | Weergave <br/> Andere afhankelijkheden: aangepaste rollen weergeven |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: aangepaste rollen weergeven |
| Volledig beheer | Weergeven, toevoegen, verwijderen <br/> Andere afhankelijkheden: aangepaste rollen weergeven |

> [!NOTE]
> Een gebruiker die zich in een aangepaste rol bevindt en die hen de toestemming geeft om andere gebruikers toe te voegen, kan alleen gebruikers toevoegen aan een rol met dezelfde of minder machtigingen dan zijn eigen rol.

#### <a name="customizing-the-app"></a>De app aanpassen

**Machtigingen voor toepassingsdashboard**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Update | Weergave   |
| Maken | Weergeven, bijwerken |
| Verwijderen | Weergave   |
| Volledig beheer | Weergeven, bijwerken, maken, verwijderen |

**Machtigingen voor persoonlijke dashboards**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Update | Weergave   |
| Maken | Weergeven, bijwerken   |
| Verwijderen | Weergave   |
| Volledig beheer | Weergeven, bijwerken, maken, verwijderen |

**Machtigingen voor branding, favicon en kleuren**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Update | Weergave   |
| Volledig beheer | Weergeven, bijwerken |

**Machtigingen voor Help-koppelingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Update | Weergave   |
| Volledig beheer | Weergeven, bijwerken |

#### <a name="extending-the-app"></a>De app uitbreiden

**Machtigingen voor gegevensexport**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Update | Weergave   |
| Maken | Weergeven, bijwerken  |
| Verwijderen | Weergave   |
| Volledig beheer | Weergeven, bijwerken, maken, verwijderen |

**API-tokenmachtigingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Maken | Weergave   |
| Verwijderen | Weergave   |
| Volledig beheer | Weergeven, maken, verwijderen |

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u gebruikers en rollen in uw Azure IoT Central-toepassing beheren, is de voorgestelde volgende stap om te leren hoe [u uw factuur beheren.](howto-view-bill.md)
