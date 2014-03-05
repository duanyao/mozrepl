mozrepl
=======

MozRepl lets you program Firefox and other Mozilla-based applications from the inside.

## Using with geckofx
[Geckofx](https://bitbucket.org/geckofx/) is a .net binding of xulrunner. In order to run mozrepl with geckofx, you should add some
bootsrap codes, for example (suppose mozrepl source is located at `D:\project\mozrepl.git`):

```C#
        static nsILocalFile toNsFile(string file) {
            var nsfile = Xpcom.CreateInstance<nsILocalFile>("@mozilla.org/file/local;1");
            nsfile.InitWithPath(new nsAString(file));
            return nsfile;
        }
        
        static void registerChromeDir(string dir) {
            var chromeDir = toNsFile(dir);
            var chromeFile = chromeDir.Clone();
            chromeFile.Append(new nsAString("chrome.manifest"));
            Xpcom.ComponentRegistrar.AutoRegister(chromeFile);
            Xpcom.ComponentManager.AddBootstrappedManifestLocation(chromeDir);
        }

        static void startMozRepl() {
            var mozrepl = Xpcom.CreateInstance<MozRepl.nsIMozRepl>("@hyperstruct.net/mozlab/mozrepl;1");
            mozrepl.Start(4242, true);
        }

        public static void Main(string[] args)
		{
        //... After Xpcom.Initialize()
            registerChromeDir(@"D:\project\mozrepl.git");
            GeckoPreferences.Load(@"D:\project\mozrepl.git\defaults\preferences\mozrepl.js");
            startMozRepl();
        //...
        }
```

Interface `MozRepl.nsIMozRepl` can be found in `D:\project\mozrepl.git\bindings\MozRepl.cs`, you may add this file to your .net project.

For now, mozrepl in geckofx connects to the browser window most recently openned, see line 129-137 of `server.js`.
