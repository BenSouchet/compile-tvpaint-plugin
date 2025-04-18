# How to Compile a TV Paint Plugin on Windows

## Step 1: Install Homebrew

Not mandatory but highly recommended to easily install cmake, go to https://brew.sh/ for the command to install it.

To ensure homebrew is added to your terminal profile run:

```shell
echo >> /Users/YOUR_USERNAME/.zprofile
```

```shell
'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/YOUR_USERNAME/.zprofile
```

And close and open a new terminal (on some IDEs like Pycharm or VSCode you will need to restart the whole IDE).

## Step 2: Install openssl and other packages

Use Homebrew to install the packages you need if not already installed

## Step 3: Install CMake

1. Download the `.dmg` installer from the official website https://cmake.org/download/
2. Execute the downloaded file and copy the app to your Applications folder
3. To install the mandatory files execute the following command in a terminal:
```shell
sudo "/Applications/CMake.app/Contents/bin/cmake-gui" --install
```

## Step 4: (Optional) Install Boost C++

If your TVPaint plugin need boost you will need to compile it from source to get all the mandatory files.
To do that here are the steps:

1. Download the unix source from the official website https://www.boost.org/users/download/
2. Unarchive the downloaded file to a easily accessible folder
3. With a terminal go to this folder
4. Run the command: `./bootstrap.sh --prefix=/Users/YOUR_USERNAME/boost` (The prefix is where you want the build to be generated, the folder should exists and be empty)
5. Then run: `./b2`
6. And finally: `./b2 install --prefix=/Users/YOUR_USERNAME/boost` (the prefix here should be useless but we are never too careful)
7. DONE

## Step 6: Creating the `Info.plist` file

For your `.bundle` to work you will need an `Info.plist` file, the easiest way is to copy a working one and change the elements you want.
You can use the [example plist file](https://github.com/BenSouchet/compile-tvpaint-plugin/blob/main/Info.plist) I added to this repo.
The main element you will need to update is the plugin name set for these keys : `CFBundleExecutable`, `CFBundleIdentifier` and `DLLName`

## Step 5: Setting up you CMakeLists.txt

For a TVPaint plugin you need to generate a `.bundle` package, to do that your `CMakeLists.txt` need to be properly configured.

**I highly recommend you check the [CMakeLists.txt](https://github.com/BenSouchet/compile-tvpaint-plugin/blob/main/CMakeLists.txt) example I added in the repo**

Instead of this line for windows (that specify the type of the build and the source)
```
add_library(${PROJECT_NAME} SHARED library.cpp library.def "${TVPAINT_SDK_LIB}/dllx.c")
```
You need this:
```
add_library(${PROJECT_NAME} MODULE library.cpp library.def "${TVPAINT_SDK_LIB}/dllx.c")
```

The difference in that `SHARED` is replaced by `MODULE`.

Additionally you need to add target properties, something like:
```
set_target_properties(${PROJECT_NAME} PROPERTIES
    BUNDLE TRUE
    MACOSX_BUNDLE TRUE
    MACOSX_BUNDLE_GUI_IDENTIFIER fr.tvpaint.tvpa.MytopPlugin
    MACOSX_BUNDLE_BUNDLE_NAME ${PROJECT_NAME}
    MACOSX_BUNDLE_BUNDLE_VERSION "1.0"
    MACOSX_BUNDLE_SHORT_VERSION_STRING "1.0"
    OUTPUT_NAME ${PROJECT_NAME}
    MACOSX_BUNDLE_INFO_PLIST "./Info.plist"
)
```

Add of course add the necessary lines for your dependencies.

## Step 6: Compiling

Now it's time to build!

1. First ensure CMake is first in your PATH: `export PATH="/Applications/CMake.app/Contents/bin":"$PATH"`
2. Go within a terminal in the folder where you `CMakeLists.txt` is located
3. Do `cmake .`, this will generate the makefile and all the necessary internal files
4. Now if the previous command run successfully, you can do:
```
make MytopPlugin
```

/!\: `MytopPlugin` is the num of you plugin you specified on the `Info.plist` and the `CMakeLists.txt`

You now should have a `.bundle` TVPaint plugin
