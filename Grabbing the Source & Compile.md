# Getting the Source

You'll want to open up a terminal shell / powershell

Firstly, you'll want to setup a good workflow / directory structure.

I chose the following structure.
> D:\secondlife\viewers\tpv\alchemy-viewer
   this is the root
   
> D:\secondlife\viewers\tpv\alchemy-viewer\3p
   this houses all the 3rd party libraries (FMOD, NVAPI)
   
> D:\secondlife\viewers\tpv\alchem-viewer\tools
   this is for virtualenv

## Add Environment Variables

Make sure the following are currently paths are included

```powershell
C:\Users\builddev\> D:
D:\> echo $env:PATH
 ```

- C:\Program Files\CMake\bin
- C:\Python39\Scripts
- C:\Python39
- C:\Program Files\TortoiseHg
-  C:\Program Files\Git\cmd
-  C:\Cygwin64\bin

Note how  Cygwin is last. 
   
 ## Setup Environment
 Open a terminal and create your environment mentioned above.
 
 ```powershell
 D:\> mkdir -p secondlife\viewers\tpv\alchemy-viewer\3p
 D:\> mkdir -p secondlife\viewers\tpv\alchemy-viewer\tools
 ```
 
 
 **FIX YOUR LINE ENCODINGS FOR GIT**
 
When you use git for the first time it'll ask you to autoconvert line endings. Leave them as they are. If you didn't it's ok we can fix it.

```powershell
D:\> git config --global core.autocrlf false
```

Now we're ready to setup build environment
 
 ### VirtualEnv
 
 You don't want to much up your other environments.
 ```powershell
  D:\> cd secondlife\viewers\tpv\alchemy-viewer\tools
  D:\secondlife\viewers\tpv\alchemy-viewer\tools\> pip3 install virtualenv
  D:\secondlife\viewers\tpv\alchemy-viewer\tools\>virtualenv -p C:\python39\python.exe .venv
  D:\secondlife\viewers\tpv\alchemy-viewer\tools\>.\.venv\Scripts\activate
  ```
  
  ### Alchemy Autobuild
You'll need RY3's special autobuild
  ```powershell
  D:\secondlife\viewers\tpv\alchemy-viewer\tools\>pip3 install --upgrade autobuild -i https://git.alchemyviewer.org/api/v4/projects/54/packages/pypi/simple 
 ```
 
 ---
 
 ## Building 3RD Party Libraries (optional)
 
 ### NVAPI
 I had to use CYGWIN for this.
 Open Cygwin and navigate to your environment.
 
 ```bash
user@domain: ~$ cd /cygwindrive/d/secondlife/viewers/tpv/alchmey-viewer/3p
user@domain:/cygwindrive/d/secondlife/viewers/tpv/alchmey-viewer/3p $ source ../tools/.venv/Scripts/activate
user@domain:/cygwindrive/d/secondlife/viewers/tpv/alchmey-viewer/3p $ git clone https://git.alchemyviewer.org/alchemy/thirdparty/3p-nvapi
 ```
 
 Once it's clone you want to put the `R470-developer.zip` you downloaded previously to the `nvapi` folder within the cloned directory.
 
 ### Build NVAPI
 ```bash
user@domain:/cygwindrive/d/secondlife/viewers/tpv/alchmey-viewer/3p 
$ cd 3p-nvapi

user@domain:/cygwindrive/d/secondlife/viewers/tpv/alchmey-viewer/3p/3p-nvapi 
$ autobuild build -A64 --all

user@domain:/cygwindrive/d/secondlife/viewers/tpv/alchmey-viewer/3p/3p-nvapi 
$ autobuild package -A64 --all
 ```
 
 Once it builds it'll give you a hash and a location, make note of these we need them later
 
  ### FMod
 
 ```bash
 user@domain:/cygwindrive/d/secondlife/viewers/tpv/alchmey-viewer/3p/3p-nvapi 
 $ cd ..
 
user@domain:/cygwindrive/d/secondlife/viewers/tpv/alchmey-viewer/3p 
$ git clone https://git.alchemyviewer.org/alchemy/thirdparty/3p-fmodstudio
 ```
 
 Once it's clone you want to put the `R470-developer.zip` you downloaded previously to the `nvapi` folder within the cloned directory.
 
 ### Build FMod (audio)
 ```bash
user@domain:/cygwindrive/d/secondlife/viewers/tpv/alchmey-viewer/3p 
$ cd 3p-fmodstudio

user@domain:/cygwindrive/d/secondlife/viewers/tpv/alchmey-viewer/3p/3p-fmodstudio 
$ autobuild build -A64 --all

user@domain:/cygwindrive/d/secondlife/viewers/tpv/alchmey-viewer/3p-fmodstudio 
$ autobuild package -A64 --all
 ```
 
 Once it builds it'll give you a hash and a path location, make note of these we need them later
 
 
 ## Include your 3rd party libs to autobuild
 
 Let's add our 3rd party libraries.
 
 Open `autobuild.xml` locate `<key>fmodstudio</key>` and look for the following chunk
 
 ```xml
<key>windows64</key>
               <map>
                 <key>archive</key>
                 <map>
                   <key>hash</key>
                    <string>a73e689113e2......</string>
                    <key>hash_algorithm</key>
                    <string>blake2b</string>
                    <key>url</key>
                    <string>https://git.alchemyviewer.org/api/v4/projects/49/packages/generic/fmodstudio/2.02.03.1163/fmodstudio-2.02.03-windows64-1163.tar.zst</string>
                  </map>
                  <key>name</key>
                  <string>windows64</string>
                </map>
```
	 
Replace the hash with the appropriate hash that you wrote down and the url with `file:///PATHHERE`

Repeat the process for NVAPI

---

## Get the viewer source
This guide is for Alchemy Next
```powershell
C:\Users\builddev\> D:
D:\> cd \secondlife\viewers\tpv\alchemy-viewer
D:\secondlife\viewers\tpv\alchemy-viewer> git clone https://git.alchemyviewer.org/alchemy/alchemy-next.git
D:\secondlife\viewers\tpv\alchemy-viewer> cd alchemy-next
```

## Configure the Viewer

Now, we're ready to configure the viewer.

Open Windows Terminal (or PowerShell 5.1)

Navigate to your build environment, (tip: in Windows Explorer there's a `Copy path` option)

```powershell
D:\secondlife\viewers\tpv\alchemy-viewer\alchemy-next> autobuild configure -c ReleaseOS -A 64 -- -DUSE_FMODSTUDIO=ON -DUSE_NVAPI=ON -DUSE_LTO=ON -DDISABLE_FATAL_WARNINGS=ON -DREVISION_FROM_VCS=FALSE -DLL_TESTS=OFF
D:\secondlife\viewers\tpv\alchemy-viewer\alchemy-next> autobuild build -c ReleaseOS -A 64 --no-configure
```

If no errors popped up you've done it!
