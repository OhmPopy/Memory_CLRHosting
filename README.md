# Memory CLR Hosting With 4.+ .Net Support
Hey mates , a perfect source code to protecting your .net apps by native unmanaged code .
The source uploaded here belongs to [this article](https://www.codeproject.com/Articles/1236146/Protecting-Net-plus-Application-By-Cplusplus-Unman) I made.

## Since The War Began

Well , let's begin. Everybody knows how it's much easier to create an app with c# and .net than develop a native language like c++ or delphi.

But , the main problem of every CLR application is ... **Security!**

I'm telling you after **flash swf files** , it's the most insecure platform you can develop your app!!!

## Umm ... Security!

Let's be honest **, Security is a joke!** But ... it has levels.

Imagine you have a gold watch and you love it too much! You don't want to share it with anybody else...

-   A > You can put it in a glass box and take care of it.
-   B > You can put it in a glass and put a lot of laser around it.
-   C > You can put it in a metal box and lock it with a unique key that only you have it.
-   D > you can put it in a glass box and next , put the glass box in a metal box and lock it.

The level of your secuirty methods are different , But eventually it can be defeated.

You just determined the level of hardness... then... **We do the same thing!**

## Before Start , Know The End!

Let's face it! Hundreds of companies claim that they can secure your software! But ...

> As You Know :
> 
> **_People Lie! , Just to Take Your Money! It never was Wrong , Right ?!_**

Obfuscator , Crypters , Anti-Debuggers , Anti-Dumpers ...

All of them can be decompiled **Very Easily!!!** There are tools that reverse your app by **One Click** in **Couple Of Seconds**... _**And you do not even need to be an engineer!!!**_

Some of these apps you can find:

de4Dot , .Net Generic Unpacker , .NetBreak , DefuscatorNet , DeSpy , .Net Reflector , ILDasm ...

> Quote:
> 
> **If you know the proper recognition of your enemy weapons , the probability of your winning is greater.** _- I don't know who said that but it's logical :D_

## The Methods

At the moment , We have several methods of injecting a .net application into a unmanaged c++ app...

-   **Using BoxedAppSDK's DotNetAppLauncher** - You need 8000 USD and It only supports 2.0 framework! Developers had a lot of emails from users wanted 4.+ frameworks but their method require to embed more than 400MB in C++ Exe file!!!! lol , it's super f---- up!
-   **Embedding your dotnet app , extract it on a temp folder , launch it !** - Hacker just needs task manager! xD
-   **Using Virtual Applications , Read the .net from memory.** - only supports 2.0 framework too.
-   **Using C++/CLI Framework .** - Sorry , the released file is a .net file !
-   **Spend a lot of time to develop a Armageddon Packer to end the WAR!** - DO IT SOLDIER !!!
-   **Using this [Article](https://www.codeproject.com/Articles/674455/Anti-Reflector-NET-Code-Protection) , supports WPF and otherstuffs.** it's hard to use and it has some bugs!
-   **Host the .Net Application by CLRHosting in C++ from Crypted Data from Memory!** - Here we go ... It's easy , fast to compile and more safe!

_**You should know ...**_

1.  _And , you can use any PEPacker , PEProtector and PECrypter on your final exe file. It's .NET ! But You Can!_
2.  It Supports WPF too!
3.  It's 100% Virus Free ! MAGIC EXISTS !!! [<VirusCheck>](https://www.virustotal.com/#/file/7c7bd08d7f27424b85ca62b496aa9ac8c79fe1252569febc0df6008bea924cad/detection)

## Preparing The Project

-   What you need :

- **Visual Studio 2012** or Higher ( C++ native / .Net C# 4.0 - 4.7 ) .

- **Custom Hash to String Convertor** " Included in source files "

- **Fody/Costura** .Net Extension to embed all dll references as resources in final .net exe file , Get it from nuget.

- **HxD Hash Editor** , it's free download it.

- **VMProtect 3** , UPXGUI , MEW , or any packer you want.

- **CFF Explorer , Detect It Easy and Exeinfo** , download them all.

## **Step A )** Develop A .Net Application

-   Create your .Net app ( UWP , WPF , WinForm ) Everything you need , there is no limit !

I Used 4.5 framework , `64bit Mode` and my custom WPF library (image.a) :

![](https://www.codeproject.com/KB/security/1236146/image1.jpg)  

-   Download and install **Fody/Costura** from Nuget , Build project again and you'll see you have only

one exe file.

-   Build on **Release**.

## **Step B )** Preparing Native Launcher App

-   Follow :  **New > Project > Visual C++ > Win32 > Win32 Console Applicaton** and create a new project.
-   Add Includes to your main cpp file . ( Create **RawFiles.h** in headers and leave it empty )

    #include <cstdlib> #include <iostream> #include <sstream> #include <cassert> #include <fstream> #include "RawFiles.h"
        #pragma region Includes and Imports
                #include <windows.h>
                #include <metahost.h>
                #include <vector>
                #pragma comment(lib, "mscoree.lib")
                #import "mscorlib.tlb" raw_interfaces_only\
                high_property_prefixes("_get","_put","_putref")\
                rename("ReportEvent", "InteropServices_ReportEvent")
                using namespace mscorlib;
        #pragma endregion

-   Define just one value : `#define RAW_ASSEMBLY_LENGTH 1000`
-   Replace `"int _tmain(int argc, _TCHAR* argv[])"` with :

int APIENTRY _tWinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPTSTR lpCmdLine, int nCmdShow)

-   Goto Linker Properties and change SubSystem to **Windows (/SUBSYSTEM:WINDOWS)** and Target Machine to **MachineX64 (/MACHINE:X64)**.

## **Step C )** Start to Code The Host!

-   Install WPF Mode By Adding : `CoInitializeEx(NULL,COINIT_APARTMENTTHREADED);`

> Tip :
> 
> **You only need to add** `CoInitializeEx` **when you :**
> 
> 1.  Build a WPF Based Application.
> 2.  Used WPF UserControl in your WinForms.
> 
> If you didn't , you don't need to add it.

-   Install CLRHost , MetaHost and Instances :

    ICLRMetaHost       *pMetaHost       = NULL;  /// Metahost installed.
    ICLRMetaHostPolicy *pMetaHostPolicy = NULL;  /// Metahost Policy installed.
    ICLRDebugging      *pCLRDebugging   = NULL;  /// Metahost Debugging installed.
    HRESULT hr;  
    hr = CLRCreateInstance(CLSID_CLRMetaHost, IID_ICLRMetaHost,  
                        (LPVOID*)&pMetaHost);  
    hr = CLRCreateInstance (CLSID_CLRMetaHostPolicy, IID_ICLRMetaHostPolicy,  
                        (LPVOID*)&pMetaHostPolicy);  
    hr = CLRCreateInstance (CLSID_CLRDebugging, IID_ICLRDebugging,  
                        (LPVOID*)&pCLRDebugging); 

-   Install .Net Runtime :

Tip : You can set your runtime framework version here `GetRuntime(L"RUNTIMEVERSION")`.

For this project I used "v4.0.30319" Version.

    ICLRRuntimeInfo* pRuntimeInfo = NULL; /// Runtime Info Installed.
        hr = pMetaHost->GetRuntime(L"v4.0.30319", IID_ICLRRuntimeInfo, (VOID**)&pRuntimeInfo);

-   Start and Load CLRApp :

    BOOL bLoadable;
    
    hr = pRuntimeInfo->IsLoadable(&bLoadable);
    
    ICorRuntimeHost* pRuntimeHost = NULL;
    
    hr = pRuntimeInfo->GetInterface(CLSID_CorRuntimeHost,
    IID_ICorRuntimeHost,
    (VOID**)&pRuntimeHost);
    
    hr = pRuntimeHost->Start();
    
    -   Install AppDomain :
    
        IUnknownPtr pAppDomainThunk = NULL;
        hr = pRuntimeHost->GetDefaultDomain(&pAppDomainThunk);
        _AppDomainPtr pDefaultAppDomain = NULL;
        hr = pAppDomainThunk->QueryInterface(__uuidof(_AppDomain), (VOID**) &pDefaultAppDomain);
        _AssemblyPtr pAssembly = NULL;
        SAFEARRAYBOUND rgsabound[1];
        rgsabound[0].cElements = RAW_ASSEMBLY_LENGTH;
        rgsabound[0].lLbound   = 0;
        SAFEARRAY* pSafeArray  = SafeArrayCreate(VT_UI1, 1, rgsabound);
        void* pvData = NULL;
        hr = SafeArrayAccessData(pSafeArray, &pvData);

-   Add , execution , clean up , and memory reader :
    

    memcpy(pvData, Raw_Net_Data, RAW_ASSEMBLY_LENGTH);
    
    hr = SafeArrayUnaccessData(pSafeArray);
    
    hr = pDefaultAppDomain->Load_3(pSafeArray, &pAssembly);
    
    _MethodInfoPtr pMethodInfo = NULL;
    
    hr = pAssembly->get_EntryPoint(&pMethodInfo);
    
    VARIANT retVal;
    ZeroMemory(&retVal, sizeof(VARIANT));
    VARIANT obj;
    ZeroMemory(&obj, sizeof(VARIANT));
    obj.vt = VT_NULL;
    SAFEARRAY *psaStaticMethodArgs = SafeArrayCreateVector(VT_VARIANT, 0, 0);
    hr = pMethodInfo->Invoke_3(obj, psaStaticMethodArgs, &retVal);

> If you have any problem at compiling , just restart visual studio.

## **Step D )** Using The Code!

-   Ok , now we have a host that execute our dotnet app from memory , now it's the time to inject our application.
-   Start **HxD** and drag and drop your .net release application.
-   Select all of hashes and click on : **Edit > Copy As > C** .
-   Paste clipboard on `RawFiles.h` file.
-   Change the unsigned char name to `Raw_Net_Data` :

    unsigned char Raw_Net_Data[120399] = {
        0x4D, 0x5A, 0x90, 0x00, 0x03, 0x00, 0x00, 0x00, 0x04, 0x00, 0x00, 0x00,
        ...
    };

-   Set `RAW_ASSEMBLY_LENGTH` to Raw_Net_Data[**120399**] number :

    #define RAW_ASSEMBLY_LENGTH **120399**

Now build the application.

**As you can see your app runs perfectly with any problem !**

## War Begins !

-   the Hex array you entered in visual studio compiles too fast but the problem is , it compiles to a embeded resource , you can rip it so easily with **Exeinfo Ripper.**
-   Let's start our next solution.
-   We're going to create the hex from strings arrays and load it on runtime. Ripper can't recognize the pe hexes located in your resources.
-   All code we wrote till now ...

    #include <cstdlib> #include <iostream> #include <sstream> #include "stdafx.h" #include <cassert> #include <fstream> #include "RawFiles.h"
        #pragma region Includes and Imports
                #include <windows.h>
                #include <metahost.h>
                #include <vector>
                #pragma comment(lib, "mscoree.lib")
                #import "mscorlib.tlb" raw_interfaces_only\
                high_property_prefixes("_get","_put","_putref")\
                rename("ReportEvent", "InteropServices_ReportEvent")
                using namespace mscorlib;
        #pragma endregion
        #define RAW_ASSEMBLY_LENGTH 1715200
    
    int _tmain(int argc, _TCHAR* argv[])
    {
                CoInitializeEx(NULL,COINIT_APARTMENTTHREADED);
                ICLRMetaHost       *pMetaHost       = NULL;  
                ICLRMetaHostPolicy *pMetaHostPolicy = NULL;
                ICLRDebugging      *pCLRDebugging   = NULL;
                HRESULT hr;
                hr = CLRCreateInstance(CLSID_CLRMetaHost, IID_ICLRMetaHost,
                                    (LPVOID*)&pMetaHost);
                hr = CLRCreateInstance (CLSID_CLRMetaHostPolicy, IID_ICLRMetaHostPolicy,
                                    (LPVOID*)&pMetaHostPolicy);
                hr = CLRCreateInstance (CLSID_CLRDebugging, IID_ICLRDebugging,
                                    (LPVOID*)&pCLRDebugging);
                ICLRRuntimeInfo* pRuntimeInfo = NULL;
                hr = pMetaHost->GetRuntime(L"v4.0.30319", IID_ICLRRuntimeInfo, (VOID**)&pRuntimeInfo);
                BOOL bLoadable;
                hr = pRuntimeInfo->IsLoadable(&bLoadable);
                ICorRuntimeHost* pRuntimeHost = NULL;
                hr = pRuntimeInfo->GetInterface(CLSID_CorRuntimeHost,
                IID_ICorRuntimeHost,
                (VOID**)&pRuntimeHost);
                hr = pRuntimeHost->Start();
        IUnknownPtr pAppDomainThunk = NULL;
        hr = pRuntimeHost->GetDefaultDomain(&pAppDomainThunk);
        _AppDomainPtr pDefaultAppDomain = NULL;
        hr = pAppDomainThunk->QueryInterface(__uuidof(_AppDomain), (VOID**) &pDefaultAppDomain);
        _AssemblyPtr pAssembly = NULL;
        SAFEARRAYBOUND rgsabound[1];
        rgsabound[0].cElements = RAW_ASSEMBLY_LENGTH;
        rgsabound[0].lLbound   = 0;
        SAFEARRAY* pSafeArray  = SafeArrayCreate(VT_UI1, 1, rgsabound);
                                        void* pvData = NULL;
                                        hr = SafeArrayAccessData(pSafeArray, &pvData);
    
                                        //////// Add Functions Here ////////////////////
                                        int startint = 0;
                                        ////////////////////////////////////////////////
    
                                        memcpy(pvData, Raw_Net_Data, RAW_ASSEMBLY_LENGTH);
                                        hr = SafeArrayUnaccessData(pSafeArray);
                                        hr = pDefaultAppDomain->Load_3(pSafeArray, &pAssembly);
                                        _MethodInfoPtr pMethodInfo = NULL;
                                        hr = pAssembly->get_EntryPoint(&pMethodInfo);
                                        VARIANT retVal;
                                        ZeroMemory(&retVal, sizeof(VARIANT));
                                        VARIANT obj;
                                        ZeroMemory(&obj, sizeof(VARIANT));
                                        obj.vt = VT_NULL;
                                        SAFEARRAY *psaStaticMethodArgs = SafeArrayCreateVector(VT_VARIANT, 0, 0);
                                        hr = pMethodInfo->Invoke_3(obj, psaStaticMethodArgs, &retVal);
        return 0;
    }

**You can copy/paste xD whatever ...**

## **Step E )** Anti-Ripper Solutions

**We have 4 way to defeat a Ripper :**

-   A > Remove random hashes from array and replace it from another lost parts array in runtime before executing CLR.

1.  It's complex but it's fast.
2.  It's compressed and gives you less size in final PE File.
3.  Security in this method is less than other methods.

-   B > Convert all of hashes to **Strings Blocks** and create a hash array from them at runtime.

1.  It's heavy , takes more than 10 minutes to compile.
2.  It executes CLR fast at runtime , don't worry.
3.  It's very good at secuirty against rippers and debuggers.
4.  It's not compressed and gives you a high size exe file but you can compress it by a packer with 5-10% Ratio.

-   C > Convert all of hashes to string next crypt them with **Xor Encryption** and decrypt it at runtime.

1.  It's fast but a little complex.
2.  It's compressed.
3.  Awsome Secuirty!

-   D > Create it very simple as before and next crypt resources with a PE Protector like **VMProtect 3.0**

1.  Only expert hackers are able to reverse it.
2.  It's super compressed.
3.  it's very simple to use and takes no extra time.
4.  It's virtual with a perfect security!

> Which is better?! :
> 
> **It really depends on you to choose one of methods.**

-   Here we go , it's the converter of hashes to strings and a function to build hashes again:

1.  String arrays are limited then we need to create block chains and link them together.
2.  Data.txt Includes String Arrays and F_N.txt Includes Functions to recreate Hex array.
3.  Paste functions at `//////// Add Functions` Here Area at main code.

    int blockchainsize = 10000; ///Block Size of strings
    int time_in_block = (_countof(rawData))/(blockchainsize); /// Data Parts
    FILE *filex = fopen("C:\\Data.txt", "w");
    for( int ax = 0; ax <= time_in_block; ax = ax++ ) {
    fprintf(filex, "std::string Raw");
    fprintf(filex , "%d", ax);
    fprintf(filex, "[");
    fprintf(filex, "] = { \n");
    for( int i = (blockchainsize*ax); i < (blockchainsize*(ax+1)-1); i = i++ ) {
    fprintf(filex,"\"0x%X\"", (unsigned char)rawData[i]);
    fprintf(filex, ",");
    };
    fprintf(filex,"\"0x%X\"", (unsigned char)rawData[(blockchainsize*(ax+1)-1)]);
    fprintf(filex, "\n};");
    fprintf(filex, "\n");
    }
    fclose(filex);
    std::cout << "file created!" << "\n";
    FILE *filex2 = fopen("C:\\F_N.txt", "w");
    for( int ax = 0; ax <= time_in_block-1; ax = ax++ ) {
    fprintf(filex2, "for (int vx = 0; vx < _countof(Raw");
    fprintf(filex2 , "%d", ax);
    fprintf(filex2, "); vx++) {\n ");
    fprintf(filex2, "char valuex = strtol(Raw");
    fprintf(filex2 , "%d", ax);
    fprintf(filex2, "[vx");
    fprintf(filex2, "].c_str(),NULL,16);\n rawData[startint] = valuex;\n  startint = startint+1; \n }; \n");
    }
    fclose(filex2);

-   And here's a simple encrypter/decrypter for using at your project :

    std::string encryptDecrypt(std::string toEncrypt) {
        char key = 'X';
        std::string output = toEncrypt;
        
        for (int i = 0; i < toEncrypt.size(); i++)
            output[i] = toEncrypt[i] ^ key;
        
        return output;
    }

## **Step F )** Final Step!

-   Congratulations!

> Quote:
> 
> **Wubba Lubba Dub Dub ! You Did it !** - R.Sanchez

-   Last step is protecting your app using a Simple/Advanced Protector , VMProtect 3.0 ( The Best! ) or MPRESS from Microsoft.
-   I used **MPRESS** with custom stubs.

## Known Issues And Fixes

-   **First Issue >** App can still be ripped by .Net Generic Unpacker

1.  Add this code in your .net app with a timer:

  

     foreach (Process process_get in Process.GetProcesses())
                {
                    try
                    {
                        if (process_get.ProcessName.Contains("Unpack"))
                        {
                            process_get.Kill();
                        }
                    }
                    catch { }
                }

2. You can make this function more complex by killing process using file informations , MD5 Hashes and etc.

-   **Second Issue >** App's threads can still be paused , you can write anti-supsend on your c++ program.

You can use [this Article](https://www.codeproject.com/Articles/30815/An-Anti-Reverse-Engineering-Guide).

## Source Codes

-   **[Download Source Codes ( VS2012 - 64bit ) - 3.7 MB](https://www.codeproject.com/KB/security/1236146/CLR_Hosting_FromMemory_Src.zip)**
-   **[Download Demo File (.Net 4.5 / C++ 11.0) - 1.0 MB](https://www.codeproject.com/KB/security/1236146/CLR_Hosting_FromMemory_Demo.zip)**

## Conclusion

Hope this article works for you. As I said, there is no full function security but you can do the best!

-   I have to give a special thanks to **RandomVertex** for his Hosting 2.0 Framework CLR from memory.
-   If you have any problem or question , feel free and email me : hamidmemar666@gmail.com , I'm glad to help.

## What's Next?

- I will show you how to protect your c++ with a custom PEPacker, YEAH ! Unknown Protector \m/!

## History

- Sunday, 25 March 2018 : Tips Added.

- Friday, 23 March 2018 : Article Created.

**Regards ,**

**Hamid Memar**




