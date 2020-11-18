---
ms.openlocfilehash: 0022448db76695894bfefca2543ad39c4fd07dcc
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347816"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="31b90-101">In diesem Lernprogramm erfahren Sie, wie Sie einen bot-Framework-bot erstellen, der die Microsoft Graph-API zum Abrufen von Kalenderinformationen für einen Benutzer verwendet.</span><span class="sxs-lookup"><span data-stu-id="31b90-101">This tutorial teaches you how to build a Bot Framework bot that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="31b90-102">Wenn Sie das fertige Lernprogramm einfach herunterladen möchten, können Sie das GitHub- [Repository](https://github.com/microsoftgraph/msgraph-training-botframework)herunterladen oder Klonen.</span><span class="sxs-lookup"><span data-stu-id="31b90-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-botframework).</span></span> <span data-ttu-id="31b90-103">Anweisungen zum Konfigurieren der APP mit einer APP-ID und einem geheimen Schlüssel finden Sie in der Readme-Datei im **Demo** -Ordner.</span><span class="sxs-lookup"><span data-stu-id="31b90-103">See the README file in the **demo** folder for instructions on configuring the app with an app ID and secret.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="31b90-104">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="31b90-104">Prerequisites</span></span>

<span data-ttu-id="31b90-105">Bevor Sie mit diesem Lernprogramm beginnen, sollten Sie Folgendes auf Ihrem Entwicklungscomputer installiert haben.</span><span class="sxs-lookup"><span data-stu-id="31b90-105">Before you start this tutorial, you should have the following installed on your development machine.</span></span>

- [<span data-ttu-id="31b90-106">.Net Core SDK</span><span class="sxs-lookup"><span data-stu-id="31b90-106">.NET Core SDK</span></span>](https://dotnet.microsoft.com/download)
- [<span data-ttu-id="31b90-107">Bot-Framework-Emulator</span><span class="sxs-lookup"><span data-stu-id="31b90-107">Bot Framework Emulator</span></span>](https://github.com/microsoft/BotFramework-Emulator/blob/master/README.md)

<span data-ttu-id="31b90-108">Sie sollten auch über ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Arbeits-oder Schulkonto verfügen.</span><span class="sxs-lookup"><span data-stu-id="31b90-108">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="31b90-109">Wenn Sie kein Microsoft-Konto haben, gibt es mehrere Optionen, um ein kostenloses Konto zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="31b90-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="31b90-110">Sie können [sich für ein neues persönliches Microsoft-Konto registrieren](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="31b90-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="31b90-111">Sie können sich [für das Office 365 Entwicklerprogramm registrieren](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="31b90-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>
- <span data-ttu-id="31b90-112">Ein Azure-Abonnement.</span><span class="sxs-lookup"><span data-stu-id="31b90-112">An Azure subscription.</span></span> <span data-ttu-id="31b90-113">Wenn Sie noch keinen haben, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , bevor Sie beginnen.</span><span class="sxs-lookup"><span data-stu-id="31b90-113">If you do not have one, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>

> [!NOTE]
> <span data-ttu-id="31b90-114">Dieses Lernprogramm wurde mit .net Core SDK Version 3.1.401 geschrieben.</span><span class="sxs-lookup"><span data-stu-id="31b90-114">This tutorial was written with .NET Core SDK version 3.1.401.</span></span> <span data-ttu-id="31b90-115">Die Schritte in diesem Leitfaden funktionieren möglicherweise mit anderen Versionen, jedoch nicht getestet.</span><span class="sxs-lookup"><span data-stu-id="31b90-115">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="31b90-116">Feedback</span><span class="sxs-lookup"><span data-stu-id="31b90-116">Feedback</span></span>

<span data-ttu-id="31b90-117">Geben Sie Feedback zu diesem Lernprogramm im [GitHub-Repository](https://github.com/microsoftgraph/msgraph-training-botframework)an.</span><span class="sxs-lookup"><span data-stu-id="31b90-117">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-botframework).</span></span>
