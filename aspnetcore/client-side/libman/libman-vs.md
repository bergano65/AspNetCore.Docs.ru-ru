---
title: Использование LibMan с ASP.NET Core в Visual Studio
author: scottaddie
description: Сведения об использовании LibMan в проекте ASP.NET Core с Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646798"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Использование LibMan с ASP.NET Core в Visual Studio

Автор: [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)

В Visual Studio имеется встроенная поддержка [LibMan](xref:client-side/libman/index) в проектах ASP.NET Core, которая включает в себя:

* поддержку настройки и выполнения операций восстановления LibMan при сборке;
* пункты меню для запуска операций восстановления и очистки LibMan;
* диалоговое окно для поиска библиотек и добавления файлов в проект;
* поддержку редактирования *libman.json* &mdash; файла манифеста LibMan.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) ([описание загрузки](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и веб-разработка**

## <a name="add-library-files"></a>Добавление файлов библиотеки

Файлы библиотек можно добавлять в проект ASP.NET Core двумя разными способами:

1. [Используйте диалоговое окно "Добавьте клиентские библиотеки".](#use-the-add-client-side-library-dialog)
1. [Настройте записи в файле манифеста LibMan вручную.](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Использование диалогового окна "Добавьте клиентские библиотеки"

Чтобы установить клиентскую библиотеку, выполните указанные ниже действия.

* В **обозревателе решений** щелкните правой кнопкой мыши папку проекта, в которую нужно добавить файлы. Выберите пункты **Добавить** > **Клиентская библиотека**. Появится диалоговое окно **Добавьте клиентские библиотеки**.

  ![Диалоговое окно "Добавьте клиентские библиотеки"](_static/add-library-dialog.png)

* В раскрывающемся списке **Поставщик** выберите поставщика библиотеки. Поставщик по умолчанию — CDNJS.
* В текстовом поле **Библиотека** введите имя библиотеки, которую нужно получить. IntelliSense предлагает список библиотек, имена которых начинаются с введенного текста.
* Выберите библиотеку в списке IntelliSense. Обратите внимание, что после имени библиотеки стоит символ `@` и номер последней стабильной версии, известной выбранному поставщику.
* Решите, какие файлы следует включить.
  * Установите переключатель в положение **Включить все файлы библиотеки**, чтобы включить все файлы библиотеки.
  * Установите переключатель в положение **Выбрать определенные файлы**, чтобы включить часть файлов библиотеки. После этого станет доступно дерево выбора файлов. Установите флажки слева от имен файлов, которые нужно скачать.
* В текстовом поле **Целевое расположение** укажите папку проекта, в которой необходимо сохранить файлы. Каждую библиотеку рекомендуется хранить в отдельной папке.

  Предлагаемое **целевое расположение** зависит от того, откуда было запущено диалоговое окно.

  * При запуске из корневого каталога проекта:
    * используется папка *wwwroot/lib*, если *wwwroot* существует;
    * используется папка *lib*, если *wwwroot* не существует.
  * При запуске из папки проекта используется соответствующее имя папки.

  После папки указывается имя библиотеки. В приведенной ниже таблице показаны папки, предлагаемые при установке jQuery в проекте Razor Pages.
  
  |Место запуска                           |Предлагаемая папка      |
  |------------------------------------------|----------------------|
  |корневой каталог проекта (если *wwwroot* существует)        |*wwwroot/lib/jquery/* |
  |корневой каталог проекта (если *wwwroot* не существует) |*lib/jquery/*         |
  |Папка *Pages* проекта                 |*Pages/jquery/*       |

* Нажмите кнопку **Установить**, чтобы скачать файлы в соответствии с конфигурацией в файле *libman.json*.
* Ознакомьтесь со сведениями об установке в канале **Диспетчер библиотек** окна **Выходные данные**. Пример:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>Настройка записей в файле манифеста LibMan вручную

Все операции LibMan в Visual Studio основаны на содержимом манифеста LibMan в корневом каталоге проекта (*libman.json*). Вы можете вручную изменить файл *libman.json*, чтобы настроить файлы библиотек для проекта. Visual Studio восстанавливает все файлы библиотек после сохранения файла *libman.json*.

Открыть файл *libman.json* для изменения можно указанными ниже способами.

* Дважды щелкните файл *libman.json* в **обозревателе решений**.
* В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Управление клиентскими библиотеками**. **&#8224;**
* В меню **Проект** в Visual Studio выберите пункт **Управление клиентскими библиотеками**. **&#8224;**

**&#8224;** Если файла *libman.json* еще нет в корневом каталоге проекта, он будет создан с содержимым по умолчанию.

В Visual Studio предлагаются широкие возможности редактирования JSON, такие как раскраска, форматирование, IntelliSense и проверка схемы. Схему JSON манифеста LibMan можно найти на странице [https://json.schemastore.org/libman](https://json.schemastore.org/libman).

При использовании приведенного ниже файла манифеста LibMan извлекает файлы в соответствии с конфигурацией, определенной в свойстве `libraries`. Далее приводится описание объектных литералов, определенных в `libraries`.

* От поставщика CDNJS получается подмножество файлов [jQuery](https://jquery.com/) версии 3.3.1. Это подмножество определено в свойстве `files` &mdash; *jquery.min.js*, *jquery.js* и *jquery.min.map*. Файлы помещаются в папку *wwwroot/lib/jquery* проекта.
* Все файлы [Bootstrap](https://getbootstrap.com/) версии 4.1.3 получаются и помещаются в папку *wwwroot/lib/bootstrap*. Свойство `provider` объектного литерала переопределяет значение свойства `defaultProvider`. LibMan получает файлы Bootstrap от поставщика unpkg.
* Подмножество файлов [Lodash](https://lodash.com/) утверждается руководством организации. Файлы *lodash.js* и *lodash.min.js* получаются из папки *C:\\temp\\lodash\\* локальной файловой системы. Они копируются в папку *wwwroot/lib/lodash* проекта.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan поддерживает только одну версию каждой библиотеки от каждого поставщика. Файл *libman.json* не пройдет проверку схемы, если он содержит две библиотеки с одним и тем же именем для данного поставщика.

## <a name="restore-library-files"></a>Восстановление файлов библиотек

Для восстановления файлов библиотек из Visual Studio в корневом каталоге проекта должен быть правильный файл *libman.json*. Восстановленные файлы помещаются в проект в расположение, указанное для каждой библиотеки.

Файлы библиотек можно восстановить в проекте ASP.NET Core двумя способами:

1. [Восстановление файлов во время сборки](#restore-files-during-build)
1. [Восстановление файлов вручную](#restore-files-manually)

### <a name="restore-files-during-build"></a>Восстановление файлов во время сборки

LibMan может восстанавливать определенные файлы библиотек в процессе сборки. По умолчанию *восстановление при сборке* отключено.

Чтобы включить и протестировать восстановление при сборке, выполните указанные ниже действия.

* В **обозревателе решений** щелкните файл *libman.json* правой кнопкой мыши и выберите в контекстном меню пункт **Включить восстановление клиентских библиотек при сборке**.
* При появлении запроса на установку пакета NuGet нажмите кнопку **Да**. Пакет NuGet [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) добавится в проект:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Выполните сборку проекта, чтобы убедиться в том, что диспетчер LibMan восстановил файлы. Пакет `Microsoft.Web.LibraryManager.Build` внедряет целевой объект MSBuild, который запускает LibMan во время операции сборки проекта.
* В канале **Сборка** окна **Выходные данные** просмотрите журнал действий LibMan:

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

После включения восстановления при сборке в контекстном меню файла *libman.json* появляется пункт **Отключить восстановление клиентских библиотек при сборке**. При его выборе ссылка на пакет `Microsoft.Web.LibraryManager.Build` удаляется из файла проекта. В результате клиентские библиотеки больше не будут восстанавливаться при каждой сборке.

Независимо от того, включено ли восстановление при сборке, вы можете в любой момент выполнить восстановление вручную из контекстного меню файла *libman.json*. Дополнительные сведения см. в разделе [Восстановление файлов вручную](#restore-files-manually).

### <a name="restore-files-manually"></a>Восстановление файлов вручную

Чтобы вручную восстановить файлы библиотек, выполните указанные ниже действия.

* Для всех проектов в решении:
  * В **обозревателе решений** щелкните правой кнопкой мыши имя решения.
  * Выберите пункт **Восстановить клиентские библиотеки**.
* Для определенного проекта:
  * В **обозревателе решений** щелкните правой кнопкой мыши файл *libman.json*.
  * Выберите пункт **Восстановить клиентские библиотеки**.

Во время операции восстановления происходит следующее:

* В строке состояния Visual Studio будет анимироваться значок Центра состояния задач (TSC) с сообщением *Операция восстановления начата*. Если щелкнуть значок, откроется подсказка со списком известных фоновых задач.
* В строку состояния и канал **Диспетчер библиотек** окна **Выходные данные** будут передаваться сообщения. Пример:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>Удаление файлов библиотек

Чтобы произвести *операцию очистки*, которая удаляет файлы библиотек, восстановленные ранее в Visual Studio, выполните указанные ниже действия.

* В **обозревателе решений** щелкните правой кнопкой мыши файл *libman.json*.
* Выберите пункт **Очистить клиентские библиотеки**.

Во избежание непреднамеренного удаления файлов, не относящихся к библиотекам, операция очистки не удаляет каталоги полностью. Она удаляет только те файлы, которые были включены в предыдущую операцию восстановления.

Во время операции очистки происходит следующее:

* В строке состояния Visual Studio будет анимироваться значок Центра состояния задач с сообщением *Начата операция с клиентскими библиотеками*. Если щелкнуть значок, откроется подсказка со списком известных фоновых задач.
* В строку состояния и канал **Диспетчер библиотек** окна **Выходные данные** передаются сообщения. Пример:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

Операция очистки удаляет файлы только из проекта. При этом файлы библиотек остаются в кэше для более быстрого извлечения при последующих операциях восстановления. Для управления файлами библиотек, хранящимися в кэше локального компьютера, используйте [интерфейс командной строки LibMan](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Удаление файлов библиотек

Чтобы удалить файлы библиотек, выполните указанные ниже действия.

* Откройте файл *libman.json*.
* Поместите курсор внутри соответствующего объектного литерала `libraries`.
* Щелкните значок лампочки, появившийся в левом поле, а затем выберите пункт **Удалить \<имя_библиотеки>@\<версия_библиотеки>** .

  ![Команда удаления библиотеки в контекстном меню](_static/uninstall-menu-option.png)

Кроме того, вы можете вручную изменить и сохранить манифест LibMan (*libman.json*). [Операция восстановления](#restore-library-files) выполняется при сохранении файла. Файлы библиотеки, которые больше не определены в файле *libman.json*, удаляются из проекта.

## <a name="update-library-version"></a>Обновление версии библиотеки

Чтобы проверить наличие новой версии библиотеки, выполните указанные ниже действия.

* Откройте файл *libman.json*.
* Поместите курсор внутри соответствующего объектного литерала `libraries`.
* Щелкните значок лампочки, появившийся в левом поле. Наведите указатель на пункт **Проверить обновления**.

LibMan проверит наличие версии библиотеки, более поздней, чем установленная. Возможен один из следующих результатов:

* Если установлена последняя версия, появится сообщение **Обновления не найдены**.
* Будет показана последняя стабильная версия, если она еще не установлена.

  ![Пункт "Проверить обновления" в контекстном меню](_static/update-menu-option.png)

* Если имеется предварительный выпуск, более поздний, чем установленная версия, он будет показан.

Чтобы перейти на более старую версию библиотеки, вручную измените файл *libman.json*. При сохранении этого файла [операция восстановления](#restore-library-files) LibMan выполняет следующие действия:

* удаляет лишние файлы из предыдущей версии;
* добавляет новые и обновленные файлы из новой версии.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:client-side/libman/libman-cli>
* [Репозиторий LibMan на GitHub](https://github.com/aspnet/LibraryManager)
