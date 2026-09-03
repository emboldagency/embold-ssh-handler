# **emBold SSH Handler for Windows**

**emBold SSH Handler** lets you open `ssh://` links in your favorite terminal (Windows Terminal, Command Prompt, Ubuntu, or custom) on Windows.

## **Features**

- **Multiple Terminal Support**: Works with Windows Terminal, Command Prompt, PowerShell, Ubuntu/WSL, Git Bash, and custom terminals
- **Self-Contained & Portable**: No installation required - run from anywhere
- **No .NET Required**: Everything is bundled in a single executable
- **Simple Setup**: Just two buttons - Apply and Clear

## **Download & Usage**

1. **Download** the latest `SSHHandlerApp.exe` from the [GitHub Releases page](https://github.com/emboldagency/embold-ssh/releases).  
2. **Run** `SSHHandlerApp.exe` from anywhere (no installation needed).  
3. **Configure** your preferred terminal and profile (if using Windows Terminal).
4. **Click "Apply"** to register the SSH protocol handler.  
5. You can now click `ssh://user@host` links, and they will open in your chosen terminal.

## **How It Works**

- **Portable**: The app can be run from any location - no installation required
- **Apply Button**: Registers the SSH protocol handler pointing to the current app location
- **Clear Button**: Removes the SSH protocol handler and optionally cleans up config files
- **Config Storage**: Settings are saved to `%LocalAppData%\embold-ssh\config.json`
- **Move Anywhere**: If you move the app, just click "Apply" again to update the registry

## **WSL Profiles**

If you pick Windows Terminal with a WSL profile (Ubuntu and friends), the handler runs
`wsl.exe -- ssh ...` so the connection uses WSL's `ssh` and WSL's `~/.ssh/known_hosts`.
Windows Terminal's `-p` flag only selects the tab's appearance and starting directory, so
without this the link would open a WSL-looking tab but run Windows OpenSSH against
`C:\Users\<you>\.ssh\known_hosts` - a common source of
`REMOTE HOST IDENTIFICATION HAS CHANGED` for hosts you already trust in WSL.

Note on keys: if you rely on a Windows-to-WSL ssh-agent relay (for example
`wsl2-ssh-agent`) that your shell rc file starts, the relay socket won't exist until you
have opened at least one WSL shell since boot. Open your WSL terminal once after logging
in, or start the relay from your distro's init, otherwise agent-based auth will fail on
the first clicked link.

## **Windows Defender Notice**

Because this app registers a custom protocol handler, Windows Defender or other antivirus software may show a warning upon first run.

* If blocked by SmartScreen, click "More info" \-\> "Run anyway".  
* If you trust this application, you can add its folder to your antivirus exclusion list to prevent future warnings.

## **Removal**

To remove the SSH protocol handler:

1. **Run** `SSHHandlerApp.exe`
2. **Click "Clear"**
   - This removes the SSH protocol handler from Windows Registry
   - Optionally removes configuration files from `%LocalAppData%\embold-ssh`
3. **Delete** `SSHHandlerApp.exe` when you no longer need it

## **Manual Cleanup (if needed)**

If you need to manually remove the protocol handler:

1. Remove registry entries by running these commands in PowerShell:
   ```powershell
   # Remove from user registry (HKCU)
   Remove-Item "HKCU:\Software\Classes\Embold.SSH" -Recurse -Force -ErrorAction SilentlyContinue
   
   # Remove from machine registry (HKLM) - may require admin privileges
   Remove-Item "HKLM:\Software\Classes\Embold.SSH" -Recurse -Force -ErrorAction SilentlyContinue
   ```
2. Optionally delete the config folder: `%LocalAppData%\embold-ssh`

## **Building and Publishing (for Developers)**

This project is set up with GitHub Actions to automatically build and create a new release when a new version tag (e.g., `v1.2.3`) is pushed to the repository.

For local development and testing, you can use the following `dotnet` CLI commands from the root of the project directory.

### **1\. Build the Application**

Builds the project and its dependencies.

```
dotnet build "SSH Handler.sln"
```

### **2\. Run for Testing**

Runs the application directly from the source for quick testing and debugging.

```
dotnet run --project SSHHandlerApp.csproj
```

### **3\. Publish for Release**

To create the final standalone executable for distribution, use the `publish` command with the correct single-file setting:

```
dotnet publish SSHHandlerApp.csproj -c Release -r win-x64 --self-contained=true /p:PublishSingleFile=true --output bin/Release/net8.0-windows/win-x64/publish
```

This command will output the final `SSHHandlerApp.exe` file (~153MB) into the `.\bin\Release\net8.0-windows\win-x64\publish\` directory. The larger size includes the .NET runtime and enables the app to run on any Windows machine without requiring .NET to be installed.

**Important**: Always test the executable from the `publish` folder, not from the `win-x64` folder directly.

### **4\. Create the Release**

After publishing, the final executable is ready for distribution:

1. Navigate to the output directory: `.\bin\Release\net8.0-windows\win-x64\publish\`  
2. The `SSHHandlerApp.exe` file (~153MB) is ready to distribute
3. Upload `SSHHandlerApp.exe` directly to a new GitHub release (no zip needed)

### **Testing**

Always test the published version from the `publish` folder. The app behavior:

1. **Portable**: Can be run from any location without installation
2. **Apply**: Registers SSH protocol handler pointing to current app location  
3. **Clear**: Removes protocol handler and optionally cleans config files
4. **Move & Re-Apply**: If you move the app, just click "Apply" again to update the registry