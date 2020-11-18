---
ms.openlocfilehash: 12a6c18b52e2bc9aba5ad69c8bb9ab8091c11a64
ms.sourcegitcommit: e0d9b18d2d4cbeb4a48890f3420a47e6a90abc53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "49347812"
---
<!-- markdownlint-disable MD002 MD041 -->

In diesem Abschnitt erstellen Sie ein bot-Framework-Projekt.

1. Öffnen Sie die Befehlszeilenschnittstelle (CLI) in einem Verzeichnis, in dem Sie das Projekt erstellen möchten. Führen Sie den folgenden Befehl aus, um ein neues Projekt mithilfe der **Microsoft. bot. Framework. CSharp. EchoBot** -Vorlage zu erstellen.

    ```dotnetcli
    dotnet new echobot -n GraphCalendarBot
    ```

    > [!NOTE]
    > Wenn Sie eine `No templates matched the input template name: echobot.` Fehlermeldung erhalten, installieren Sie die Vorlage mit dem folgenden Befehl, und führen Sie den vorherigen Befehl erneut aus.
    >
    > ```dotnetcli
    > dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
    > ```

1. Benennen Sie die standardmäßige **EchoBot** -Klasse in **CalendarBot** um. Öffnen Sie **./Bots/EchoBot.cs** , und ersetzen Sie alle Instanzen von `EchoBot` mit `CalendarBot` . Benennen Sie die Datei in **CalendarBot.cs** um.

1. Ersetzen Sie alle Instanzen von `EchoBot` mit `CalendarBot` in den restlichen **CS** -Dateien.

1. Ändern Sie in der CLI das aktuelle Verzeichnis in das **GraphCalendarBot** -Verzeichnis, und führen Sie den folgenden Befehl aus, um die Projekt Builds zu bestätigen.

    ```dotnetcli
    dotnet build
    ```

## <a name="add-nuget-packages"></a>Hinzufügen von NuGet-Paketen

Bevor Sie fortfahren, installieren Sie einige zusätzliche NuGet-Pakete, die Sie später verwenden werden.

- [AdaptiveCards](https://www.nuget.org/packages/AdaptiveCards/) , damit der bot Adaptive Karten in Antworten senden kann.
- [Microsoft. bot. Builder. Dialogs](https://www.nuget.org/packages/Microsoft.Bot.Builder.Dialogs/) , um dem bot Dialogfeld Unterstützung hinzuzufügen.
- [Microsoft. Recognizers. Text. DataTypes. Timex](https://www.nuget.org/packages/Microsoft.Recognizers.Text.DataTypes.TimexExpression/) Expression konvertiert die Timex-Ausdrücke, die von bot-Ansagen zurückgegeben werden, in **DateTime** -Objekte.
- [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) zum Aufrufen von Microsoft Graph

1. Führen Sie die folgenden Befehle in der CLI aus, um die Abhängigkeiten zu installieren.

    ```Shell
    dotnet add package AdaptiveCards --version 2.2.0
    dotnet add package Microsoft.Bot.Builder.Dialogs --version 4.10.3
    dotnet add package Microsoft.Bot.Builder.Integration.AspNet.Core --version 4.10.3
    dotnet add package Microsoft.Recognizers.Text.DataTypes.TimexExpression --version 1.4.1
    dotnet add package Microsoft.Graph --version 3.18.0
    ```

## <a name="test-the-bot"></a>Testen des bot

Testen Sie vor dem Hinzufügen von Code den bot, um sicherzustellen, dass er ordnungsgemäß funktioniert und dass der bot Framework-Emulator so konfiguriert ist, dass er getestet wird.

1. Starten Sie den bot, indem Sie den folgenden Befehl ausführen.

    ```dotnetcli
    dotnet run
    ```

    > [!TIP]
    > Sie können zwar einen beliebigen Text-Editor verwenden, um die Quelldateien im Projekt zu bearbeiten, es wird jedoch empfohlen, [Visual Studio Code](https://code.visualstudio.com/)zu verwenden. Visual Studio Code bietet Debugging-Unterstützung, IntelliSense und vieles mehr. Wenn Sie Visual Studio-Code verwenden, können Sie den bot mit **Run** dem Menü Start  ->  **Debuggen** ausführen starten.

1. Bestätigen Sie, dass der bot gestartet wird, indem Sie Ihren Browser öffnen und zu wechseln `http://localhost:3978` . Sie sollten sehen, dass **Ihr bot ist Ready!** Nachricht.

1. Öffnen Sie den bot Framework-Emulator. Klicken Sie im Menü **Datei** auf **bot öffnen**.

1. Geben Sie `http://localhost:3978/api/messages` in die **bot-URL** ein, und wählen Sie dann **Connect** aus.

1. Der bot antwortet mit `Hello and welcome!` im Chatfenster. Senden Sie eine Nachricht an den bot, und bestätigen Sie, dass Echo zurückgegeben wird.

    ![Ein Screenshot des bot Framework-Emulators, der mit dem bot verbunden ist](images/test-emulator.png)
