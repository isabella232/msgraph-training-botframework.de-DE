---
ms.openlocfilehash: c0fd9a9f5529f202c125d7f1e7c6b50fa9dc630d
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347819"
---
# <a name="graphcalendarbot"></a><span data-ttu-id="c807d-101">GraphCalendarBot</span><span class="sxs-lookup"><span data-stu-id="c807d-101">GraphCalendarBot</span></span>

<span data-ttu-id="c807d-102">Bot-Framework-V4-Echo-bot-Beispiel.</span><span class="sxs-lookup"><span data-stu-id="c807d-102">Bot Framework v4 echo bot sample.</span></span>

<span data-ttu-id="c807d-103">Dieser bot wurde mit dem [bot-Framework](https://dev.botframework.com)erstellt und zeigt, wie ein einfacher bot erstellt wird, der Eingaben vom Benutzer annimmt und zurück hallt.</span><span class="sxs-lookup"><span data-stu-id="c807d-103">This bot has been created using [Bot Framework](https://dev.botframework.com), it shows how to create a simple bot that accepts input from the user and echoes it back.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c807d-104">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="c807d-104">Prerequisites</span></span>

- <span data-ttu-id="c807d-105">[.Net Core SDK](https://dotnet.microsoft.com/download) Version 3,1</span><span class="sxs-lookup"><span data-stu-id="c807d-105">[.NET Core SDK](https://dotnet.microsoft.com/download) version 3.1</span></span>

  ```bash
  # determine dotnet version
  dotnet --version
  ```

## <a name="to-try-this-sample"></a><span data-ttu-id="c807d-106">So testen Sie dieses Beispiel</span><span class="sxs-lookup"><span data-stu-id="c807d-106">To try this sample</span></span>

- <span data-ttu-id="c807d-107">Navigieren Sie in einem Terminal zu `GraphCalendarBot`</span><span class="sxs-lookup"><span data-stu-id="c807d-107">In a terminal, navigate to `GraphCalendarBot`</span></span>

    ```bash
    # change into project folder
    cd GraphCalendarBot
    ```

- <span data-ttu-id="c807d-108">Führen Sie den bot über ein Terminal oder Visual Studio aus, und wählen Sie Option a oder B aus.</span><span class="sxs-lookup"><span data-stu-id="c807d-108">Run the bot from a terminal or from Visual Studio, choose option A or B.</span></span>

  <span data-ttu-id="c807d-109">A) von einem Terminal</span><span class="sxs-lookup"><span data-stu-id="c807d-109">A) From a terminal</span></span>

  ```bash
  # run the bot
  dotnet run
  ```

  <span data-ttu-id="c807d-110">B) oder von Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c807d-110">B) Or from Visual Studio</span></span>

  - <span data-ttu-id="c807d-111">Visual Studio starten</span><span class="sxs-lookup"><span data-stu-id="c807d-111">Launch Visual Studio</span></span>
  - <span data-ttu-id="c807d-112">Datei > Open-> Projekt/Lösung</span><span class="sxs-lookup"><span data-stu-id="c807d-112">File -> Open -> Project/Solution</span></span>
  - <span data-ttu-id="c807d-113">Navigieren Sie zu `GraphCalendarBot` Ordner</span><span class="sxs-lookup"><span data-stu-id="c807d-113">Navigate to `GraphCalendarBot` folder</span></span>
  - <span data-ttu-id="c807d-114">`GraphCalendarBot.csproj`Datei auswählen</span><span class="sxs-lookup"><span data-stu-id="c807d-114">Select `GraphCalendarBot.csproj` file</span></span>
  - <span data-ttu-id="c807d-115">Drücken Sie `F5` , um das Projekt auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c807d-115">Press `F5` to run the project</span></span>

## <a name="testing-the-bot-using-bot-framework-emulator"></a><span data-ttu-id="c807d-116">Testen des bot mithilfe des bot Framework-Emulators</span><span class="sxs-lookup"><span data-stu-id="c807d-116">Testing the bot using Bot Framework Emulator</span></span>

<span data-ttu-id="c807d-117">[Bot-Framework-Emulator](https://github.com/microsoft/botframework-emulator) ist eine Desktopanwendung, die es bot-Entwicklern ermöglicht, ihre Bots auf localhost zu testen und zu debuggen oder Remote über einen Tunnel auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c807d-117">[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) is a desktop application that allows bot developers to test and debug their bots on localhost or running remotely through a tunnel.</span></span>

- <span data-ttu-id="c807d-118">Installieren Sie die bot Framework-Emulatorversion 4.9.0 oder höher von [hier](https://github.com/Microsoft/BotFramework-Emulator/releases) aus.</span><span class="sxs-lookup"><span data-stu-id="c807d-118">Install the Bot Framework Emulator version 4.9.0 or greater from [here](https://github.com/Microsoft/BotFramework-Emulator/releases)</span></span>

### <a name="connect-to-the-bot-using-bot-framework-emulator"></a><span data-ttu-id="c807d-119">Herstellen einer Verbindung mit dem Bot mithilfe des bot Framework-Emulators</span><span class="sxs-lookup"><span data-stu-id="c807d-119">Connect to the bot using Bot Framework Emulator</span></span>

- <span data-ttu-id="c807d-120">Starten des bot Framework-Emulators</span><span class="sxs-lookup"><span data-stu-id="c807d-120">Launch Bot Framework Emulator</span></span>
- <span data-ttu-id="c807d-121">Datei > offener bot</span><span class="sxs-lookup"><span data-stu-id="c807d-121">File -> Open Bot</span></span>
- <span data-ttu-id="c807d-122">Geben Sie eine bot-URL für `http://localhost:3978/api/messages`</span><span class="sxs-lookup"><span data-stu-id="c807d-122">Enter a Bot URL of `http://localhost:3978/api/messages`</span></span>

## <a name="deploy-the-bot-to-azure"></a><span data-ttu-id="c807d-123">Bereitstellen des bot in Azure</span><span class="sxs-lookup"><span data-stu-id="c807d-123">Deploy the bot to Azure</span></span>

<span data-ttu-id="c807d-124">Weitere Informationen zum Bereitstellen eines bot für Azure finden Sie unter [deploy your bot to Azure](https://aka.ms/azuredeployment) für eine vollständige Liste der Bereitstellungsanweisungen.</span><span class="sxs-lookup"><span data-stu-id="c807d-124">To learn more about deploying a bot to Azure, see [Deploy your bot to Azure](https://aka.ms/azuredeployment) for a complete list of deployment instructions.</span></span>

## <a name="further-reading"></a><span data-ttu-id="c807d-125">Weiterführende Literatur</span><span class="sxs-lookup"><span data-stu-id="c807d-125">Further reading</span></span>

- [<span data-ttu-id="c807d-126">Bot-Framework-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="c807d-126">Bot Framework Documentation</span></span>](https://docs.botframework.com)
- [<span data-ttu-id="c807d-127">Bot-Grundlagen</span><span class="sxs-lookup"><span data-stu-id="c807d-127">Bot Basics</span></span>](https://docs.microsoft.com/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0)
- [<span data-ttu-id="c807d-128">Aktivitäts Verarbeitung</span><span class="sxs-lookup"><span data-stu-id="c807d-128">Activity processing</span></span>](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-activity-processing?view=azure-bot-service-4.0)
- [<span data-ttu-id="c807d-129">Einführung in den Azure bot-Dienst</span><span class="sxs-lookup"><span data-stu-id="c807d-129">Azure Bot Service Introduction</span></span>](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
- [<span data-ttu-id="c807d-130">Dokumentation zum Azure bot-Dienst</span><span class="sxs-lookup"><span data-stu-id="c807d-130">Azure Bot Service Documentation</span></span>](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)
- [<span data-ttu-id="c807d-131">.Net-Kern-CLI-Tools</span><span class="sxs-lookup"><span data-stu-id="c807d-131">.NET Core CLI tools</span></span>](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x)
- [<span data-ttu-id="c807d-132">Azure-CLI</span><span class="sxs-lookup"><span data-stu-id="c807d-132">Azure CLI</span></span>](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
- [<span data-ttu-id="c807d-133">Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="c807d-133">Azure Portal</span></span>](https://portal.azure.com)
- [<span data-ttu-id="c807d-134">Sprachverständnis mit Luis</span><span class="sxs-lookup"><span data-stu-id="c807d-134">Language Understanding using LUIS</span></span>](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/)
- [<span data-ttu-id="c807d-135">Kanäle und bot Connector-Dienst</span><span class="sxs-lookup"><span data-stu-id="c807d-135">Channels and Bot Connector Service</span></span>](https://docs.microsoft.com/en-us/azure/bot-service/bot-concepts?view=azure-bot-service-4.0)

<span data-ttu-id="c807d-136">Generiert mit `dotnet new echobot` v 4.10.2</span><span class="sxs-lookup"><span data-stu-id="c807d-136">Generated using `dotnet new echobot` v4.10.2</span></span>
