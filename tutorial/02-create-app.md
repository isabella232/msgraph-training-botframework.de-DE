---
ms.openlocfilehash: 12a6c18b52e2bc9aba5ad69c8bb9ab8091c11a64
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347812"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8a742-101">In diesem Abschnitt erstellen Sie ein bot-Framework-Projekt.</span><span class="sxs-lookup"><span data-stu-id="8a742-101">In this section you'll create a Bot Framework project.</span></span>

1. <span data-ttu-id="8a742-102">Öffnen Sie die Befehlszeilenschnittstelle (CLI) in einem Verzeichnis, in dem Sie das Projekt erstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="8a742-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="8a742-103">Führen Sie den folgenden Befehl aus, um ein neues Projekt mithilfe der **Microsoft. bot. Framework. CSharp. EchoBot** -Vorlage zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8a742-103">Run the following command to create new project using the **Microsoft.Bot.Framework.CSharp.EchoBot** template.</span></span>

    ```dotnetcli
    dotnet new echobot -n GraphCalendarBot
    ```

    > [!NOTE]
    > <span data-ttu-id="8a742-104">Wenn Sie eine `No templates matched the input template name: echobot.` Fehlermeldung erhalten, installieren Sie die Vorlage mit dem folgenden Befehl, und führen Sie den vorherigen Befehl erneut aus.</span><span class="sxs-lookup"><span data-stu-id="8a742-104">If you receive an `No templates matched the input template name: echobot.` error, install the template with the following command and re-run the previous command.</span></span>
    >
    > ```dotnetcli
    > dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
    > ```

1. <span data-ttu-id="8a742-105">Benennen Sie die standardmäßige **EchoBot** -Klasse in **CalendarBot** um.</span><span class="sxs-lookup"><span data-stu-id="8a742-105">Rename the default **EchoBot** class to **CalendarBot**.</span></span> <span data-ttu-id="8a742-106">Öffnen Sie **./Bots/EchoBot.cs** , und ersetzen Sie alle Instanzen von `EchoBot` mit `CalendarBot` .</span><span class="sxs-lookup"><span data-stu-id="8a742-106">Open **./Bots/EchoBot.cs** and replace all instances of `EchoBot` with `CalendarBot`.</span></span> <span data-ttu-id="8a742-107">Benennen Sie die Datei in **CalendarBot.cs** um.</span><span class="sxs-lookup"><span data-stu-id="8a742-107">Rename the file to **CalendarBot.cs**.</span></span>

1. <span data-ttu-id="8a742-108">Ersetzen Sie alle Instanzen von `EchoBot` mit `CalendarBot` in den restlichen **CS** -Dateien.</span><span class="sxs-lookup"><span data-stu-id="8a742-108">Replace all instances of `EchoBot` with `CalendarBot` in the remaining **.cs** files.</span></span>

1. <span data-ttu-id="8a742-109">Ändern Sie in der CLI das aktuelle Verzeichnis in das **GraphCalendarBot** -Verzeichnis, und führen Sie den folgenden Befehl aus, um die Projekt Builds zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="8a742-109">In your CLI, change the current directory to the **GraphCalendarBot** directory and run the following command to confirm the project builds.</span></span>

    ```dotnetcli
    dotnet build
    ```

## <a name="add-nuget-packages"></a><span data-ttu-id="8a742-110">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="8a742-110">Add NuGet packages</span></span>

<span data-ttu-id="8a742-111">Bevor Sie fortfahren, installieren Sie einige zusätzliche NuGet-Pakete, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="8a742-111">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="8a742-112">[AdaptiveCards](https://www.nuget.org/packages/AdaptiveCards/) , damit der bot Adaptive Karten in Antworten senden kann.</span><span class="sxs-lookup"><span data-stu-id="8a742-112">[AdaptiveCards](https://www.nuget.org/packages/AdaptiveCards/) to allow the bot to send Adaptive Cards in responses.</span></span>
- <span data-ttu-id="8a742-113">[Microsoft. bot. Builder. Dialogs](https://www.nuget.org/packages/Microsoft.Bot.Builder.Dialogs/) , um dem bot Dialogfeld Unterstützung hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="8a742-113">[Microsoft.Bot.Builder.Dialogs](https://www.nuget.org/packages/Microsoft.Bot.Builder.Dialogs/) to add dialog support to the bot.</span></span>
- <span data-ttu-id="8a742-114">[Microsoft. Recognizers. Text. DataTypes. Timex](https://www.nuget.org/packages/Microsoft.Recognizers.Text.DataTypes.TimexExpression/) Expression konvertiert die Timex-Ausdrücke, die von bot-Ansagen zurückgegeben werden, in **DateTime** -Objekte.</span><span class="sxs-lookup"><span data-stu-id="8a742-114">[Microsoft.Recognizers.Text.DataTypes.TimexExpression](https://www.nuget.org/packages/Microsoft.Recognizers.Text.DataTypes.TimexExpression/) to convert the TIMEX expressions returned from bot prompts into **DateTime** objects.</span></span>
- <span data-ttu-id="8a742-115">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) zum Aufrufen von Microsoft Graph</span><span class="sxs-lookup"><span data-stu-id="8a742-115">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to Microsoft Graph.</span></span>

1. <span data-ttu-id="8a742-116">Führen Sie die folgenden Befehle in der CLI aus, um die Abhängigkeiten zu installieren.</span><span class="sxs-lookup"><span data-stu-id="8a742-116">Run the following commands in your CLI to install the dependencies.</span></span>

    ```Shell
    dotnet add package AdaptiveCards --version 2.2.0
    dotnet add package Microsoft.Bot.Builder.Dialogs --version 4.10.3
    dotnet add package Microsoft.Bot.Builder.Integration.AspNet.Core --version 4.10.3
    dotnet add package Microsoft.Recognizers.Text.DataTypes.TimexExpression --version 1.4.1
    dotnet add package Microsoft.Graph --version 3.18.0
    ```

## <a name="test-the-bot"></a><span data-ttu-id="8a742-117">Testen des bot</span><span class="sxs-lookup"><span data-stu-id="8a742-117">Test the bot</span></span>

<span data-ttu-id="8a742-118">Testen Sie vor dem Hinzufügen von Code den bot, um sicherzustellen, dass er ordnungsgemäß funktioniert und dass der bot Framework-Emulator so konfiguriert ist, dass er getestet wird.</span><span class="sxs-lookup"><span data-stu-id="8a742-118">Before adding any code, test the bot to make sure that it works correctly, and that the Bot Framework Emulator is configured to test it.</span></span>

1. <span data-ttu-id="8a742-119">Starten Sie den bot, indem Sie den folgenden Befehl ausführen.</span><span class="sxs-lookup"><span data-stu-id="8a742-119">Start the bot by running the following command.</span></span>

    ```dotnetcli
    dotnet run
    ```

    > [!TIP]
    > <span data-ttu-id="8a742-120">Sie können zwar einen beliebigen Text-Editor verwenden, um die Quelldateien im Projekt zu bearbeiten, es wird jedoch empfohlen, [Visual Studio Code](https://code.visualstudio.com/)zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a742-120">While you can use any text editor to edit the source files in the project, we recommend using [Visual Studio Code](https://code.visualstudio.com/).</span></span> <span data-ttu-id="8a742-121">Visual Studio Code bietet Debugging-Unterstützung, IntelliSense und vieles mehr.</span><span class="sxs-lookup"><span data-stu-id="8a742-121">Visual Studio Code offers debugging support, Intellisense, and more.</span></span> <span data-ttu-id="8a742-122">Wenn Sie Visual Studio-Code verwenden, können Sie den bot mit **Run** dem Menü Start  ->  **Debuggen** ausführen starten.</span><span class="sxs-lookup"><span data-stu-id="8a742-122">If using Visual Studio Code, you can start the bot using the **Run** -> **Start Debugging** menu.</span></span>

1. <span data-ttu-id="8a742-123">Bestätigen Sie, dass der bot gestartet wird, indem Sie Ihren Browser öffnen und zu wechseln `http://localhost:3978` .</span><span class="sxs-lookup"><span data-stu-id="8a742-123">Confirm the bot is running by opening your browser and going to `http://localhost:3978`.</span></span> <span data-ttu-id="8a742-124">Sie sollten sehen, dass **Ihr bot ist Ready!**</span><span class="sxs-lookup"><span data-stu-id="8a742-124">You should see a **Your bot is ready!**</span></span> <span data-ttu-id="8a742-125">Nachricht.</span><span class="sxs-lookup"><span data-stu-id="8a742-125">message.</span></span>

1. <span data-ttu-id="8a742-126">Öffnen Sie den bot Framework-Emulator.</span><span class="sxs-lookup"><span data-stu-id="8a742-126">Open the Bot Framework Emulator.</span></span> <span data-ttu-id="8a742-127">Klicken Sie im Menü **Datei** auf **bot öffnen**.</span><span class="sxs-lookup"><span data-stu-id="8a742-127">Choose the **File** menu, then **Open Bot**.</span></span>

1. <span data-ttu-id="8a742-128">Geben Sie `http://localhost:3978/api/messages` in die **bot-URL** ein, und wählen Sie dann **Connect** aus.</span><span class="sxs-lookup"><span data-stu-id="8a742-128">Enter `http://localhost:3978/api/messages` in the **Bot URL**, then select **Connect**.</span></span>

1. <span data-ttu-id="8a742-129">Der bot antwortet mit `Hello and welcome!` im Chatfenster.</span><span class="sxs-lookup"><span data-stu-id="8a742-129">The bot responds with `Hello and welcome!` in the chat window.</span></span> <span data-ttu-id="8a742-130">Senden Sie eine Nachricht an den bot, und bestätigen Sie, dass Echo zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="8a742-130">Send a message to the bot and confirm it echoes it back.</span></span>

    ![Ein Screenshot des bot Framework-Emulators, der mit dem bot verbunden ist](images/test-emulator.png)
