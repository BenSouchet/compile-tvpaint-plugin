# How to Compile a TV Paint Plugin on Windows

## Step 1: Install the Microsoft Visual C++ (MSVC) compiler toolset.

If you have a recent version of Visual Studio, open the Visual Studio Installer from the Windows Start menu and verify that the C++ workload is checked. If it's not installed, then check the box and select the Modify button in the installer.

You can also install the Desktop development with C++ workload without a full Visual Studio IDE installation. From the Visual Studio Downloads page, scroll down until you see Tools for Visual Studio under the All Downloads section and select the download for Build Tools for Visual Studio 2022.

![Build Tools for Visual Studio download](https://github.com/user-attachments/assets/817b7f57-5263-4bbf-a8f6-4cf35567c192)


This will launch the Visual Studio Installer, which will bring up a dialog showing the available Visual Studio Build Tools workloads. Check the Desktop development with C++ workload and select Install.

![Cpp build tools workload](https://github.com/user-attachments/assets/2febd320-0a51-4241-b5d4-63ba771ad6cc)

> Note: You can use the C++ toolset from Visual Studio Build Tools along with Visual Studio Code to compile, build, and verify any C++ codebase as long as you also have a valid Visual Studio license (either Community, Pro, or Enterprise) that you are actively using to develop that C++ codebase.

### Check your Microsoft Visual C++ installation
To use MSVC from a command line or VS Code, you must run from a Developer Command Prompt for Visual Studio. An ordinary shell such as PowerShell, Bash, or the Windows command prompt does not have the necessary path environment variables set.

To open the Developer Command Prompt for VS, start typing 'developer' in the Windows Start menu, and you should see it appear in the list of suggestions. The exact name depends on which version of Visual Studio or the Visual Studio Build Tools you have installed. Select the item to open the prompt.

![Developer Command Prompt](https://github.com/user-attachments/assets/17d9ee37-c4d9-40c4-8808-bbd690d13d69)

You can test that you have the C++ compiler, cl.exe, installed correctly by typing 'cl' and you should see a copyright message with the version and basic usage description.

![Checking cl.exe installation](https://github.com/user-attachments/assets/a02edfbe-89bf-4e6b-a23e-8031016be0a5)

## Step 2: (Optional) Install Boost C++

If you need boost in your plugin (like us with [QuadPype](https://github.com/quadproduction/quadpype))

1. Go to the [Boost Downloads page](https://www.boost.org/users/download/) and download the latest Boost source `.zip` file or `.tar.gz` file.
2. Extract the archive to a directory, e.g., `C:\Boost`.
3. Open a **Developer PowerShell for VS** console and navigate to the Boost directory, e.g.:
```cmd
cd C:\Boost
```
4. Run the `bootstrap.bat` script to set up the build system:
```cmd
.\bootstrap.bat
```
5. Build Boost using the b2 tool. Specify the desired toolchain (e.g., msvc) and install path:
```cmd
.\b2 install --toolset=msvc --address-model=64 --prefix=C:\Boost\Boost_Install
```

`--address-model=64`: Specifies a 64-bit build.  
`--prefix=C:\Boost\Boost_Install`: Directory where Boost will be installed.

###  Configure Environment Variable

Open your user environment variables window

![Screenshot 2024-12-11 181900](https://github.com/user-attachments/assets/3a2359fe-c7bc-433c-9d8d-9aa994806199)


And add a new environment variable:

`BOOST_ROOT` : `C:\Boost\Boost_Install`

![Screenshot 2024-12-11 182148](https://github.com/user-attachments/assets/a8d793a6-ff75-4e2b-b430-9cce9684f177)

## Step 3: (Optional) OpenSSL
If you need openSSL for your compilation you should use the version from [Shining Light Productions](https://slproweb.com/products/Win32OpenSSL.htm).
Yes the website seems to come from the 90s and have stay in it, it's true, BUT it's the easiest way without building manually OpenSSL to get the correct LIB and HEADERS files.

1. Go to [this page](https://slproweb.com/products/Win32OpenSSL.html) and downlaod the correct MSI installed for your platform (x64 or x86).
2. Install it but check the option to install the lib data in the OpenSSL install folder not copy them to the Windows directory.
3. All good you have the files you need.

## Step 4: Your Others Dependencies

Based on your code and the `CMakeLists.txt` you have (should have) you will for sure need other packages.

## Step 5: The official TV Paint SDK

Yeah no easy to find (or kind of impossible), but I'm kind and I can provide it (send me an email).

When you have it you need to add it to you CMakeLists by adding the `TVPAINT_SDK_INCLUDE` path to the `include_directories`.

And by adding the `"${TVPAINT_SDK_LIB}/dllx.c"` as the last element in the `add_library` (used to build your dll) in your CMakeLists.

## Step 6: The Building Phase

**I highly recommend you check the exemple [CMakeLists.txt](https://github.com/BenSouchet/compile-tvpaint-plugin/blob/main/CMakeLists.txt) to ensure yours is properly configured.**

**CAUTION: If you want to properly use your plugin for TVPaint 12 or newer you will need to replace all DWORDs in your plugin code with INTPTRs (search and replace)**

Here we go!

1. First, update your `CMakeLists.txt` to point to the correct folders you retrieved / downloaded/ installed.
2. Now a **Developer Command Prompt for VS** in admin (right click > Run as administrator), navigate to the location where you `CMakeLists.txt` is, and then run:

![Screenshot 2024-12-11 192805](https://github.com/user-attachments/assets/e061ef36-4717-4526-9c99-1d58842e71bb)

```cmd
cmake .
```

For `x86` (x32) build the command need to be:
```
cmake -G "Visual Studio 17 2022" -A Win32 .
```

3. Check the logs, fix the issues (you will probably have some the first time, or you are just too good and you can take an ice cream, you deserve it!)
4. Last but not least, the true generation of your TV Paint dll plugin, run the command:
```cmd
msbuild NAME_OF_YOU_PROJECT.sln
```

INFO: This `.sln` file as heen generated by the previous `cmake .` command, the `NAME_OF_YOU_PROJECT` is your project name defined in your `CMakeLists.txt` with the line:
```
project(NAME_OF_YOU_PROJECT C CXX)
```

If everything works, wow, that was a good day! If not, hum sorry you will need more debug, but I'm sure you will succeed.
