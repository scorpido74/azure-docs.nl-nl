---
title: Een virtuele Linux-machine maken en versleutelen met de Azure Portal
description: In deze Quick Start leert u hoe u de Azure Portal kunt gebruiken om een virtuele Linux-machine te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 83446d30de17973352e836d6107ad545b2da63e3
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72550310"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Snelstartgids: een virtuele machine maken en versleutelen met de Azure Portal

Virtuele Azure-machines (VM's) kunnen gemaakt worden via Azure Portal. De Azure-portal is een gebruikersinterface op basis van een browser voor het maken van VM's en alle verwante resources. In deze Quick Start gebruikt u de Azure Portal voor het implementeren van een virtuele Linux-machine (VM) met Ubuntu 18,04 LTS, het maken van een sleutel kluis voor de opslag van versleutelings sleutels en het versleutelen van de virtuele machine.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

1. Selecteer de optie **Een resource maken** in de linkerbovenhoek van Azure Portal
1. Typ **Sleutelkluis** in het zoekvak.
1. Selecteer in de lijst met resultaten **Key Vault**.
1. Selecteer **maken**in de sectie Key Vault.
1. Kies in het scherm **sleutel kluis maken** een unieke naam voor uw nieuwe sleutel kluis.

    > [!Important]
    > Elk Key Vault moet een unieke naam hebben. In het volgende voor beeld wordt een Key Vault gemaakt met de naam *myADEKV*, maar u moet iets anders een naam hebben.

1. Selecteer een **abonnement**.
1.  Selecteer onder **resource groep**de optie **nieuwe maken**. Typ in het pop-upvenster *myResourceGroup* als naam voor de resourcegroep en kies **OK**. 

    ![Scherm voor het maken van de resource groep](./media/disk-encryption/portal-qs-keyvaultcreation.png)

1. Kies in het vervolg keuzemenu **locatie** de optie **VS Oost**.
1. Houd voor de overige opties de standaardwaarden aan.
1. Selecteer ' toegangs beleid ', waarmee u naar een nieuw scherm gaat.
1. Schakel het selectie vakje in naast toegang tot Azure Disk Encryption inschakelen voor volume versleuteling.

    ![Scherm ResourceGroup maken](./media/disk-encryption/portal-qs-keyvault-enable-encryption.png)

1. Klik onder aan het scherm toegangs beleid op ' bekijken + maken '.
1. Klik na het controleren op maken.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Kies in de linkerbovenhoek van de Azure-portal **Een resource maken**.

1. Op de nieuwe pagina, onder populair, selecteert u **Ubuntu Server 18,04 LTS**.
1. Controleer op het tabblad **basis beginselen** onder **Project Details**of het juiste abonnement is geselecteerd.
1. Voor **resource groep**selecteert u de resource groep die u hebt gemaakt bij het maken van uw sleutel kluis hierboven (bijvoorbeeld **myResourceGroup**)
1. Voer *MyVM*in bij **naam van virtuele machine** en kies 
1. Selecteer voor **regio**dezelfde regio die u hebt gebruikt bij het maken van uw sleutel kluis (bijvoorbeeld **VS-Oost**).
1. Zorg ervoor dat de grootte *standaard D2s v3*is.
1. Selecteer **wacht woord**onder **Administrator-account**. Voer een gebruikers naam en wacht woord in.
    ![ResourceGroup scherm maken ](./media/disk-encryption/portal-qs-vm-creation.png)
1. Selecteer het tabblad beheer en controleer of u een diagnostische opslag account hebt. Als u geen opslag accounts hebt, selecteert u nieuwe maken, geeft u het nieuwe account een naam en selecteert u OK ![ResourceGroup scherm maken ](./media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Klik op ' bekijken + maken '.
1. Op de pagina **Een virtuele machine maken** ziet u de details van de virtuele machine die u gaat maken. Wanneer u klaar bent, selecteert u **Maken**.

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd. Wanneer de implementatie is voltooid, gaat u verder naar de volgende sectie.

## <a name="encrypt-the-virtual-machine"></a>De virtuele machine versleutelen

1. Wanneer de VM-implementatie is voltooid, selecteert **u naar resource**.
1. Selecteer op de zijbalk links de optie **schijven**.
1. Selecteer op het scherm schijven de optie **versleuteling**. 

    ![schijven en versleutelings selectie](./media/disk-encryption/portal-qs-disks-to-encryption.png)

1. Kies op het scherm versleuteling onder **schijven die moeten worden versleuteld, het** **besturings systeem en de gegevens schijven**.
1. Klik onder **versleutelings instellingen**op ' Selecteer een sleutel kluis en sleutel voor versleuteling '.
1. Selecteer in de zijbalk aan de rechter kant de naam van de sleutel kluis die u eerder hebt gemaakt als de waarde voor **sleutel kluis**en klik op **selecteren**.

    ![schijven en versleutelings selectie](./media/disk-encryption/portal-qs-encrypt-vm-screen.png)
1. Klik boven aan het scherm voor versleuteling op opslaan. Er wordt een waarschuwing weer gegeven dat de VM opnieuw moet worden opgestart. Klik op **Ja**.


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. Als u dit wilt doen, selecteert u de resource groep voor de virtuele machine, selecteert u verwijderen en bevestigt u de naam van de resource groep die u wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een Key Vault gemaakt die is ingeschakeld voor versleutelings sleutels, een virtuele machine gemaakt en de virtuele machine ingeschakeld voor versleuteling.  

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
