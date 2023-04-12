---
title: Windows Development Environment improvement tips From a Microsoft Employee
date: 2022-12-17 22:28:25
tags:
- Windows
categories:
- Tech Sharing
---

## Introduction

As a former Mac user, transitioning to Windows 11 was initially challenging for me. I found myself missing the ease of use and convenience of MacOS, and I struggled to adapt to the different development environment. However, with some effort and exploration, I discovered some tools and techniques that have helped me improve my experience as a developer in Windows. I know someone has to use Windows as his dev machine, like Microsoft employees. In this article, I want to share my experience and offer some tips and resources to help you enhance your Windows development environment, specifically with regards to shell improvements.

## Clink

Clink is a powerful tool that enhances the functionality of the Windows command prompt. It provides several features that can help you be more productive and efficient when working with the command line.

### How to install and set up Clink

### How to install and set up Clink

1. Download the Clink installer from the [official website](https://mridgers.github.io/clink/).
2. Run the installer and follow the on-screen instructions to install Clink on your system.
3. Once the installation is complete, open the Windows command prompt.
4. Type `clink install` and press Enter to enable Clink in the command prompt. This will add Clink to your PATH environment variable and configure some default settings.
5. Restart your command prompt to apply the changes.

You can customize Clink further by editing the configuration file `clink_inputrc.ini`, which is located in your user profile directory (e.g., `C:\Users\username`). The file contains various options that you can adjust to customize the behavior of Clink. For example, you can change the color scheme, add new keyboard shortcuts, and define aliases for commonly used commands.

Here's an example of how to modify the color scheme in `clink_inputrc.ini`:

1. Open `clink_inputrc.ini` in a text editor.
2. Locate the `[colors]` section.
3. Change the values of the color codes to your desired values. For example, to change the foreground color of directory listings to yellow, you can set `ls+di=33;1`.

You can find more information about customizing Clink in [the official documentation](https://mridgers.github.io/clink/).

### Examples of using Clink

Clink provides many useful features that can improve your command line experience on Windows. Here are some examples:

#### 1. Tab completion

Clink offers tab completion for commands, options, and file paths. To use tab completion, simply type the first few letters of a command or file path, then press the `Tab` key to complete it. If there are multiple options or file paths that match your input, you can press `Tab` repeatedly to cycle through them.

#### 2. Aliases

You can define aliases for commonly used commands using Clink's `alias` command. For example, if you frequently use `dir /w`, you can create an alias called `ls` that maps to this command. To create the alias, type the following command:

```
alias ls=dir /w
```


Now, whenever you type `ls` in the command prompt, it will be replaced with `dir /w`.

#### 3. History navigation

Clink allows you to navigate your command history using the up and down arrow keys. This is particularly useful if you need to repeat a command that you typed earlier.

#### 4. Customization

As mentioned earlier, you can customize Clink further by editing the configuration file `clink_inputrc.ini`. Here are some examples of customization options:

- Change the color scheme of the command prompt.
- Define keyboard shortcuts for frequently used commands.
- Set default options for certain commands.

You can find more information about these and other customization options in the official Clink documentation.

#### 4. Navigating directories

Clink provides several shortcuts for navigating directories. Here are some examples:

- `cd..`: This takes you up one directory level.
- `cd\`: This takes you to the root directory.
- `cd.`: This changes the current directory to the current directory (in other words, it does nothing).
- `cd~`: This takes you to your home directory.

#### 5. Copying and moving files

Clink provides aliases for commonly used file management commands. Here are some examples:

- `cp`: This is an alias for the `copy` command. For example, to copy a file called `example.txt` from the current directory to a directory called `backup`, you can use the following command: `cp example.txt backup`.
- `mv`: This is an alias for the `move` command. For example, to move a file called `example.txt` from the current directory to a directory called `archive`, you can use the following command: `mv example.txt archive`.

#### 6. Managing processes

Clink provides several commands for managing processes. Here are some examples:

- `tasklist`: This displays a list of all running processes, along with their process ID (PID) and other information.
- `taskkill`: This terminates a process based on its PID or image name. For example, to terminate a process with the image name `notepad.exe`, you can use the following command: `taskkill /im notepad.exe`.

By taking advantage of Clink's features and aliases, you can navigate directories, copy and move files, and manage processes more easily and quickly. 

Overall, Clink is a powerful tool that can greatly enhance your command line experience on Windows. By using its features and customizing it to your needs, you can work more efficiently and productively.


### Powershell
Powershell is a more advanced command-line interface for Windows that provides a wide range of features and capabilities. It can help you perform complex tasks more efficiently and effectively.

### How to install and set up Powershell

PowerShell is a powerful command-line interface for Windows that provides a more powerful and flexible environment than the traditional Command Prompt. Here's how to install and set up PowerShell:

1. Check your Windows version: PowerShell is included with Windows 7 SP1 and later versions. If you have an earlier version of Windows, you'll need to install PowerShell Core.

2. Install PowerShell: If you don't already have PowerShell installed, you can download it from the Microsoft website. Go to the [PowerShell downloads page](https://github.com/PowerShell/PowerShell/releases) and download the version that matches your operating system.

3. Open PowerShell: Once you have PowerShell installed, you can open it by typing "powershell" into the Start menu or by pressing Windows key + X and selecting "Windows PowerShell" from the menu.

4. Customize your PowerShell profile: You can customize your PowerShell profile to add aliases, functions, and other customizations. To do this, open PowerShell and type `notepad $PROFILE` to open your profile file in Notepad. You can then add your customizations to this file and save it.

5. Install modules: PowerShell has a large library of modules that can be installed to add additional functionality. To install a module, use the `Install-Module` command. For example, to install the Azure PowerShell module, you can use the following command: `Install-Module -Name Az -AllowClobber`.

By installing and customizing PowerShell, you can have a more powerful and flexible command-line environment on Windows.


### Examples of using Powershell

### Examples of how Powershell can help with system management tasks

PowerShell is a powerful tool for managing Windows systems. Here are a few examples of how PowerShell can help with system management tasks:

#### Managing Windows services

Powershell can be used to manage Windows services. For example, to start the Print Spooler service, you can use the following command:

```
Start-Service -Name Spooler
```

To stop the service, you can use the following command:

```
Stop-Service -Name Spooler
```


#### Configuring network settings

Powershell can also be used to configure network settings. For example, to set the IP address of a network adapter, you can use the following command:

```
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.1.100 -PrefixLength 24 -DefaultGateway 192.168.1.1
```


This command sets the IP address of the "Ethernet" adapter to 192.168.1.100 with a subnet mask of 255.255.255.0 and a default gateway of 192.168.1.1.

#### Performing system maintenance tasks

Powershell can also be used to perform various system maintenance tasks. For example, to check your system for disk errors, you can use the following command:

```
Get-Volume | Get-Partition | Get-Disk | Repair-Volume -SpotFix
```

This command checks all volumes, partitions, and disks for errors and attempts to fix them.

These are just a few examples of how PowerShell can be used to manage and maintain Windows systems. With PowerShell, the possibilities are endless!

## Winget

[Winget](https://learn.microsoft.com/en-us/windows/package-manager/winget/) is a package manager for Windows that allows you to easily install, manage, and update applications from the command line. It is similar to package managers like Homebrew on MacOS or apt on Ubuntu, and provides a simple and convenient way to manage software on your Windows system. With Winget, you can quickly install and update applications without having to search for and download installation files manually, making it a valuable tool for developers and power users on Windows.

### Examples of using Winget

Winget makes it easy to install applications from the command line. Here are a few examples:

#### Install an application
To install an application, simply enter the following command:

```
winget install <application>
```

For example, to install Visual Studio Code, you would enter:

```
winget install Microsoft.VisualStudioCode
```

#### List installed applications
To list the applications that are currently installed on your system, you can use the following command:

```
winget list
```

#### Search for an application
To search for an application, you can use the following command:

```
winget search <application>
```
For example, to search for Firefox, you would enter:

```
winget search Firefox
```

#### Uninstall an application
To uninstall an application that was installed using Winget, you can use the following command:

```
winget uninstall <application>
```
For example, to uninstall Visual Studio Code, you would enter:

```
winget uninstall Microsoft.VisualStudioCode
```

These are just a few examples of how you can use Winget to install and manage applications on your Windows system. With Winget, you can easily install and update applications from the command line, making it a valuable tool for developers and power users alike.

### OhMyPosh

OhMyPosh is a tool that lets you customize your Powershell prompt. It allows you to create custom themes that can make your command-line interface more visually appealing and easier to use.

#### Installing OhMyPosh
Open the Windows Terminal as an administrator.

Install OhMyPosh by running the following command:

```
Install-Module oh-my-posh -Scope CurrentUser
```

This command installs OhMyPosh as a PowerShell module in your current user scope.

Install the required font by running the following command:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iwr -useb https://github.com/ryanoasis/nerd-fonts/releases/download/v2.1.0/CascadiaCode.zip -OutFile CascadiaCode.zip; Expand-Archive CascadiaCode.zip -DestinationPath $env:SystemRoot\Fonts
```

This command installs the Cascadia Code Nerd Font, which is required for OhMyPosh to display custom prompt themes.

Once the font is installed, you can set up a custom prompt theme by running the following command:

```
Set-PoshPrompt -Theme paradox
```

This command sets the prompt theme to "paradox," which is a popular OhMyPosh theme. You can choose a different theme by replacing "paradox" with the name of your desired theme.

Finally, to make your custom prompt theme persist across PowerShell sessions, add the following line to your PowerShell profile:

```
Import-Module oh-my-posh; Set-PoshPrompt -Theme paradox
```

This command imports the OhMyPosh module and sets the prompt theme to "paradox" every time you start a new PowerShell session.

### Using OhMyPosh

Once OhMyPosh is installed and set up, you can start using it to customize your PowerShell prompt. Here are a few examples:

#### List available themes
To see a list of available prompt themes, run the following command:

```
Get-PoshThemes
```

#### Customize your prompt
To customize your prompt with OhMyPosh, you can edit the theme file for your chosen prompt. For example, if you're using the "paradox" theme, you can edit the "paradox.omp.json" file to change the colors and formatting of your prompt.

#### Create your own theme
If you want to create your own custom prompt theme, you can use the OhMyPosh theme editor. To open the editor, run the following command:

```
Invoke-PoshEditor
```

This command opens a web-based editor where you can create and preview your own custom prompt theme.

With OhMyPosh, you can create custom and visually appealing prompt themes for your PowerShell sessions, making your development experience on Windows more enjoyable and efficient.

## Autojump
[Autojump](https://github.com/wting/autojump) is a tool that helps you navigate your file system more efficiently. It remembers the directories you visit and lets you quickly jump back to them using a short command.

### Installing Autojump
Open a PowerShell terminal as an administrator.

Install Autojump by running the following command:

```
winget install autojump
```

You can find your PowerShell profile by running the following command:

```
$PROFILE
```

Restart your PowerShell terminal to apply the changes.

### Using Autojump
Autojump is a command-line tool that allows you to navigate your file system more efficiently and quickly by remembering the directories you've visited and providing shortcuts to them.

#### Jump to a directory
To jump to a directory that you've visited before, simply type j followed by a unique part of the directory name. For example:

```
j documents
```

This will jump to the directory that contains the word "documents" in its name.

#### List directories
To see a list of the directories that Autojump remembers, type:

```
j --stat
```

This will display a list of the directories along with the number of times you've visited each one.

With Autojump, you can navigate your file system more efficiently and quickly, making your development experience on Windows more enjoyable and efficient.

## Z.lua, a faster equivalent of autojump

[z.lua](https://github.com/skywind3000/z.lua) is a faster equivalent of autojump. It is written in pure Lua and has no external dependencies. It is also faster than autojump. I use it more on Windows and PowerShell.

## Conclusion

In conclusion, Windows can be a great platform for development if you have the right tools and know how to use them. In this article, we have covered several tools that can help improve your Windows development experience, including Clink, PowerShell, OhMyPosh, Winget, and Autojump.

Clink and PowerShell provide a powerful shell experience that is comparable to what you get on other platforms. OhMyPosh allows you to customize your PowerShell prompt and make it more visually appealing. Winget is a package manager that makes it easy to install and manage software on Windows. Autojump provides a quick and efficient way to navigate your file system.

By using these tools and tips, you can enhance your development experience on Windows and work more efficiently. With the right setup, Windows can be just as powerful and enjoyable as other platforms for development.

## References

- https://mridgers.github.io/clink/
- https://github.com/wting/autojump
- https://ohmyposh.dev/docs/installation/prompt
- https://learn.microsoft.com/en-us/windows/package-manager/winget/