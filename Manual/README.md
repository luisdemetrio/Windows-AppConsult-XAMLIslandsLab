﻿# Bring Fluent Design and modern user interaction to your WPF application

## Introduction
Windows 10 enables you to create modern applications thanks to the Universal Windows Platform (UWP). To benefit from the UWP Platform and its ecosystem, classic desktop applications have to be migrated. The migration paths are not trivial and may require a tremendous work; most of the times, it imposes a full rewriting.

Now, with XAML Islands, we can use UWP controls in non-UWP desktop applications so that we can enhance the look, feel, and functionality of your our LOB desktop applications with the latest Windows 10 UI features that are only available via UWP controls. This means that you can use UWP features such as Windows Ink and controls that support the Fluent Design System in your existing WPF, Windows Forms, and C++ Win32 applications.

With this lab, we will experiment XAML Islands and modernize an existing WPF application.

### Estimated time
90 minutes

### Objectives
- Learn how to modernize the user experience and the features of a desktop WPF application
- Learn how to leverage the Universal Windows Platforms without having to rewrite the app from scratch
- Use a bluit-in XAML Islands control in an existing WPF application
- Be able to 'integrate' any custom UWP XAML component in the WPF application
- Go further and perform bindings between the UWP XAML and the WPF application
- Understand how XAML Islands can help to start a progressive modernization journey to the Universal Windows Platform

### Prerequisites

- Experience in developing Windows Desktop applications with WPF
- Basic knowledge of C# and XAML
- Basic knowledge of UWP 

### Overview of the lab
We're going to start from an existing LOB application and we're going to enhance it by supporting modern features with the help of XAML Islands. We'll learn how to integrate Fluent controls from the Universal Windows Platform in the existing codebase.
The lab consists of five exercises:
1. In the first one you're going to start modernizing the application by adding a UWP control which enables to digitally sign a document.
2. In the second exercise you will continue the modernization journey, by adding a touch-enabled map control to display a location.
3. In the third exercise you're going to learn how you can add any native UWP control and interact with it.
4. In the fourth exercise you will make easier to interact with a UWP control, by creating a control wrapper.
5. In the last exercise you're going to migrate the WPF application to .NET Core 3.0, which will open up new and important scenarios in the future.

### Computers in this lab
This lab uses a single Virtual Machine to provide you with the development environment.

The virtual machine is based on Windows 10 October Update (1809) and it includes:
- Visual Studio 2019 Preview 1
- Windows 10 SDK version 10.0.17763.0 or later

We're going to use Visual Studio 2019 Preview since it provides preliminary support to .NET Core 3.0, which is one of the technologies we're going to use in one of the exercises. However, despite being a preview, it provides a good level of stability and reliability and, as such, we're going to leverage it for all the exercises in the lab.
As a backup, just in case you face any issue, the machine comes also with Visual Studio 2017 Community preinstalled.

If you already have these tools on your computer, feel free to directly use it for the lab instead of the virtual machine. Be aware that the following Visual Studio workloads have to be installed: 
- .NET desktop development
- Universal Windows Platform development

### Scenario
Contoso Expenses is an internal application used by managers of Contoso Corporation to keep track of the expenses submitted by their reports. Modernizing this application is necessary in order to enhance employee efficiency when creating expenses reports. Many of the requested features could be easily implemented with the Universal Windows Platform. However, the application is complex and it's the outcome of many years of development by different teams. As such, rewriting it from scratch with a new technology isn't an option on the table. The team is looking for the best approach to add these features but, at the same time, reusing the existing codebase.

### The project
Contoso Expenses is a desktop application, built with WPF and the .NET Framework. Being an application built for demo purposes, it contains some simplifications compared to a real WPF project, like:

- It doesn't use any development pattern, like MVVM, but the standard code-behind approach. 
- It uses a local database solution called [**LiteDb**](http://www.litedb.org/), which is an embedded NoSQL solution. In a real world scenario, such an application would connect to a centralized database, either on-premise or in the cloud, like SQL Server, MySQL, Cosmos DB, etc.

The goal of this project, in fact, is to help you focusing on understanding and implementing XAML Island inside an existing WPF application. It isn't made to teach you the best practices for WPF development.

### Key concepts that will be used during the lab

**Please note**. The following information are provided in case you're planning to follow this lab on your own or from home. If you are following this lab as part of a live training class, feel free to skip it and jump directly to the beginning of the first exercise. These concepts, in fact, should have already be explained by the trainers of the lab before starting the practical exercises.

#### Universal Windows Platform
Starting from Windows 8, Microsoft has introduced a new kind of applications: Windows Store apps, based on a new framework called Windows Runtime. Unlike the .NET Framework, the Windows Runtime is a native layer of APIs which are exposed directly by the operating system to applications which want to consume them. With the goal to make the platform viable for every developer and to not force them to learn C++, the Windows Runtime has introduced language projections, which are layers added on top of the runtime to allow developers to interact with it using well-known and familiar languages. Thanks to projections, developers can build applications on top of the Windows Runtime leveraging the same C# and XAML knowledge they have acquired in building apps with the .NET Framework. The Windows Runtime libraries (called Windows Runtime Components) are described using special metadata files, which make it possible for developers to access the APIs using the specific syntax of the language they’re using. This way, projections can also respect the language conventions and types, like uppercase if you use C# or camel case if you use JavaScript. Additionally, Windows Runtime components can be used across multiple languages: for example, a Windows Runtime component written in C++ can be used by an application developed in C# and XAML.
With the release of Windows 10, Microsoft has introduced the Universal Windows Platform, which can be considered the successor of the Windows Runtime since it’s built on top of the same technology. The most important feature of the Universal Windows Platform is that it offers a common set of APIs across every platform: no matter if the app is running on a desktop, on a Xbox One or on a HoloLens, you’re able to use the same APIs to reach the same goals. This is a major step forward compared to the Windows Runtime, which didn’t provide this kind of cross-device support. You were able to share code and UI between a PC project and a mobile project, but, in the end, developers needed anyway to create, maintain and deploy two different solutions.

The Universal Windows Platform has been built with security and privacy in mind. As such, Universal Windows Platform applications run inside a sandbox; they don’t have access to the registry; they can freely read and write data only in a specific local folder; etc. Any operation which is potentially dangerous requires the declaration of a capability and the consent of the user: some examples are accessing to the files in the Pictures library; using the microphone or the webcam; retrieving the location of the user; etc. Everything is controlled by a manifest file, which is an XML file that describes the identity of the application: its unique identifier, its capabilities, its visual aspect, its integration with the Windows 10 ecosystem, etc.

Last but not the least, all the investments of the Windows team for developers are focused on the Universal Windows Platform. All the latest features added in Windows 10, like Timeline, Project Rome, Windows Hello, etc. are exposed by the Universal Windows Platform, so that developers can integrate them in their applications.

#### Desktop Bridge
With the introduction of Windows Store apps first and Universal Windows Platform apps later, Microsoft has also introduced a new packaging modell called MSIX (formerly known as AppX), which is very different from the existing deployment models (like MSI). It's completely controlled by the operating system; it can be used to deploy applications not only using traditional approaches, like the web, SSCM, Intune, but it opens up now opportunities like the Microsoft Store / Store for Business / Store for Education.

With the release of Windows 10 Anniversary Update, this new format has been expanded to support not only modern applications, but also the existing ones built with traditional Win32 technologies, like WPF, Windows Forms, Java, Electron, etc. **Desktop Bridge** is the name of the technology that has enabled this feature, allowing developers to release their Win32 applications also on the Microsoft Store. Another key feature of Desktop Bridge is that it enables Win32 applications to have an identity, which opens up the opportunity to consume APIs from the Universal Windows Platform. 

When a Win32 application runs packaged with the Desktop Bridge, it's executed inside a lightweight container which helps to improve the security and the reliability of the application. The container enables three features:

- A **Virtual File System**. Each package can contain a folder, called **VFS**, which maps all the main system folders, like *C:\Windows*, *C:\Program Files*, etc. When the application looks for a file in one of these folders, Windows will look first inside the Virtual File System and, only if it can't find it, will redirect the call to the real file system. Thanks to this feature you can create self-deployable packages, which don't need the user to manually install 3rd party dependencies like frameworks, libraries, etc. Additionally, you can solve the problem known as **DLL hell**, which can happen when you have on a system multiple applications which depend by different versions of the same system framework or library. Since you can bundle the most appropriate version of the framework in each package, it won't interfere with any other application or framework already installed on the machine.
- A **virtualized registry**. All the writing operation to the HKEY_CURRENT_USER hive of the registry are stored in a binary file which is unique for each application. This way the application doesn't have the opportunity to interfere with the real registry. And when the user uninstalls the application, the binary file is simply deleted, making sure that no orphan registry keys are left in the system.
- A **virtualized file system**. As a best practice in Windows development, all the data generated by an application which is tightly coupled to it (a database, log files, temporary files, etc.) should be saved in the AppData folder, which lives under the user's space. In a packaged application, all the reading and writing operations against the AppData folder are automatically redirected to the local folder, which is unique for an application. This approach helps to keep the system efficient and reliable since, when the user uninstalls the application, the local folder is simply deleted, making sure there are no orphan files left in the system.

Desktop Bridge plays an important role with XAML Island because, by combining these two technologies, you'll be able to leverage the same time features and user controls of the Universal Windows Platform without rewriting it from scratch with another technology. 

#### XAML Islands architecture
The Windows 10 October 2018 Update with the SDK 17763, enables the scenario of XAML Islands for Desktop applications. That means that Windows 10 now supports hosting UWP controls inside the context of a Win32 Process. The 'magic' is powered by two new system APIs called <a href="https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager" target="_blank">WindowsXamlManager</a> and <a href="https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource" target="_blank">DesktopWindowXamlSource</a>.

- The **WindowsXamlManager** handles the UWP XAML Framework. As such, the only exposed method is called **InitializeForCurrentThread()**, which takes care of initializing the UWP XAML Framework inside the current thread of a non-Win32 Desktop app, so that you can start adding UWP controls to it.
- The **DesktopWindowXamlSource** is the actual instance of your Island content. It has a **Content** property which you can instantiate and set with the control you want to render. 

With an instance of the **DesktopWindowXamlSource** class you can attach its HWND to any parent HWND you want from your native Win32 App. This enables any framework that exposes HWND to host a XAML Island, including 3rd party technologies like Java or Delphi.
However, when it comes to WPF and Windows Forms applications, you don’t have to manually do that thanks to the Windows Community Toolkit, since it already wraps these classes into ready-to-be-used controls.

[The Windows Community Toolkit](https://docs.microsoft.com/en-us/windows/communitytoolkit/) is an open-source project, maintained by Microsoft and driven by the community, which includes many custom controls, helpers and service to speed up the development of Windows applications. Starting from version 5.0, the toolkit includes 4 packages to enable XAML Island: 

- One called **XamlHost**. It's a generic control that can host any UWP control, either custom or native. It comes in two variants: [Microsoft.Toolkit.Wpf.UI.XamlHost](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.XamlHost/) for WPF and [Microsoft.Toolkit.Forms.UI.XamlHost](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.XamlHost/) for Windows Forms.
- One called **Controls**, which includes wrappers for 1st party controls like Map or InkCanvas. Thanks to these controls, you'll be able to leverage them like if they're native WPF or Windows Forms control, including direct access to the exposed properties and binding support. Also in this case, it comes into two variants: [Microsoft.Toolkit.Wpf.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls/) for WPF and [Microsoft.Toolkit.Forms.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls/) for Windows Forms.


#### .NET Core 3
.NET Core is a open-source framework built from scratch which brings all the goodies of the .NET Framework into the new modern world. Unlike the full .NET Framework, which has its roots deeply integrated into Windows, .NET Core is cross-platform, lightweight and easily extensible.

Until today, .NET Core has always been focused on supporting these new requirements. As such, its primary workload has always been web or back-end applications. Thanks to .NET Core, you can build easily scalable web applications or APIs that can be hosted on Windows, Linux, or in micro services architectures like Docker containers.

At BUILD 2018 we have announced the next major release of .NET Core, 3.0, which is, without any doubts, the biggest and most ambitious release since the first version. On top of .NET Core 3.0, in fact, we'll be able to build new workloads.

![.NET Core workloads](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/NETCoreWorkloads.png)

As you can see from the image, for the first time .NET Core will support not just web and back-end applications, but also desktop ones which, until today, have always been part only of the full traditional .NET Framework.

> **Disclaimer:** This doesn't mean that WPF and Windows Forms will become cross-platform and you'll be able to run a Windows desktop application, as it is, also on Linux and MacOS. The UI piece of the two frameworks still has a dependency on the Windows rendering system and, as such, it can't run on platforms which use instead a different visual rendering system.
> 

Let's take a look at the msot important benefits of running a desktop application on top of .NET Core.

##### Performance improvements.
Key investments in .NET Core were made around performances. Startup time is much faster and most of the APIs have been rewritten to be fully optimized. This is true for server side and client side workloads.

##### Side-by-side support
One of the biggest blockers for enterprises to adopt newer versions of the .NET Framework is that it can be installed only at system level and it automatically comes with newer version of Windows. This means that if you have an application which targets .NET Framework 4.5 and you want to update it to take advantage of some of the improvements delivered by .NET Framework 4.7, you are forced to update all the applications (or, at least, to make sure they still run well) at the same time. The reason is that you can't install the .NET Framework 4.7 side-by-side with .NET Framework 4.5, but you have to update the existing 4.5 installation. This isn't a nightmare only for enterprises, but a big blocker also for Microsoft. If you look at the recent history of .NET Framework, you will notice how every upgrade brings mainly fixes and minor improvements. The reason is that, since we need to make sure to maintain backward compatibility, the team can't be agile and evolve the framework with changes that, potentially, can break older apps. Checking new code into the .NET Framework requires a long validation and testing period. You can read some thoughts from the team on this in the following article.

.NET Core, instead, can run truly side-by-side, with two different approaches:

- You can embed the runtime inside the application. This way you'll be able to deploy the app on any machine, even without the runtime installed, and make sure it will target the specific .NET Core version you have used to build it.
- You can install multiple .NET Core runtimes on the same machine. Unlike with the .NET Framework, you can have on the same machine .NET Core 1.0, .NET Core 2.0, .NET Core 3.0 and whatever .NET Core version will ship in the future. This means that if you deploy an application which runs on .NET Core 2.0, it will effectively leverage the .NET Core 2.0 runtime and not another runtime in backward compatibility mode.

Additionally, you will be able to leverage many of the benefits of the .NET Core ecosystem, like the opportunity to use the command line tools to create and build your projects or to use the improved .csproj format. In the end, .NET Core 3.0 will bring some specific benefits for desktop development, like a better support to high DPI screens or the opportunity to leverage all the UWP APIs.

##### Why .NET Core 3 for XAML Islands
You should wonder where .NET Core plays a role here. Of course for performance startup and execution of the WPF application: this is the good and immediate gain. The long term advantage is really the fact that this is the migration path for modernizing .NET WPF and Windows Forms applications. All the long-term investments in .NET will be delivered to .NET Core, while the full .NET Framework will focus only on security and feature updates.

___
## Exercise 1 - Use a 1st party UWP control with XAML Islands
We start with the simpliest modernization path possible: We would like to use a rich UWP control that is 'available for use in WPF'. Crazy idea? No! Indeed, the most requested controls are already wrapped for you! The current XAML Islands iteration brings you the InkCanvas, the InkToolbar, the MapControl and the MediaPlayerElement.
So in our Contoso Expenses application we will bring a modern touch by using InkCanvas and MapControl. This will be possible thanks to the Microsoft.Toolkit.Wpf.UI.Controls NuGet package.

### Task 1 - Setup the Contoso Expenses solution
Let's first be sure we can run and debug the Contoso Expenses solution locally.

1.  In the Windows Explorer, create a new local folder like *"C:\XAMLIslandsLab"*. It will be our working folder for the Contoso Dashboard website.
2.  In order to get the source code of the ExpenseIt solution, go to <a href="https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/tree/master/" target="_blank">Windows AppConsult XAMLIslandsLab repository</a>. Click on the **releases** tab and donwload the latest release.

3.  When ready, click on the downloaded file in your browser to open it.

    ![Downloaded file in Chrome](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/SourceCodeDownloaded.png)

4.  Open the zip file and extract all the content to your working folder "C:\XAMLIslandsLab" you've just created.

5.  Open Visual Studio 2019, and double click on the *"C:\XAMLIslandsLab\Lab\Exercise1\01-Start\ContosoExpenses\ContosoExpenses.sln"* file to open the solution.

    ![ContosoExpenses solution in Windows Explorer](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/ContosoExpensesSolution.png)

7.  Verify that you can debug the Contoso Expenses WPF project by pressing the **Start** button or CTRL+F5.

### Task 2 - Reference the "Microsoft.Toolkit.Wpf.UI.Controls" NuGet package
We need this WPF package because it takes care for us about all the necessary piping for XAML Islands. It provides wrapper classes for 1st party controls, such as the InkCanvas, InkToolbar, MapControl, and MediaPlayerElement, all for WPF.

Please note that the same package exists for Windows Forms. Its name is <a href="https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls/" target="_blank">Microsoft.Toolkit.Forms.UI.Controls</a>.

1.  If the Contoso Expenses solution is not opened in Visual Studio, double click on *"Exercise1\01-Start\ContosoExpenses\ContosoExpenses.sln* in the folder where you have extracted the zipped file (it should be *"C:\XAMLIslandsLab"*).
2.  Right click on the **ContosoExpenses** project in the Solution Explorer window on the left and choose **Manage NuGet Packages...**.

    ![Manage NuGet Packages menu in Visual Studio](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/ManageNuGetPackages.png)

3. Search for "Microsoft.Toolkit.Wpf.UI.Controls". The NuGet package from Microsoft.Toolkit will be displayed.

    ![Microsoft.Toolkit.Wpf.UI.Controls NuGet package](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/Microsoft.Toolkit.Wpf.UI.Controls.png)

4.  Click on the **Install** button on the right.

    ![Install Controls NuGet package](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/InstallControlsNuGetPackage.png)

5. You might get the following error.

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/ErrorMinimumNETVersionForControls.png)

This error gives us the opportunity to mention the requirement for the .NET WPF or Windows Forms application: **The .NET Framework version has to be > 4.6.2**. Let's retarget the Contoso Expenses project to this version.

6.  Right click on the **ContosoExpenses** project and choose **Properties**.

    ![Project properties](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/ProjectProperties.png)

7.  In the **Target framework** dropdown listbox, select the **.NET Framework 4.7.2** (as this version is installed on the box).

    ![.NET Framework 4.7.2 version](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/NET472Version.png)

8. Rebuild the project using the **CTRL+SHIFT+B** shortcut in order to check if the retargeting action haven't caused any regression or ask us to do some manual code modifications.

    ```dos
    1>------ Build started: Project: ExpenseItDemo, Configuration: Debug Any CPU ------
    1>  ContosoExpenses -> C:\XAMLIslandsLab\Exercise1\01-Start\ContosoExpenses\ContosoExpenses\bin\Debug\ExpenseItDemo.exe
    ========== Build: 1 succeeded, 0 failed, 1 up-to-date, 0 skipped ==========
    ```

9.  It is now safe to add the **Microsoft.Toolkit.Wpf.UI.Controls** NuGet package to the ContosoExpenses project as explained above.

### Task 3 - Use the InkCanvas control in the application
One of the features that the development team is looking to integrate inside the application is support to digital signature. Managers wants to be able to easily sign the expenses reports, without having to print them and digitalize them back.
XAML Island is the perfect candidate for this scenario, since the Universal Windows Platform includes a control called **InkCanvas**, which offers advanced support to digital pens. Additionally, it includes many AI powered features, like the capability to recognize text, shapes, etc.

Adding it to a WPF application is easy, since it's one of the 1st party controls included in the Windows Community Toolkit we have just installed. Let's do it!

1. Go back to Visual Studio and double click on the **ExpenseDetail.xaml** file in Solution Explorer.
2. As first step, we need to add a new XAML namespace, since the control we need is part of a 3rd party library. Locate the **Window** tag at the top of the XAML file.
3. Copy and paste the following definition as attribute of the **Window** element:

    ```xml
    xmlns:toolkit="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
    ```
    This is how the complete definition should look like:
    
    ```xml
    <Window x:Class="ContosoExpenses.ExpenseDetail"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:toolkit="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
            Loaded="Window_Loaded"
            xmlns:local="clr-namespace:ContosoExpenses"
            mc:Ignorable="d"
            Title="Expense Detail" Height="500" Width="800"
            Background="{StaticResource HorizontalBackground}">
    ```
4. Now we can add the **InkCanvas** control to the page. We're going to add two new rows to the **Grid** to fit it.
5. Add two new **RowDefinition** tags at the end the **Grid.Row** collection: one with **Height** equal to **Auto**, the other one equal to *. This is how the final result should look like:

    ```xml
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    ```
    
6. Now move to the bottom of the XAML file and, inside the **Grid** control, add the following code:

    ```xml
    <TextBlock Text="Signature:" FontSize="16" FontWeight="Bold" Grid.Row="5" />
                
    <toolkit:InkCanvas x:Name="Signature" Grid.Row="6" />
    ```

    The first control is a simple **TextBlock**, used as a header. The second one is real **InkCanvas** control, which is prefixed by the **toolkit** keyword we have defined as namespace, being a 3rd party control.
    
7. That's it! Now we can test the application. Press F5 to launch the debugging experience.
8. Choose an employee from the list, then one of the available expenses.
9. Notice that, in the expense detail page, there's a new space for the **InkCanvas** control. 

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/InkCanvasPenOnly.png)

    If you have a device which supports a digital pen, like a Surface, go on and try to use it. You will see the digital ink appearing on the screen. However, if you don't have a pen capable device and you try to sign with your mouse, nothing will happen. This is happening because, by default, the **InkCanvas** control is enabled only for digital pens. However, we can change this behavior.
10. Stop the debugger and double click on the **ExpenseDetail.xaml.cs** file in Visual Studio.
11. Add the following namespace declaration at the top of the class:

    ```csharp
    using Microsoft.Toolkit.Win32.UI.Controls.Interop.WinRT;
    ```
    
12. Now locate the **ExpenseDetail()** method, which is the public constructor of the class.
13. Add the following line of code right after the **InitializeComponent()** method:

    ```csharp
    Signature.InkPresenter.InputDeviceTypes = CoreInputDeviceTypes.Mouse | CoreInputDeviceTypes.Pen;
    ```
    
    **InkPresenter** is an object exposed by the **InkCanvas** control which we can use to customize the default inking experience. Thanks to the **InputDeviceTypes** we can change which inking devices are supported. By using the values offered by the **CoreInputDeviceTypes** enumerator, we enable pen and mouse.
    
14. Now let's test the application again. Press F5 to start the debugging, then choose one of the employees followed by one of the expenses.
15. Try now to draw something in the signature space with the mouse. This time, you'll see the ink appearing on the screen.

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/Signature.png)
    
However, if you try to play a bit with the application you will notice that not everything is working as expected. Close the expense detail and try to open another expense from the list. You will notice that the application will crash with the following exception:

![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/XamlIslandException.png)

The reason is that every UWP control included in a WPF app through XAML Island must be properly disposed before being instantiated again. As such, we need to take care of this operation when the expense detail page is closed.

1. Go back to Visual Studio and double click the **ExpenseDetail.xaml** file in Solution Explorer.
2. Locate the **Window** tag and add the following attribute:
    
    ```xml
    Closed="Window_Closed"
    ```
    We are handling the **Closed** event, which is triggered when the window is closed. This is how the full definition of the **Window** control should look like:
    
    ```xml
    <Window x:Class="ContosoExpenses.ExpenseDetail"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:toolkit="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
            Loaded="Window_Loaded"
            Closed="Window_Closed"
            xmlns:local="clr-namespace:ContosoExpenses"
            mc:Ignorable="d"
            Title="Expense Detail" Height="500" Width="800"
            Background="{StaticResource HorizontalBackground}">
    ```

3. Now double click on the **ExpenseDetail.xaml.cs** file in Solution Explorer.
4. Copy and paste the following code at the end of the class:

    ```csharp
    private void Window_Closed(object sender, EventArgs e)
    {
        Signature.Dispose();
    }
    ```

    We're invoking the **Dispose()** method exposed by the **InkCanvas** control when the window is closed.
    
5. Now let's test the code. Press F5 to activate the debugger.
6. Choose any employee from the list, then choose one of the available expenses. The expense detail page will be opened.
7. Now close it and choose another expense of the list. This time the updated expense detail page will be opened without any issue.

We have completed our task. Now we have a fully working signature pad in the expense detail page of our application.

___
## Exercise 2 - Integrate the Universal Windows Platform
One of the feedbacks that the developer team has received by managers who are using the Contoso Expenses application is to make easier to locate the place where the expense happened. The current detail page of an expense already shows the full address, but they would like something more visual and easier to understand.
The Universal Windows Platform includes a beautiful and performant control to display maps, which can be leveraged also in a WPF applications thanks to XAML Island. In this exercise we're going to include it.

### Task 1 - Use the MapControl in the application
Like the **InkCanvas** control we have previously added to handle the signature, the **MapControl** is another 1st party control included in the Windows Community Toolkit. As such, we can reuse the same library we have installed in the previous task to add this new control in the detail page of an expense.

1. If you have completed Exercise 1, you can continue working on the same project. Otherwise, open the folder *"Lab/Exercise2/01-Start/ContosoExpenses"* in the location where you have unzipped the lab (it should be *"C:\XAMLIslandLab"* and double click on the **ContosoExpenses.sln** file.
2. We're going to add a new row, right after the full address, with the map control. Look, in the main **Grid** control, for the list of rows inside the **Grid.RowDefinitions** property.
3. Add the following code after the 5th row:

    ```xml
    <RowDefinition Height="*"/>
    ```
    
    This is how the full definition should look like:
    
    ```xml
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    ```
    
4. Now move down in the page and look for the last **StackPanel** control, the one which is used to display the location of the expense:

    ```xml
    <StackPanel Orientation="Horizontal"  Grid.Row="4">
        <TextBlock Text="Location:" FontSize="16" FontWeight="Bold" />
        <TextBlock x:Name="txtLocation" FontSize="16" Margin="5, 0, 0, 0" />
    </StackPanel>
    ```

5. Add right below the following code:

    ```xml
    <toolkit:MapControl Grid.Row="5" x:Name="ExpenseMap" />
    ```

    We're adding the **MapControl** to the page and we're assigning a name to it. We're reusing the same prefix we have defined in the previous exercise, **toolkit**, which points to the namespace **Microsoft.Toolkit.Wpf.UI.Controls**.
    
6. Like we did with the **InkCanvas** control we have added in the previous task, we need to dispose the **MapControl** when the windows gets closed, to avoid multiple instantions that could lead to exceptions. As such, double click on the **ExpenseDetail.xaml.cs** file in Solution Explorer.
7. Locate the **Windows_Closed** event handler you have declared in the previous task.
8. Add the following code before the end of the event handler:

    ```csharp
    ExpenseMap.Dispose();
    ```
9. Now launch the application.
10. Choose one of the employees, then one of the expenses. This is how the updated expense detail page should look like:

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/MapControlPreview.png)
    
As you can see, everything is working as expected. However, the current version of the page isn't really useful. We are indeed seeing a map, but it isn't displaying the exact location where the expense happened. We need to interact with the control in code to achieve this goal. If we explore [the documentation about the MapControl](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.maps.mapcontrol), we can see that it offers a method to center the map to a specifc point called **TrySetViewAsync()**. The method accepts multiple parameters, but the only required one are the coordinates of the location to display, provided with a [Geopoint](https://docs.microsoft.com/en-us/uwp/api/windows.devices.geolocation.geopoint) object.

The Windows Community Toolkit does a good job in supporting the **MapControl** and, as such, it provides some wrappers also for the most important classes which are required to work with it, including the **Geopoint** one. We can find it inside the **Microsoft.Toolkit.Win32.UI.Controls.Interop.WinRT** namespace:

![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/Geopoint.png)

> If you look at the properties exposed by the **Geopoint** class, can you understand which is the challenge we're about to face?

The **Geopoint** class represents geographic coordinates, like latitude and longitude. However, in our database we have only the information about the full address where the expense happened. How can we translate the address into coordinates?
[If we look at the documentation of the Universal Windows Platform](https://docs.microsoft.com/en-us/windows/uwp/maps-and-location/geocoding), we can find that it offers a class called **MapLocationFinder** which supports geocoding (converting an address into coordinates) and reverse geocoding (converting coordinates into an address).

Let's try to use it!

1. Go back to Visual Studio and double click on the **ExpenseDetail.xaml.cs** file in Solution Explorer
2. Locate the **Windows_Loaded** event handler, which contains the code that is esecuted when the window is loaded. It already contains some code to initialize the various information that are displayed in the page about the selected expense, like the type, the description, etc.
3. Move at the end of the event handler and try to reference the **MapLocationFinder** class:

![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/MapLocationFinder.png)

4. Notice how the class can't be found and even Visual Studio doesn't know where to find it. All the proposed quick actions, in fact, are about generating a new property / class / field since it doesn't exist.

> Can you guess why we aren't able to find the **MapLocationFinder** class?

The **MapLocationFinder** class is part of the Universal Windows Platform. Our application, instead, is a WPF application built on top of the .NET Framework. Thanks to the Windows Community Toolkit we have access to wrappers for some modern controls, but still we don't have access to all the APIs and features exposed by the Universal Windows Platform.
In the next exercise we're going to enhance our application so that we can use APIs from the Universal Windows Platform without rewriting it from scratch, thanks to a technology called Desktop Bridge.

In the next tasks we're going to make the **MapControl** we have added more useful, by actually displaying the location where the expense happened. However, as we have discovered at the end of the previous task, we first need to integrate the Universal Windows Platform in our application since the **MapLocationFinder** class, which can convert the address we have into a coordinate, is part of it.

### Task 2 - Package the application with the Desktop Bridge

Before doing this, we need to introduce the Desktop Bridge. Thanks to this technology, we can package our WPF application using the same format of the Universal Windows Platform, which is MSIX (formerly known as AppX, for versions of Windows 10 prior to 1809). Once the application is packaged, it gets an identity, which we'll allow us to integrate the Universal Windows Platform and use most of the available APIs without having to rewrite our WPF application from scratch.

Visual Studio offers an easy way to achieve this goal thanks to a template called **Windows Application Packaging Project**. Let's add it!

1. Go back to Visual Studio. 
2. Right click on the **ContosoExpenses** solution in **Solution Explorer** and choose **Add -> New project**.

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/AddNewProject.png)

3. Select the **Windows Universal** category under **Visual C#** and look for a template called **Windows Application Packaging Project**:

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/WAP.png)
    
4. Name it **ContosoExpenses.Package** and press OK.
5. You will be asked which target SDK and minimum SDK you want to use:

    - **Target SDK** defines which APIs of the Universal Windows Platform you'll be able to use in your application. Choosing the most recent version will allow you to leverage all the latest and greates features offered by the platform.
    - **Minimum SDK** defines which is the minimum Windows 10 version you support. Lower versions won't be able to install this package. In case of a packaged Win32 application, the minimum supported version is Windows 10 Anniversary Update, since it was the first release to support the Desktop Bridge.
    
    For the purpose of our lab make sure to choose the most recent version for both options, as in the following picture:
    
    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/TargetSdk.png)
    
    Then press OK.
6. You will se a new project inside your Visual Studio solution, which structure will resemble the one of a Universal Windows Platform project:

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/WAPdetails.png)
    
    It has a manifest, which describes the application; it has some default assets, which are used for the icon in the Programs menu, the tile in the Start screen, the Store, etc. However, unlike a Universal Windows Platform project, it doesn't contain code. Its purpose is to package an existing Win32 application.
7. The project includes a section called **Applications**, which you can use to choose which applications included in your Visual Studio solution you want to include inside the package. Right click on it and choose **Add reference**.
8. You will see a list of all the other projects included in the solution which, currently, is only the **ContosoExpenses** application:

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/ReferenceManager.png)
    
    Make sure to select it and press Ok.
9. Now expand the **Applications** section. You will notice that the **ContosoExpense** project is referenced and highlighted in bold, which means that it will be used as a starting point for the package. In case of a project with multiple executables, you can set the starting point by clicking on the correct one in the **Applications** list and choosing **Set as entry point**.  However, this isn't our case, so we are ready to go on.
10. That's it! We can now test the packaged version of the application.
11. Right click on the **ContosoExpenses.Package** project in Solution Explore and choose **Set As Startup Project**.
12. Press F5 to launch the debugging. 

Out of the box, you won't notice any meaningful difference. We have simply packaged our WPF application, so it's behaving like the traditional one. However, we can notice some small changes that can help us to understand the application is running as packaged:

- The icon in the taskbar or in the Start screen isn't anymore the icon of our application, but it's the default asset which is included in every UWP project.
- If we right click on the **ContosoExpense.Package** application listed in the Start menu, we will notice that we many options which are typically reserved to applications downloaded from the Microsoft Store, like **App settings**, **Rate and review** or **Share**.
    
    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/StartMenu.png)

- If we want to remove the application from the system, we can just right click again on his icon in the Start menu and choose **Uninstall**. After pressing Ok, the application will be immediately removed, without leaving any leftover on the system.

Now that our application has been packaged with the Desktop Bridge, we can start integrating the Universal Windows Platform.

### Task 3 - Add a reference to the Universal Windows Platform
In order to start using Universal Windows Platform APIs in a WPF application we need to add a reference to two files:

- **Windows.md**, which contains the metadata that describes all the APIs of the Universal Windows Platform.
- **System.Runtime.WindowsRuntime** which is a library that contains the infrastructure required to properly support the **IAsyncOperation** type, which is used by the Universal Windows Platform to handle asynchronous operation with the well known async / await pattern. Without this library your options to interact with the Universal Windows Platform would be very limited, since all the APIs which take more than 50 ms to return a result are implemented with this pattern.

1. Go back to Visual Studio and right click on the **ContosoExpenses** package.
2. Choose **Add reference**.
3. Press the **Browse** button.
4. Look for the following folder on the system: *"C:\Program Files (x86)\Windows Kits\10\UnionMetadata\10.0.17763.0\"*
5. Change the dropdown to filter the file types from **Component files** to **All files**. This way, the **Windows.md** file will become visible.

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/WindowsMd.png)
    
6. Select it and press **Add**.
7. Now press again the **Browse** button.
8. This time look for the following folder on the system: *"C:\Windows\Microsoft.NET\Framework\v4.0.30319"*
9. Look for a file called **System.Runtime.WindowsRuntime.dll**, select it and press Ok.
10. Now expand the **References** section of the **ContosoExpenses** project in Solution Explorer and look for the **Windows** reference.

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/WindowsReference.png)
    
11. Select it, right it click on it and choose **Properties**.
12. Change the value of the **Copy Local** property from **True** to **False**.

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/CopyLocal.png)

You're all set. Now you're ready to start using APIs from the Universal Windows Platform.

### Task 4 - Display the expense location on the map
Now that we have enabled the Universal Windows Platform in our WPF project, we can start using the **MapLocationFinder** class we have mentioned before, which can help us to convert the address of the expense location to a set of coordinates we can use with the **MapControl**.

1. Go back to Visual Studio and double click on the **ExpenseDetail.xaml.cs** file in Solution Explorer
2. Add a reference to the correct namespace, by including the following statement at the top of the class:

    ```csharp
    using Windows.Services.Maps;
    ```
3. Look for the **Window_Loaded** event handler.
4. Copy and paste the following code at the end of the handler, after the initialization of the other controls in the page:

    ```csharp
    var result = await MapLocationFinder.FindLocationsAsync(SelectedExpense.Address, null);
    var location = result.Locations.FirstOrDefault();
    if (location != null)
    {
        await ExpenseMap.TrySetViewAsync(location.Point, 13);
    }
    ```
    
    The **MapLocatioNFinder** class exposes a method called **FindLocationAsync()** which is perfect for our scenario. As parameters, we need to pass the address we want to convert into coordinates and, optionally, a coordinate that can be used as a reference for the search. In our case, we pass the address of the current expense (stored in the **Address** property of the **SelectedExpense** object) and **null** as second parameter, since we don't have any coordinate to use as a reference. This method is asynchronous, so we need to invoke it with the **await** prefix.
    The method returns a list of possible locations, represented by the **MapLocation** object. We take the first result and, if it's not null, we call the **TrySetViewAsync()** method exposed by the **MapControl**. As parameter, we specify the location's coordinate (stored in the **Point** property) and the zoom level. Also this method is asynchronous, so we prefix the **await** keyword.
5. As it is, the code won't compile. We are using two asynchronous methods inside the event handler, but we haven't added the **async** prefix. Change the signature of the **Windows_Loaded** handler to include the **async** keyword. This is how the final event handler should look like:

    ```csharp
    private async void Window_Loaded(object sender, RoutedEventArgs e)
    {
        txtType.Text = SelectedExpense.Type;
        txtDescription.Text = SelectedExpense.Description;
        txtLocation.Text = SelectedExpense.Address;
        txtAmount.Text = SelectedExpense.Cost.ToString();
        Chart.Height = (SelectedExpense.Cost * 400) / 1000;
    
        var result = await MapLocationFinder.FindLocationsAsync(SelectedExpense.Address, null);
        var location = result.Locations.FirstOrDefault();
        if (location != null)
        {
            await ExpenseMap.TrySetViewAsync(location.Point, 13);
        }
    }
    ```

Now it's time to test the code!

1. Make sure that the **ContosoExpenses.Package** project in Solution Explorer is still set as startup project. Otherwise, right click on it and choose **Set As Startup Project**.
2. Press F5 to launch the debugging experience.
3. Choose any employee from the list, then one of the available expenses.
4. The detail page will open up and... the map will stay still like at the end of Exercise 1.
5. Close the window and try to open another expense. The map will continue to be stuck.

> Can you understand what's happening? Hint: the debugger can help!

Let's see what's happening.

1. Go back in Visual Studio and double click on the **ExpenseDetail.xaml.cs** file.
2. Set a breakpoint on the line immediately after the usage of the **MapLocationFinder.FindLocationsAsync()** method.
3. Now press again F5 to launch the debugging experience.
4. Choose any employee from the list, then one of the available expenses.
5. The detail page will open up. Wait for the breakpoint to be hit and explore the properties exposed by the **result** object:

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/InvalidCredentials.png)
    
As you can see, the **Status** property has the value **InvalidCredentials**. The **MapControl**, in fact, is subject to the Bing Maps licensing and, as such, many of these services can't be used without a valid license. You may have noticed another symptom of this requirement. When the application is running, below the **MapControl** you can see a red bold message stating **Warning: MapServiceToken not specified**.

![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/MapServiceToken.png)

Let's move on and see how we can request a license and integrate it into our application.

1. Open a browser on your machine. If you're using the VM setup for this lab, you can use Microsoft Edge.
2. Go to the website [https://www.bingmapsportal.com/](https://www.bingmapsportal.com/).
3. Press the sign-in button.
4. Login with your personal Microsoft Account. It must be a personal account, like @outlook.com. You can't use an Office 365 account. In case you don't have one, feel free to skip the following steps. You will find a license to use later in the exercise.
5. Once you're logged in the main dashboard, choose **My account -> My keys**.

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/BingDevMyKeys.png)
    
6. Click on the **here** link in the section **Click here** to create a new key.

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/BingDevCreateNewKey.png)

7. Fill the following information:

    - **Application name**: feel free to choose the name you prefer.
    - **Application URL**: leave it empty.
    - **Key type**: choose **Basic**.
    - **Application type**: choose **Private Windows App (UWP, 8.x and earlier)**
    
    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/BingDevCreateKey.png)
    
8. Press the **Create** button.
9. You will see a new item in the **My keys** section with all the info you have just provided.

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/BingDevKeyInfo.png)
    
10. Press the **Show key** button and copy the that will be displayed.
11. Now go back to Visual Studio and double click on the **ExpenseDetail.xaml.cs** file in Solution Explorer.
12. Look for the constructor of the class, which is the **ExpenseDetail()** method.
13. Copy and paste the following code at the end of the method:

    ```csharp
    MapService.ServiceToken = "<add your key here>";
    ```

    Replace the string value with the key you have just copied from the Bing Dev portal. If you weren't able to retrieve a key, feel free to use the following one:
    
    ```csharp
    MapService.ServiceToken = "SstdhSuxgHWMVoqmrpla~PRzuxyU3RgB7e-csBqBIfA~AizgpHwv-MER89vjeuJ7LVP01a7gacUbusArACn1x7BcalbDhY23o0pt6L7AhZe";
    ```

14. Let's test the code again! Make sure that the **ContosoExpenses.Package** project is still selected as startup and press F5.
15. Choose one employee from the list, then one of the available expenses.
16. If you did everhting correctly, you should notice that:

    - After an animation, the map will be centered and zoomed on the location of the expense
    - The warning message under the **MapControl** will be gone
    
    
    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/MapControlOk.png)

Great job! Now you have a WPF application which perfecly integrates two UWP controls, **InkCanvas** and **MapControl**. Additionally, since we have packed our application with the Desktop Bridge, we have the chance to leverage APIs from the Universal Windows Platform, to make it even more powerful. The Desktop Bridge opens up also the opportunity to release our application using the new MSIX format, which supports not only traditional deployment models (like web, SSCM, Intune, etc.) but also new ones like the Microsoft Store / Store for Business / Store for Education.

___
## Exercise 3 - Integrate a custom UWP XAML component
The company has recently gone after a big hardware refresh and now all the managers are equipped with a Microsoft Surface or other touch equipped devices. Many managers would like to use the Contoso Expenses application on the go, without having to attach the keyboard, but the current version of the application isn't really touch friendly. The development team is looking to make the application easier to use with a touch device, without having to rewrite it from scratch with another technology.
Thanks to XAML Island, we can start replacing some WPF controls with the UWP counterpart, which are already optimized for multiple input experiences, like touch and pen.

The development team has decide to start modernizing the form to add a new expense, by making easier to choose the expense date with a touch device. The Universal Windows Platform offers a control called **CalendarView**, [which is perfect for our scenario](https://docs.microsoft.com/en-us/windows/uwp/design/controls-and-patterns/calendar-view). It's the same control that it's integrated in Windows 10 when you click on the date and time in the taskbar:

![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/CalendarViewControl.png)

However, it isn't included as a 1st party control in the Windows Community Toolkit, so we'll have to use the generic XAML Host control.

### Task 1 - Add the WindowsXamlHost control
1. You can use the output of Exercise 2 as a starting point. In case you haven't completed it, you can open the folder *"Lab/Exercise3/01-Start/ContosoExpenses"* in the location where you have unzipped the lab (it should be *"C:\XAMLIslandLab"*) and double click on the **ContosoExpenses.sln** file.
2. Regardless of your starting point, the required NuGet package should be already installed. We can verify this by right clicking on the **ContosoExpenses** project in Solution Explorer, choosing **Manage NuGet packages** and moving to the **Installed** tab.

    ![Manage NuGet Packages menu in Visual Studio](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/ManageNuGetPackages.png)

3. We should see a packaged called **Microsoft.Toolkit.Wpf.UI.XamlHost**.

    ![Microsoft.Toolkit.Wpf.UI.XamlHost NuGet Package](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/XamlHostNuGetPackages.png)

    The package is already installed because the one we have installed for exercises 1 and 2, **Microsoft.Toolkit.Wpf.UI.Controls**, has a dependency on it. As such, when we have installed it in the previous exercises, NuGet automatically downloaded and installed also the **Microsoft.Toolkit.Wpf.UI.XamlHost** package.
4. Now we can start editing the code to add our control. Locate, in Solution Explorer, the file called **AddNewExpense.xaml** and double click on it. This is the form used to add a new expense to the list. Here is how it looks like in the current version of the application:

    ![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/AddNewExpense.png)
    
    As you can notice, the date picker control included in WPF is meant for traditional computers with mouse and keyboard. Choosing a date with a touch screen isn't really feasible, due to the small size of the control and the limited space between each day in the calendar.
    
5. We can see the current date picker implemented using the standard WPF control towards the end of the XAML file:

    ```xml
    <DatePicker x:Name="txtDate" Grid.Row="6" Grid.Column="1" Margin="5, 0, 0, 0" Width="400" />
    ```

6. We're going to replace this control with the **WindowsXamlHost** one, which allows hosting any UWP control inside our WPF application. However, first, we need to add a new namespace to the page. Scroll to the top of the page, identify the **Window** tag and add the following attribute:

    ```xml
    xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
    ```

    This is how the full definition should look like:
    
    ```xml
    <Window x:Class="ContosoExpenses.AddNewExpense"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
            xmlns:local="clr-namespace:ContosoExpenses"
            mc:Ignorable="d"
            Title="Add new expense" Height="450" Width="800"
            Background="{StaticResource AddNewExpenseBackground}">
    ```
    
7. Now replace the **DatePicker** control you have previously identified in the XAML page with the following one:

    ```xml
    <xamlhost:WindowsXamlHost InitialTypeName="Windows.UI.Xaml.Controls.CalendarView" Grid.Column="1" Grid.Row="6" Margin="5, 0, 0, 0" x:Name="CalendarUwp"  />
    ```

    We have added the **WindowsXamlHost** control, by using the **xamlhost** prefix we have just defined. The most important property to setup the control is **InitialTypeName**: you must specify the full name of the UWP control you want to host. In our case, we specify the full signature of the **CalendarView** control, which is **Windows.UI.Xaml.Controls.CalendarView**.
    
8. Let's test the work now. In order to compile our project, we need to make a small change. Locate the **AddNewExpense.xaml.cs** file in Solution Explorer and double click on it.
9. You will notice that there's a compilation error in the file. The **OnSaveExpenses()** method, in fact, contains the following code snippet:

    ```csharp
    Expense expense = new Expense
    {
        Address = txtAmount.Text,
        City = txtCity.Text,
        Cost = Convert.ToDouble(txtAmount.Text),
        Description = txtDescription.Text,
        Type = txtType.Text,
        Date = txtDate.SelectedDate.GetValueOrDefault(),
        EmployeeId = EmployeeId
    };
    ```

    However, we have removed the WPF DatePicker control with name **txtDate** from the XAML page and, as such, the following line will return an error:
    
    ```csharp
    Date = txtDate.SelectedDate.GetValueOrDefault()
    ```
    
    For the moment, let's just comment it:
    
    ```csharp
    //Date = txtDate.SelectedDate.GetValueOrDefault(),
    ```

Now press F5 to build and run the application. Once it starts, choose any employee from the list, then press the **Add new expense** button at the bottom of the list. You will notice how the WPF DatePicker control has been replaced with a full calendar view, which is more touch friendly. 

![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/CalendarViewWrapper.png)

However, the work isn't completed yet. We need a way to handle the selected date, so that we can display it on the screen and we can store it in the code-behind, so that we can populate the new **Expense** object that gets saved in the database.

### Task 2 - Interact with the WindowsXamlHost control
Let's take a look [at the documentation](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.CalendarView) of the **CalendarView** control. There are two things which are relevant for our scenario:

- The **SelectedDates** property, which contains the date selected by the user.
- The **SelectedDatesChanged** event, which is raised when the user selects a date.
    
Let's go back to the **AddNewExpense.xaml** page and handle them.

> Can you guess which is the challenge here?

The **WindowsXamlHost** control is a generic host control for any kind of UWP control. As such, it doesn't expose any property called **SelectedDates** or any event called **SelectedDatesChanged**, since they are specific of the **CalendarView** control.
In order to implement our scenario, we need to move to the code behind and cast the **WindowsXamlHost** to the type we expect, in our case the **CalendarView** one. The best place to do is when the **ChildChanged** event is raised, which is triggered when the guest control has been rendered.

1. Double click on the **AddNewExpense.xaml** file in Solution Explorer in Visual Studio
2. Identify the **WindowsXamlHost** control you have previously added and subscribe to the **ChildChanged** event:

    ```xml
    <xamlhost:WindowsXamlHost InitialTypeName="Windows.UI.Xaml.Controls.CalendarView" Grid.Column="1" Grid.Row="6" Margin="5, 0, 0, 0" x:Name="CalendarUwp"  ChildChanged="CalendarUwp_ChildChanged" />
    ```
    
3. Before moving on to the code behind, we need to add a **TextBlock** control to display the value selected by the user. First add a new **RowDefinition** with **Height** equal to **Auto** at the end of the **Grid.RowDefinitions** collection of the main **Grid**. This is how the final setup should look like:

    ```xml
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
    </Grid.RowDefinitions>
    ```

4. Copy and paste the following code after the **WindowsXamlHost** control and before the **Button** one at the end of the XAML page:

    ```xml
    <TextBlock Text="Selected date:" FontSize="16" FontWeight="Bold" Grid.Row="7" Grid.Column="0" />
    <TextBlock x:Name="txtDate" FontSize="16" Grid.Row="7" Grid.Column="1" />
    ```

5. Locate the **Button** control at the end of the XAML page and change the **Grid.Row** property from **7** to **8**:

    ```xml
    <Button Content="Save" Grid.Row="8" Grid.Column="0" Click="OnSaveExpense" Margin="5, 12, 0, 0" HorizontalAlignment="Left" Width="180" />
    ```
    
    Since we have added a new row in the **Grid**, we need to shift the button of one row.
    
6. There's one last small change we need to make. If you remember when we have tested the application at the end of the previous task, the look and feel of the window wasn't really good. The reason is that the **CalendarView** control takes more space than the previous **DatePicker** and, as such, the current size of the window isn't enough to fit all the content. Let's incrase the height of the window. 
7. Locate the **Window** tag at the top of the XAML file.
8. Locate the **Height** property and change the value from **400** to **800**.
9. Also the **WindowsXamlHost** control must be properly disposed in order to be reused. As such, we need to subscribe to the **Closed** event also in this window, by adding the following attribute:

    ```xml
    Closed="Window_Closed"
    ```
    
    This is how the final definition of the **Window** element should look like:
    
    ```xml
    <Window x:Class="ContosoExpenses.AddNewExpense"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
            xmlns:local="clr-namespace:ContosoExpenses"
            mc:Ignorable="d"
            Closed="Window_Closed"
            Title="Add new expense" Height="800" Width="800"
            Background="{StaticResource AddNewExpenseBackground}">
    ```

10. Now let's start to work on the code behind. Identify in Solution Explorer the **AddNewExpense.xaml.cs** file and double click on it.
11. First we need a property to hold a reference to the selected date. Copy and paste the following definition inside the class:

    ```csharp
    private DateTime SelectedDate;
    ```

11. Now copy and paste the following event handler inside the class definition:

    ```csharp
    private void CalendarUwp_ChildChanged(object sender, EventArgs e)
    {
        WindowsXamlHost windowsXamlHost = (WindowsXamlHost)sender;
    
        Windows.UI.Xaml.Controls.CalendarView calendarView =
            (Windows.UI.Xaml.Controls.CalendarView)windowsXamlHost.Child;
    
        if (calendarView != null)
        {
            calendarView.SelectedDatesChanged += (obj, args) =>
            {
                if (args.AddedDates.Count > 0)
                {
                    SelectedDate = args.AddedDates.FirstOrDefault().DateTime;
                    txtDate.Text = SelectedDate.ToShortDateString();
                }
            };
    
            calendarView.MinDate = DateTimeOffset.Now.AddYears(-1);
            calendarView.MaxDate = DateTimeOffset.Now;
        }
    }
    ```

    We are handling the **ChildChanged** event we have previously subscribed to. As first step, we retrieve a reference to the **WindowsXamlHost** control which triggered it. The control exposes a property called **Child**, which hosts the UWP control we have assigned with the **InitialTypeName** property. We retrieve this property and we cast it to the type of control we're hosting, which in our case is **Windows.UI.Xaml.Controls.CalendarView**. Now we have access to the full UWP control, so we can:
    
    - Subscribe to the **SelectedDatesChanged** event, which is triggered when the user selects a date from the calendar. Inside this handler, thanks to the event arguments, we have access to the **AddedDates** collection, which contains the selected dates. In our case we're using the **CalendarView** control in single selection mode, so the collection will contain only one element. We store it into the **SelectedDate** property we have previously created and we display it in the **txtDate** control.
    - Customize the behavior of the control. Since, for compliance reasons, an employee can report only expenses occurred in the last year, it would be confusing to display dates older than 1 year or in the future. As such, we set the **MaxDate** property with the current date, while the **MinDate** one with the same date, but 1 year in the past. This means that if, for example, today is 14th February 2019, employees will be able to choose a date between 14th February 2018 and 14th February 2019.

12. As next step, we need to handle the **Closed** event of the window to dispose the **WindowsXamlHost** control. Copy and paste the following event handler before the end of the **AddNewExpense** class:

    ```csharp
    private void Window_Closed(object sender, EventArgs e)
    {
        CalendarUwp.Dispose();
    }
    ```
13. As last step we need to update the **OnSaveExpense** event handler to retrieve the selected date from the new UWP control we have added. If you remember, in the previous task we have commented the following line of code in the creation of the **Expense** object:

    ```csharp
    Date = txtDate.SelectedDate.GetValueOrDefault(),
    ```

    Delete it and replace it with the following code:
    
    ```csharp
    Date = SelectedDate,
    ```

    We're setting the **Date** property of the **Expense** object we're going to store into the database with the value we have previously stored in the **ChildChanged** event. This is how the final definition of the **Expense** object should look like:
    
    ```csharp
    Expense expense = new Expense
    {
        Address = txtAmount.Text,
        City = txtCity.Text,
        Cost = Convert.ToDouble(txtAmount.Text),
        Description = txtDescription.Text,
        Type = txtType.Text,
        Date = SelectedDate,
        EmployeeId = EmployeeId
    };
    ```

We're done! Let's test again the project:

1. Press F5 and launch the application
2. Choose one of the available employees, then click the **Add new expense** button.
3. Fill all the information in the form and choose one date from the new **CalendarView** control.
4. Press the **Save** button.
5. The form will be closed and, in the list of expenses, you will find the new one you have just created at the end of the list. Take a look at the first column with the expense date: it should be exactly the one you have selected in the **CalendarView** control.

We have replaced an existing WPF control with a newer mordern version, which fully supports mouse, keyboard, touch and digital pens. Despite the fact that it isn't included as 1st party control in the Windows Community Toolkit, we've been able anyway to include a **CalendarView** control in our application and to interact with it.

___
## Exercise 4 - Create a XAML Island wrapper
From a technical point of view, the outcome of the previous code works without issues. However, the code we have written isn't super elegant. In order to interact with the **CalendarView** control we had to subscribe to the **ChildChanged** event exposed by the **WindowsXamlHost** control, peform a cast and manually change some properties. Additionally, if we have a more complex application built with the MVVM pattern, we would have faced a blocker: we can't use binding to handle the **SelectedDates** property.

We can solve this problem by creating our own wrapper to the UWP control we want to integrate, exactly like the **MapControl** or the **InkCanvas** controls. The purpose of this wrapper is to take the properties and events exposed by UWP control and forward them to the WPF control, so that they could be directly access like with a native .NET control. Let's start!

### Task 1 - Create a basic wrapper

1. If you have completed Exercise 3, you can start from the outcome of it. Otherwise, open the folder *"Lab/Exercise4/01-Start/ContosoExpenses"* from the location where you have unzipped the lab (it should be *"C:\XAMLIslandLab*") and double click on the **ContosoExpenses.sln** file.
2. First we need to create the wrapper control. Right click on the **ContosoExpenses** project in Solution Explorer and choose **Add -> Class**. 
3. Name it **CalendarViewWrapper** and press OK.
4. Add a reference to the following namespaces at the top of the class:

    ```csharp
    using Microsoft.Toolkit.Win32.UI.XamlHost;
    using Microsoft.Toolkit.Wpf.UI.XamlHost;
    ```
5. The first step is to make the class public and to inherit from the **WindowsXamlHostBase**. This is how the definition should look like:

    ```csharp
    public class CalendarViewWrapper: WindowsXamlHostBase
    {
        public CalendarViewWrapper() : base()
        {
    
        }
    }
    ```
    
    We have added a public constructor, which does nothing more than implementing the same base costructor of the **WindowsXamlHostBase** class.
6. The next step is to initialize the control with the UWP control we want to host, in our case the **CalendarView** one. Copy and paste the following code inside the class:

    ```csharp
    protected override void OnInitialized(EventArgs e)
    {
        base.OnInitialized(e);
        this.ChildInternal = UWPTypeFactory.CreateXamlContentByType("Windows.UI.Xaml.Controls.CalendarView");
    
        SetContent();
    }
    ```
    
    We're overriding the **OnInitialized** event and, inside it, we're using a class provided by the Windows Community Toolkit called **UWPTypeFactory**. Thanks to the **CreateXamlContentByType()** method we can manually create a new instance of the UWP control we need. This code has the same effect of setting the **InitialTypeName** property on the **WindowsXamlHost** control as we did in Exercise 3. Once we have a reference to the UWP control, we assign it to the **ChildInternal** property, which is the host. In the end, we call the **SetContent()** to finalize the operation.

7. Now that we have a basic wrapper, we can use it to replace the **WindowsXamlHost** control we have previously added. Double click on the **AddNewExpense.xaml** file in Solution Explorer and locate the **WindowsXamlHost** control:

    ```xml
    <xamlhost:WindowsXamlHost InitialTypeName="Windows.UI.Xaml.Controls.CalendarView" Grid.Column="1" Grid.Row="6" Margin="5, 0, 0, 0" x:Name="CalendarUwp" ChildChanged="CalendarUwp_ChildChanged"/>
    ```
8. Delete it and replace it with the following snippet:

    ```xml
    <local:CalendarViewWrapper x:Name="CalendarUwp" Grid.Column="1" Grid.Row="6" Margin="5, 0, 0, 0" />
    ```

    We are simply referencing the **CalendarViewWrapper** control we have just created, using the **local** prefix which is already included in the **Window** definition and which points to the default namespace of the project:
    
    ```xml
    xmlns:local="clr-namespace:ContosoExpenses"
    ```
    
We're ready to start performing a first test. Press F5 and launch the application, then select one of the available employees and press the **Add new expense** button. You should see the same visual output of the previous exercise:

![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/CalendarViewWrapper.png)

However, the current iteration isn't really useful. If you click on any date, nothing will happen. And the calendar isn't constrained anymore to show only the dates from the past year. We need to customize our wrapper in order to expose the properties we need.

### Task 2 - Add properties to the wrapper
Let's start by adding some properties to our wrapped control. For our scenario, we need to expose 3 properties of the original UWP control:

- **SelectedDates** to get the date selected by the user
- **MinDate** and **MaxDate** to set the calendar's range

1. Double click on the **AddNewExpense.xaml.cs** in the Solution Explorer 
2. Copy and paste the following code snippet inside the class:

    ```csharp
    public IList<DateTimeOffset> SelectedDates
    {
        get
        {
            if (this.ChildInternal != null)
            {
                Windows.UI.Xaml.Controls.CalendarView calendarView = this.ChildInternal as Windows.UI.Xaml.Controls.CalendarView;
                return calendarView.SelectedDates;
            }
    
            return null;
        }
    }
    ```

    We are exposing a public property called **SelectedDates**, which type matches the same one of the property exposed by the **CalendarView** control, which is **IList<DateTimeOffset>**. This property is read-only, as such we can implement only the **get**. First we check that the **ChildInternal** property isn't null, to make sure that we are indeed hosting a control inside our wrapper. If that's the case, we can reuse the property to get a reference to the original **CalendarView** control. In the end, we simply return the value of the **SelectedDates** property exposed by the control.
    
3. Now we need to expose the **MinDate** and **MaxDate** properties, so that we can customize the behavior of the calendar. Let's start with the first one:

    ```csharp
    private DateTimeOffset minDate;
    
    public DateTimeOffset MinDate
    {
        get { return minDate; }
        set
        {
            if (this.ChildInternal != null)
            {
                minDate = value;
                Windows.UI.Xaml.Controls.CalendarView calendarView = this.ChildInternal as Windows.UI.Xaml.Controls.CalendarView;
                calendarView.MinDate = value;
            }
        }
    }
    ```
    
    The approach is the same we have previously used. However, in this case, we need to expose also the **set**, since we need to change the value of the real property exposed by the **CalendarView** control. However, the implementation is basically the same. We check if the **ChildInternal** control isn't null and, if that's the case, we get a reference to the hosted **CalendarView** control. In this end, we set the **MinDate** property with the assigned value.
    
4. Now let's add the **MaxDate** property, which works in the same way:

    ```csharp
    private DateTimeOffset maxDate;
    
    public DateTimeOffset MaxDate
    {
        get { return maxDate; }
        set
        {
            if (this.ChildInternal != null)
            {
                maxDate = value;
                Windows.UI.Xaml.Controls.CalendarView calendarView = this.ChildInternal as global::Windows.UI.Xaml.Controls.CalendarView;
                calendarView.MaxDate = value;
            }
        }
    }
    ```
    
5. Now that the wrapped control is exposing the properties we need, we can start to integrate them in code behind. Double click on the **AddNewExpense.xaml.cs** file.
6. First locate the **CalendarUwp_ChildChanged** event handler and delete it. We don't need it anymore, since the we have replaced the **WindowsXamlHost** control with the wrapped one.
7. Now we need to initialize the **MinDate** and **MaxDate** properties. We need to do it when the window is loaded, so we need to subscribe to the **Loaded** event exposed by the **Window**. Double click on the **AddNewExpense.xaml** file, locate the **Window** tag at the top of the page and add the following attribute:

    ```xml
    Loaded="Window_Loaded"
    ```
    
    This is how the full definition of the **Window** control should look like:
    
    ```xml
    <Window x:Class="ContosoExpenses.AddNewExpense"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
            xmlns:local="clr-namespace:ContosoExpenses"
            mc:Ignorable="d"
            Closed="Window_Closed"
            Loaded="Window_Loaded"
            Title="Add new expense" Height="800" Width="800"
            Background="{StaticResource AddNewExpenseBackground}">
    ```
8. Now open again the **AddNewExpense.xaml.cs** file and copy and paste the following event handler inside th class:

    ```csharp
    private void Window_Loaded(object sender, RoutedEventArgs e)
    {
        CalendarUwp.MinDate = DateTimeOffset.Now.AddYears(-1);
        CalendarUwp.MaxDate = DateTimeOffset.Now;
    }
    ```

    As we did in Exercise 3, we are setting the **MinDate** property with the current date, but one year back, and the **MaxDate** property with the current date. The difference is that, since this time we are providing wrapper properties, we can directly set them, instead of having to go through the **ChildChanged** event.
    
Let's test our work. Press F5 to launch the application, choose an employee from the list, then press the **Add new expense** button. This time you should see that the calendar will allow you to choose only a date between the current date and one year ago. Now we need to handle the **SelectedDates** property.

> Are we able to implement the same behavior of Exercise 3 with the current wrapper?

The answer is no. We have exposed the **SelectedDates** collection of the **CalendarView** control, but we don't have a way to know when the user has indeed selected a date. In Exercise 3 we have achieved this goal by subscribing to the **SelectedDateChanged** event. As such, we need to wrap also this event in our custom control. This will be the goal of our next task.

### Task 3 - Add events to the wrapper

An event is represented by the **EventHandler<T>** class, where **T** is the object which represents the event arguments. It's a special object which is returned to the event handler, where we can store relevant information about the event. [If we take a look at the documentation](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.calendarview.selecteddateschanged) about the **SelectedDatesChanged** event of **CalendarView** control, [we can see](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.calendarviewselecteddateschangedeventargs) that it returns an object of type **CalendarViewSelectedDatesChangedEventArgs**, which contains two collections: **AddedDates** and **RemovedDates**. 
We're going to recreate a similar class in our project for our custom event handler.

1. Right click on the **ContosoExpenses** project in Visual Studio and choose **Add -> Class**.
2. Name it **SelectedDatesChangedEventArgs** and press OK.
3. Replace the existing class definition with the following one:

    ```csharp
    public class SelectedDatesChangedEventArgs: EventArgs
    {
        public IReadOnlyList<DateTimeOffset> SelectedDates { get; set; }
    
        public SelectedDatesChangedEventArgs(IReadOnlyList<DateTimeOffset> selectedDates)
        {
            this.SelectedDates = selectedDates;
        }
    }
    ```

    Every custom event arguments object must inherit from the base **EventArgs** class. Then we can add our custom properties. In our case, we just add a property of type **IReadOnlyList<DateTimeOffset>**, which is the same type of the **AddedDates** collection of the original **CalendarView** control.
    
4. Now we can implement our event handler. Double click on the **CalendarViewWrapper.cs** file in Solution Explorer and add the following code inside the class:

    ```csharp
    public event EventHandler<SelectedDatesChangedEventArgs> SelectedDatesChanged;
    ```
    
    We're exposing a public event thanks to the **EventHandler** class. We specify, as returned parameter, the custom event arguments class we have just created, called **SelectedDatesChangedEventArgs**.
    
5. Now we need to expose a method to invoke whenever we want to trigger our event. Copy and paste the following code inside the class:

    ```csharp
    protected virtual void OnSelectedDatesChanged(SelectedDatesChangedEventArgs e)
    {
        SelectedDatesChanged?.Invoke(this, e);
    }
    ```
    
6. Now we need to forward the original event exposed by the **CalendarView** control to the custom one we have just created. This way, we'll be able to leverage this event directly from our wrapped control. As first step, we need to subscribe to the original event inside the **OnInitialize()** method we have previously implemented. Copy and paste the following line after the **SetContent()** method:

    ```csharp
    Windows.UI.Xaml.Controls.CalendarView calendarView = this.ChildInternal as Windows.UI.Xaml.Controls.CalendarView;
    calendarView.SelectedDatesChanged += CalendarView_SelectedDatesChanged;
    ```
    
    This is how the final method should look like:
    
    ```csharp
    protected override void OnInitialized(EventArgs e)
    {
        base.OnInitialized(e);
        this.ChildInternal = UWPTypeFactory.CreateXamlContentByType("Windows.UI.Xaml.Controls.CalendarView");
    
        SetContent();
    
        Windows.UI.Xaml.Controls.CalendarView calendarView = this.ChildInternal as Windows.UI.Xaml.Controls.CalendarView;
        calendarView.SelectedDatesChanged += CalendarView_SelectedDatesChanged;
    }
    ```
    
    We have simply retrieved a reference to the hosted **CalendarView** control through the **ChildInternal** property and we have subscribed to the **SelectedDatesChanged** event.
    
7. As last step, let's implement the event handler:

    ```csharp
    private void CalendarView_SelectedDatesChanged(Windows.UI.Xaml.Controls.CalendarView sender, Windows.UI.Xaml.Controls.CalendarViewSelectedDatesChangedEventArgs args)
    {
        OnSelectedDatesChanged(new SelectedDatesChangedEventArgs(args.AddedDates));
    }
    ```
    
    We are invoking the method we have previously created, passing as parameter a new instance of our custom event args object. Inside this instance, we include the collection of selected dates retrieved from the **AddedDates** collection.
    
8. That's it. Now that our wrapped control exposes an event handler, we can use it to handle the selection of the date. Double click on the **AddNewExpense.xaml** page in Solution Explorer and locate the **CalendarViewWrapper** control we have previously added.
9. Subscribe to the **SelectedDatesChanged** event. This is how the final definition of the control should look like:

    ```csharp
    <local:CalendarViewWrapper x:Name="CalendarUwp" Grid.Column="1" Grid.Row="6" Margin="5, 0, 0, 0" SelectedDatesChanged="CalendarUwp_SelectedDatesChanged"/>
    ```
    
10. Now double click on the **AddNewExpense.xaml.cs** file in Solution Explorer and add the following event handler inside the class:

    ```csharp
    private void CalendarUwp_SelectedDatesChanged(object sender, SelectedDatesChangedEventArgs e)
    {
        SelectedDate = e.SelectedDates.FirstOrDefault().Date;
        txtDate.Text = SelectedDate.ToShortDateString();
    }
    ```
    
    We're retrieved from the event args object the selected date, which is stored inside the **SelectedDates** collection. Since we're using the control in single selection mode, we just retrieve the first element. Then we store the date inside the **SelectedDate** property, which is the one defined at class level that we use to populate the **Expense** object that will be saved in the database. In the end, we display the selected date in a **TextBlock** control on the page.
    
Now we can test the code. Press F5 to launch the application, choose an employee from the list and press the **Add new expense** button. Now the control should behave like at the end of Exercise 4:

- By clicking on a date, you will see the selected date displayed under the calendar.
- If you press **Save** and you look at the **Date** column of newly added expense, you will see the same date selected in the calendar.

![](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/CalendarViewWrapperFinal.png)

That's it! Our wrapper is working and it makes easier to interact with the original UWP control directly from the WPF XAML. As optional task, you can try to change the properties we have created (**SelectedDates**, **MinDate** and **MaxDate**) to [dependencies properties](https://docs.microsoft.com/en-us/dotnet/framework/wpf/advanced/dependency-properties-overview), so that they can properly support binding.

___
## Exercise 5 - Migrate to .NET Core
Migrating the application to .NET Core 3 is, from far, the best and recomanded path for modernizing a .NET application (WPF or Windows Forms). As previously mentionned, the first really nice improvment is about the startup and execution time! This is only the emerged part of the iceberg. The best advantage is that, the app will be able to use all the upcoming new features both from .NET Core and UWP! 

### Task 1 - Setup for using .NET Core
At the moment of writing .NET Core is still in Preview and it is highly experimental technologies. Nevertheless, it is enough stable to play with it. The minimum required is made of two pieces:
- The .NET Core 3 runtime - https://github.com/dotnet/core-setup
- The .NET Core 3 SDK - https://github.com/dotnet/core-sdk

Do not worry, using the VM provided, all is already setup for you: You do not have to download and install anything. On the other hand, if you are using you own computer, just navigate to the two links above and take the correct installer for your platform.

![Download .NET Core](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/DownloadNETCore.png)

### Task 2 - Perform the migration - The csproj
As mentioned, .NET Core is in the Preview state. We also need a preliminary version of Visual Studio. Again, the VM is setup for you and Visual Studio 2019 Preview is alreday installed. If you need to install it on your own box, here is the link: [https://visualstudio.microsoft.com/vs/preview/](https://visualstudio.microsoft.com/vs/preview/).

Let's open the solution using Visual Studio 2019 Preview:
1.  In Windows Explorer, navigate to `C:\XAMLIslandsLab\Lab\Exercise3\01-Start\ContosoExpenses` and double click on the `ContosoExpenses.sln` solution.
    
    The project ContosoExpenses is now open in Visual Studio but nothing changed: The appllication still uses the Full .NET 4.7.2. To verify this, just right click on the project in the Solution Explorer Windows and **Properties**.
    
    ![Project properties in the Solution Explorer](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/PropertiesContosoExpenses.png)

    The *Target framework* of the project is displayed in the **Application** tab.
    
    ![.NET Framework version 4.7.2 for the project](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/NETFramework472.png)

2.  Right click on the project in the solution explorer and choose **Unload Project**.

    ![Unload project](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/UnloadProject.png)

3.  Right click again on the project in the solution explorer ; click **Edit ContosoExpenses.csproj**.

    ![Edit ContosoExpenses csproj](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/EditContosoExpensesCSPROJ.png)

4.  The content of the .csproj file looks like

    ![csproj file content](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/CSPROJFile.png)

    Do not be afraid, it is not the time to understand the whole csproj structure. You will see that the migration will be done easely: Juste remove all the content of the file by doing **CTRL+A** and than  **SUPPR**!
    
    ![Empty csproj file](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/EmptyCSPROJ.png)
    
5.  Start writing the new csproj file content by typing `<Project Sdk="Microsoft.NET.Sdk.WindowsDesktop"> </Project>` in the ContosoExpense.csproj. Microsoft.NET.Sdk.WindowsDesktop is the .NET Core 3 SDK for applications on Windows Desktop. It includes WPF and Windows Forms.

    ![Windows Desktop in csproj](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/WindowsDesktopInCSPROJ.png)

7.  Let's specify now a few details. To do this, insert a `<PropertyGroup></PropertyGroup>` element in inside the `<Project></Project>` element. 

    ![PropertyGroup inside Project in csproj](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/PropertyGroup.png)

8.  First, we indicate that the project output is a **executable** and not a dll. This is acheived by adding `<OutputType>WinExe</OutputType>` inside `<PropertyGroup></PropertyGroup>`.

> Note that, if the project output was a dll, this line has to be omitted.

9.  Secondly, we specify that the project is using .NET Core 3: Just below the <OutputType> line, add ` <TargetFramework>netcoreapp3.0</TargetFramework>`

10. Lastly, we point out that this is a WPF application in adding a third line: `<UseWPF>true</UseWPF>`.

> If the application is Windows Forms, we do not need this third line.

#### Summary, verification and last step

- The project using .NET Core 3 and the **Microsoft.NET.Sdk.WindowsDesktop** SDK
- Output is an **application** so we need the `<OutputType>` element
- `<UseWPF>` is self-describing

Here is the full content of the new csproj. Please double check that you have everything:

```xml
<Project Sdk="Microsoft.NET.Sdk.WindowsDesktop">

  <PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>netcoreapp3.0</TargetFramework>
    <UseWPF>true</UseWPF>
  </PropertyGroup>

</Project>
```

By default, with the new project format, all the files in the folder are considered part of the solution. As such, we don't have any more to specify each single file included in the project, like we had to do the old .csproj file. We need to specify only the ones for which we need to define a custom build action or that we want to exclude. 
It is now safe to save file by pressing **CTRL+S**.

### Task 3 - Perform the migration - Actions in the project

1.  The csproj is saved. Let's reopen the project: Go to the **Solution Explorer**, right click on the project and choose **Reload project**.

    ![Reload project in the Solution Explorer](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/ReloadProject.png)
    
2.  Visual Studio just asks for a confirmation ; click **yes**.

    ![Confirmation for closing the csproj](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/CloseCSPROJ.png)
    
3.  The project should load correctly. Let's try to build it in order to 'discover' the last steps to perform to complete the migration. Use the **Build** menu and **Build solution**.

    ![Builld solution in Visual Studio](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/BuildSolutionInVisualStudio.png)
    
4.  As expected, we have some errors. Open the **Output window** which is located in the bottom left of the Visual Studio window.

    ![Output windows of Visual Studio](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/OutputWindowVisualStudio.png)
    
5.  Let's fix the first series of error:
    
    ```bash
    1>obj\Debug\netcoreapp3.0\ContosoExpenses_eywqybwm_wpftmp.AssemblyInfo.cs(14,12,14,54): error CS0579: Duplicate 'System.Reflection.AssemblyCompanyAttribute' attribute
    1>obj\Debug\netcoreapp3.0\ContosoExpenses_eywqybwm_wpftmp.AssemblyInfo.cs(15,12,15,60): error CS0579: Duplicate 'System.Reflection.AssemblyConfigurationAttribute' attribute
    1>obj\Debug\netcoreapp3.0\ContosoExpenses_eywqybwm_wpftmp.AssemblyInfo.cs(16,12,16,58): error CS0579: Duplicate 'System.Reflection.AssemblyFileVersionAttribute' attribute
    1>obj\Debug\netcoreapp3.0\ContosoExpenses_eywqybwm_wpftmp.AssemblyInfo.cs(18,12,18,54): error CS0579: Duplicate 'System.Reflection.AssemblyProductAttribute' attribute
    1>obj\Debug\netcoreapp3.0\ContosoExpenses_eywqybwm_wpftmp.AssemblyInfo.cs(19,12,19,52): error CS0579: Duplicate 'System.Reflection.AssemblyTitleAttribute' attribute
    1>obj\Debug\netcoreapp3.0\ContosoExpenses_eywqybwm_wpftmp.AssemblyInfo.cs(20,12,20,54): error CS0579: Duplicate 'System.Reflection.AssemblyVersionAttribute' attribute
    ```
    
    It is not interesting to give explanations here: It is only 'piping' we have to resolve by either removing the mentioned lines in the `AssemblyInfo.cs` file or just delete the file. We go for the simpliest. 

6.  In the **Solution Explorer** window / Under the **ContosoExpenses** project, expand the **Properties** node and right click on the **AssemblyInfo.cs** file ; Click on **Delete**.
    
    ![AssemblyInfo cs file](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/AssemblyInfoFile.png)

7.  Just rebuild the project (for example using CTRL+SHIFT+B): Only the last two previous error should remain liste.

    ```bash
    1>Services\DatabaseService.cs(5,7,5,12): error CS0246: The type or namespace name 'Bogus' could not be found (are you missing a using directive or an assembly reference?)
    1>Services\DatabaseService.cs(6,7,6,13): error CS0246: The type or namespace name 'LiteDB' could not be found (are you missing a using directive or an assembly reference?)
    ```
    
> What could be the problem here? Remember that we removed (the hard way) all the content of the initial csproj file.

The NuGet packages used by the project were gone by removing all the content of the csproj! Remember that all these steps are perform for achieving the migration with the Preview version of .NET Core 3 and Visual Studio 2019. 

8.  You have a confirmation by expending the **Dependencies/NuGet** node in which you have only the .NET Code 3 package.

    ![NuGet packages](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/NuGetPackages.png)
    
    Also, if you click on the **Packages.config** in the **Solution Explorer**. You will find the 'old' references NuGet packages by the project when it was using the full .NET Framework.
    
    ![Dependencies and packages](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/Packages.png)
    
    Here is the content of the **Packages.config** file:
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <packages>
      <package id="Bogus" version="25.0.3" targetFramework="net472" />
      <package id="LiteDB" version="4.1.4" targetFramework="net472" />
    </packages>
    ```
    
    You notice there the two missing references mentionned in the errors messages: *Bogus* and *LiteDB*.
    
9.  Delete the file **Packages.config** by right clicking on it and **Delete** in the **Solution Explorer**. This file was used when the project was using the .NET Framework 4.7.2.

10. Right click on the **Dependencies** node in the **Solution Explorer** and **Manage NuGet Packages...**

  ![Manage NuGet Packages...](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/ManageNugetNETCORE3.png)

11. Click on **Browse** at the top left of the opened window and search for `Bogus`. The package by Brian Chavez should be listed. Install it.

    ![Bogus NuGet package](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/Bogus.png)

12. Do the same for `LiteDB`. This package is provided by Mauricio David.

    ![LiteDB NuGet package](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/LiteDB.png)

> Isn't it strange that we add the same packages as the ones used by the .NET Framework 4.7.2?

NuGet packages supports multi-targeting. You can include, in the same package, different versions of the library, compiled for different architectures. If you give a closer look at the packages' details, you will see that, other than supporting the full .NET Framework, it includes also a .NET Standard 2.0 version, which is perfect for .NET Core 3 (Further details on .NET Framework, .NET Core and .NET Standard at https://docs.microsoft.com/en-us/dotnet/standard/net-standard)

![Dot Net standard](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/DotNetStandard.png)

13. Rebuild the project (CTRL+SHIFT+B) and... you succeed!

```bash
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

> Since we don't have anymore a packages.config file, can you guess where the list of NuGet packages gets stored?

With the new project format, the referenced NuGet packages are stored directly in the .csproj file. You can check by right clicking on the **ContosoExpenses** project in Solution Explorer and choosing **Edit ContosoExpenses.csproj**. You will find some new lines compared to when we have manually edited the file:

```xml
<ItemGroup>
  <PackageReference Include="Bogus" Version="25.0.3" />
  <PackageReference Include="LiteDB" Version="4.1.4" />
</ItemGroup>
```


### Task 4 - Perform the migration - Debug

We are ok to finally, launch the app.

1.  Use the **Debug** menu / **Start Debugging F7**

> You had an exception. What is it that? Don't we finished the migration? Can you find the root cause of the issue reading the Exception Debug popup displayed by Visual Studio?

![Exception displayed in Visual Studio](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/ExceptionNETCore3.png)

Strange because the images files are in the solution and the path seems correct.

![Images in the Solution Explorer](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/ImagesInTheSolutionExplorer.png)

> Why do we get this file not found exception?

In fact, it is simple. Again, as we hardly deleted all the content of the csproj file at the beginning of the migration, we removed the information about the **Build action** for the images' files. Let fix it.

2.  In the **Solution Explorer**, select all the images files except the contoso.ico ; In the properties windows choose **Build action** = `Content` and **Copy to Output Directory** = `Copy if Newer`

    ![Build Action Content and Copy if newer](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/ContentCopyIfNewer.png)

3.  To assign the Contoso.ico to the app, we have to right click on the project in the **Solution Explorer** / **Properties**. In the opened page, click on the dropdown listbox for Icon and select `Images\contoso.ico`

    ![Contoso ico in the Project's Properties](https://github.com/Microsoft/Windows-AppConsult-XAMLIslandsLab/raw/master/Manual/Images/ContosoIco.png)

There is another issue which is due the fact that the NuGet Package `Microsoft.Toolkit.Wpf.UI.Controls` is in preview. It supports the .NET Full Framework and not yet the .NET Core Framework. That is why we need an extra modification: Use the Preview of the version supporting .NET Core. Let's use such a version:

1.  In the **Solution Explorer**, right click on the solution and choose **Add** / **New item...**.

2.  Create a new file named `nuget.config` and use the following content.

```json
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <!--To inherit the global NuGet package sources remove the <clear/> line below -->
    <clear />
    <add key="nuget" value="https://api.nuget.org/v3/index.json" />
    <add key="Custom" value="https://www.myget.org/CUSTOM/api/v3/index.json" />
  </packageSources>
</configuration>
```

The NuGet package `Microsoft.Toolkit.Wpf.UI.Controls` supporting .NET Core will come from this added Packages source.

We are done! Test the app in debug with F7 and it should work like a charm... Using .NET Core 3!

We are now ready to go further and use all the power of the full UWP ecosystem controls, packages, dlls.

