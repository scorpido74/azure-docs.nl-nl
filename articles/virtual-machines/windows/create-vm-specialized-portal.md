---
title: Een Windows-VM maken op basis van een gespecialiseerde VHD in de Azure-portal
description: Maak een nieuwe Windows VM van een VHD in de Azure-portal.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: d360ac5a57fe72b092a6694721905c066527bba3
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086450"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Een VM maken van een VHD met behulp van de Azure-portal

Er zijn verschillende manieren om een virtuele machine (VM) te maken in Azure: 

- Als u al een virtuele harde schijf (VHD) te gebruiken of u wilt de VHD te kopiëren van een bestaande VM te gebruiken, u een nieuwe VM door *het koppelen van* de VHD aan de nieuwe VM als een OS-schijf. 

- U een nieuwe VM maken van de VHD van een VM die is verwijderd. Als u bijvoorbeeld een Azure-vm hebt die niet goed werkt, u de vm verwijderen en de VHD gebruiken om een nieuwe vm te maken. U dezelfde VHD opnieuw gebruiken of een kopie van de VHD maken door een momentopname te maken en vervolgens een nieuwe beheerde schijf van de momentopname te maken. Hoewel het maken van een momentopname nog een paar stappen duurt, behoudt het de originele VHD en biedt het u een terugval.

- Neem een klassieke VM en gebruik de VHD om een nieuwe VM te maken die gebruik maakt van het Implementatiemodel resourcebeheer en beheerde schijven. Voor de beste resultaten **stopt u** de klassieke VM in de Azure-portal voordat u de momentopname maakt.
 
- U een Azure VM maken vanuit een on-premises VHD door de on-premises VHD te uploaden en aan een nieuwe VM te koppelen. U gebruikt PowerShell of een ander hulpmiddel om de VHD naar een opslagaccount te uploaden en vervolgens een beheerde schijf van de VHD te maken. Zie [Een gespecialiseerde VHD uploaden](create-vm-specialized.md#option-2-upload-a-specialized-vhd)voor meer informatie. 

Gebruik geen gespecialiseerde schijf als u meerdere VM's wilt maken. Maak in plaats daarvan voor grotere implementaties [een afbeelding](capture-image-resource.md) en gebruik die afbeelding om [meerdere VM's te maken.](create-vm-generalized-managed.md)

We raden u aan het aantal gelijktijdige implementaties te beperken tot 20 VM's van één momentopname of VHD. 

## <a name="copy-a-disk"></a>Een schijf kopiëren

Maak een momentopname en maak vervolgens een schijf van de momentopname. Met deze strategie u de originele VHD als terugval behouden:

1. Selecteer in de [Azure-portal](https://portal.azure.com)in het linkermenu **Alle services**.
2. Voer in het zoekvak **Alle services** **schijven** in en selecteer **Schijven** om de lijst met beschikbare schijven weer te geven.
3. Selecteer de schijf die u wilt gebruiken. De pagina **Schijf** voor die schijf wordt weergegeven.
4. Selecteer in het menu bovenaan de optie **Momentopname maken**. 
5. Voer een **naam** voor de momentopname in.
6. Kies een **resourcegroep** voor de momentopname. U een bestaande resourcegroep gebruiken of een nieuwe groep maken.
7. Kies **voor accounttype** **standaardopslag (HDD)** of **Premium (SSD).**
8. Wanneer u klaar bent, selecteert u **Maken** om de momentopname te maken.
9. Nadat de momentopname is gemaakt, selecteert u **Een resource maken** in het linkermenu.
10. Voer in het zoekvak **beheerde schijf** in en selecteer **Beheerde schijven** in de lijst.
11. Selecteer op de pagina **Beheerde schijven** de optie **Maken**.
12. Voer een **naam** voor de schijf in.
13. Kies een **resourcegroep** voor de schijf. U een bestaande resourcegroep gebruiken of een nieuwe groep maken. Deze selectie wordt ook gebruikt als de resourcegroep waarin u de VM van de schijf maakt.
14. Kies **voor accounttype** **standaardopslag (HDD)** of **Premium (SSD).**
15. Controleer in **Brontype**dat **Momentopname** is geselecteerd.
16. Selecteer in de vervolgkeuzelijst **Bronmomentopname** de momentopname die u wilt gebruiken.
17. Voer indien nodig andere aanpassingen aan en selecteer **Vervolgens Maken** om de schijf te maken.

## <a name="create-a-vm-from-a-disk"></a>Een VM maken vanaf een schijf

Nadat u de beheerde schijf VHD hebt die u wilt gebruiken, u de VM in de portal maken:

1. Selecteer in de [Azure-portal](https://portal.azure.com)in het linkermenu **Alle services**.
2. Voer in het zoekvak **Alle services** **schijven** in en selecteer **Schijven** om de lijst met beschikbare schijven weer te geven.
3. Selecteer de schijf die u wilt gebruiken. De pagina **Schijf** voor die schijf wordt geopend.
4. Controleer op de pagina **Overzicht** of **schijfstatus** wordt vermeld als **Ontkoppeld**. Als dit niet het is, moet u de schijf mogelijk loskoppelen van de vm of de VM verwijderen om de schijf vrij te maken.
4. Selecteer **VM maken**in het menu boven aan de pagina .
5. Voer op de pagina **Basisbeginselen** voor de nieuwe virtuele machinenaam een **virtuele machinenaam** in en selecteer een bestaande **resourcegroep** of maak een nieuwe groep.
6. Selecteer Bij **Grootte**de **optie Grootte wijzigen** om toegang te krijgen tot de pagina **Grootte.**
7. Selecteer een rij met VM-grootte en kies **Selecteer .**
8. Op **de** netwerkpagina u de portal alle nieuwe bronnen laten maken of een bestaande **virtuele netwerk-** en **netwerkbeveiligingsgroep**selecteren. De portal creëert altijd een nieuwe netwerkinterface en een openbaar IP-adres voor de nieuwe VM. 
9. Breng op de pagina **Beheer** eventuele wijzigingen aan in de bewakingsopties.
10. Voeg op de pagina **Gastconfig** desgewenst extensies toe.
11. Wanneer u klaar bent, selecteert u **Controleren + maken**. 
12. Als de VM-configuratie de validatie doorstaat, selecteert u **Maken** om de implementatie te starten.


## <a name="next-steps"></a>Volgende stappen

U PowerShell ook gebruiken om [een VHD naar Azure te uploaden en een gespecialiseerde VM te maken.](create-vm-specialized.md)


