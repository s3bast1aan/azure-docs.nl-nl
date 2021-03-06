---
title: Azure Storage toevoegen met behulp van verbonden Services in Visual Studio | Microsoft Docs
description: Azure Storage toevoegen aan uw app met behulp van de Visual Studio verbonden dialoogvenster Services toevoegen
services: visual-studio-online
author: ghogen
manager: douge
assetId: 521ec044-ad4b-4828-8864-01decde2e758
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/26/2017
ms.author: ghogen
ms.openlocfilehash: 3c5d3dc1d91a6f8bb1816b2985f86ec5c4a12e63
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793593"
---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Azure-opslag toe te voegen met behulp van Visual Studio verbonden Services
Met Visual Studio, kunt u een van de volgende naar Azure Storage met behulp van de **verbonden Services toevoegen** dialoogvenster:

- C#-cloudservice
- .NET-back-end voor mobiele service
- ASP.NET-website of service
- ASP.NET Core-service
- Azure webtaak-service 

De functionaliteit van de gekoppelde service worden alle benodigde verwijzingen en verbinding code toegevoegd aan uw project en de configuratiebestanden op de juiste wijze wordt gewijzigd. 

Na voltooiing wordt de **verbonden Services toevoegen** dialoogvenster automatisch wordt weergegeven met gedetailleerde informatie over de stappen die nodig zijn om te gaan werken met blob storage, wachtrijen, documentatie en tabellen.

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Verbinding maken met Azure Storage met het dialoogvenster Services verbonden
1. Open het project in Visual Studio

1. In **Solution Explorer**, met de rechtermuisknop op de **verbonden Services** knooppunt en klik in het contextmenu en selecteer **verbonden Service toevoegen**.
   
    ![Toevoegen van Azure service verbonden](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. In de **verbonden Services** pagina **Cloud-opslag met Azure Storage**.
   
    ![Azure-opslag toevoegen](./media/vs-azure-tools-connected-services-storage/add-azure-storage.png)

1. In de **Azure Storage** dialoogvenster, selecteer een bestaand opslagaccount en selecteert u **toevoegen**.
   
    Als u een opslagaccount maken wilt, gaat u naar de volgende stap. Ga anders verder met stap 6.
    
    ![Bestaande opslagaccount toevoegen aan project](./media/vs-azure-tools-connected-services-storage/select-azure-storage-account.png)

1. Een opslagaccount maken: 
   
   1. Selecteer **een nieuw Opslagaccount maken** aan de onderkant van het dialoogvenster.

   1. Vul de **Storage-Account maken** dialoogvenster en selecteer **maken**.
      
       ![Nieuwe Azure storage-account](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
   1. Wanneer de **Azure Storage** dialoogvenster wordt weergegeven, wordt het nieuwe opslagaccount wordt weergegeven in de lijst. Selecteer het nieuwe opslagaccount in de lijst en selecteer **toevoegen**.

1. De opslag gekoppelde service wordt weergegeven onder de **Serviceverwijzingen** knooppunt van uw project.
   
## <a name="how-your-project-is-modified"></a>Hoe uw project is gewijzigd
Wanneer u klaar bent met het dialoogvenster, wordt in Visual Studio verwijzingen worden toegevoegd en bepaalde configuratiebestanden wijzigt. De specifieke wijzigingen, is afhankelijk van het type: 

- ASP.NET-project - [wat is er gebeurd – ASP.NET-projecten](http://go.microsoft.com/fwlink/p/?LinkId=513126)
- ASP.NET Core project - [wat is er gebeurd – ASP.NET 5-projecten](http://go.microsoft.com/fwlink/p/?LinkId=513124) 
- Cloudserviceproject (webrollen en werkrollen) - [wat is er gebeurd – Cloud Service-projecten](http://go.microsoft.com/fwlink/p/?LinkId=516965)
- Webtaak project - [wat is er gebeurd - webtaak projecten](visual-studio/vs-storage-webjobs-what-happened.md)

## <a name="next-steps"></a>Volgende stappen
- [MSDN-Forum: Azure-opslag](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
- [Blog van Microsoft Azure Storage-Team](http://blogs.msdn.com/b/windowsazurestorage/)
- [Documentatie bij Azure Storage](https://docs.microsoft.com/azure/storage/)
