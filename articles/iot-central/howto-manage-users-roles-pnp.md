---
title: Gebruikers en rollen beheren in azure IoT Central-toepassing | Microsoft Docs
description: Als Administrator, het beheren van gebruikers en rollen in uw Azure IoT Central-toepassing
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0c1a6b7370a4d1f8e01b22bfd52caa6cb6973947
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880642"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gebruikers en rollen beheren in uw IoT Central-toepassing

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

In dit artikel wordt beschreven hoe u, als beheerder, gebruikers in uw Azure IoT Central-toepassing kunt toevoegen, bewerken en verwijderen en ook hoe u rollen in uw Azure IoT Central-toepassing beheert.

Voor toegang tot en gebruik van de sectie **beheer** moet u de rol **beheerder** hebben voor een Azure IOT Central-toepassing. Als u een Azure IoT Central-toepassing maakt, wordt u automatisch toegewezen aan de beheerdersrol voor die toepassing.


## <a name="add-users"></a>Gebruikers toevoegen

Elke gebruiker moet een gebruikers account hebben voordat ze zich kunnen aanmelden en toegang hebben tot een Azure IoT Central-toepassing. Micro soft-accounts (Msa's) en Azure Active Directory (Azure AD) worden ondersteund in azure IoT Central. Azure Active Directory-groepen worden momenteel niet ondersteund in azure IoT Central.

Zie [Microsoft-account Help](https://support.microsoft.com/products/microsoft-account?category=manage-account) en [Quick start voor meer informatie: Nieuwe gebruikers toevoegen aan Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory) als een testgebruiker zonder beheerdersbevoegdheden een wachtwoord heeft dat u kent, en u een gebruiker moet maken.

1. Als u een gebruiker aan een IoT Central-toepassing wilt toevoegen, gaat u naar de pagina **gebruikers** in de sectie **beheer** .

    ![Lijst met gebruikers](media/howto-manage-users-roles-pnp/image1.png)

1. Als u een gebruiker wilt toevoegen, klikt u op de pagina **gebruikers** op **+ gebruiker toevoegen**.

1. Kies een rol voor de gebruiker in de vervolg keuzelijst **rol** . Meer informatie over rollen vindt u in de sectie [rollen beheren](#manage-roles) van dit artikel.

    ![Geselecteerde rol](media/howto-manage-users-roles-pnp/image3.png)

    > [!NOTE]
    >  Als u gebruikers wilt toevoegen in bulk, voert u de gebruikers-Id's in van alle gebruikers die u wilt toevoegen, gescheiden door punt komma's. Kies een rol in de vervolg keuzelijst **rol** . Selecteer vervolgens **Opslaan**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>De rollen bewerken die aan gebruikers zijn toegewezen

Rollen kunnen niet worden gewijzigd nadat ze zijn toegewezen. Als u de rol die is toegewezen aan een gebruiker wilt wijzigen, verwijdert u de gebruiker en voegt u de gebruiker opnieuw toe met een andere rol.

> [!NOTE]
> De toegewezen rollen zijn specifiek voor IoT Central toepassing en kunnen niet worden beheerd vanuit de Azure-Portal.

## <a name="delete-users"></a>Gebruikers verwijderen

Als u gebruikers wilt verwijderen, schakelt u een of meer selectie vakjes op de pagina **gebruikers** in. Selecteer vervolgens **Verwijderen**.

## <a name="manage-roles"></a>Rollen beheren

Met rollen kunt u bepalen wie binnen uw organisatie verschillende taken in IoT Central kan uitvoeren. Er zijn drie rollen die u aan gebruikers van uw toepassing kunt toewijzen.

### <a name="administrator"></a>Beheerder

Gebruikers met de rol **beheerder** hebben toegang tot alle functies in een toepassing.

De gebruiker die een toepassing maakt, wordt automatisch toegewezen aan de rol **beheerder** . Er moet altijd ten minste één gebruiker in de beheerdersrol zijn.

### <a name="application-builder"></a>Toepassingsontwikkelaar

Gebruikers met de rol **toepassings opbouw** kunnen alles in een toepassing doen, met uitzonde ring van de toepassing beheren. Bouwers kunnen sjablonen en apparaten voor apparaten maken, bewerken en verwijderen, Apparaatsets beheren en analyses en taken uitvoeren. Bouwers hebben geen toegang tot het gedeelte **beheer** van de toepassing.

### <a name="application-operator"></a>Toepassingsoperator

Gebruikers met de rol **toepassings operator** kunnen geen wijzigingen aanbrengen in apparaatprofielen en kunnen de toepassing niet beheren. Opera tors kunnen apparaten toevoegen en verwijderen, Apparaatsets beheren en analyses en taken uitvoeren. Opera tors hebben geen toegang tot de pagina's voor **toepassings Builder** en **beheer** .

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over het beheren van gebruikers en rollen in uw Azure IoT Central, is de voorgestelde volgende stap meer informatie over [het weer geven van uw factuur](howto-view-bill-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) in azure IOT Central.
