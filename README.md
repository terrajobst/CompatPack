# Windows Compat Pack for .NET Standard

## Open questions

* What does it mean to be in the Windows compat pack?
    - Single gesture to get .NET Framework support?
    -- or --
    - "Here is a bunch of legacy stuff"
* We need a name and a title for the package
* Do we need to cover non-CoreFX packages?
    - Likely yes, like WCF
* Will this compat pack install into .NET Standard?
    - If folks need to share code between .NET Framework and .NET Core, we tell
      them to use .NET Standard
    - This would make customers want to reference the compat pack from .NET
      Standard
    - However, this doesn't mean the result will work on all .NET
      implementations
    - 2.0 seems easy, some packages also support lower versions
    - Concern is that a good chunk of the package will throw
      `PlatformNotSupportedException`
    - However, we could issue a warning during build for the most common cases,
      such as building a project on Linux consuming this package
* Should we flat the compat pack?
    - Most of the packages already shipped on nuget.org
    - We can dead-end them, assuming we preserve all the customer scenarios they
      currently have
    - We need to think about cross-platform
    - We don't care about polluting the output
* What about UWP? It seems the same reasons of wanting to make existing code
  work would apply there
    - Not all packages will work in UPW
    - Supporting UWP would probably require a separate package
* Evolution
    - We should emphasize first party and drive this much harder
    - Directory services: they are OK with maintaining if we do the porting,
      tests missing.
    - Compat pack should ideally be a separate repo with a policy of no feature
      work being accepted.

## Existing Packages

I've computed the set of all the existing packages that contain types that ship
in .NET Framework. I've then annotated which one these packages are already part
of .NET Core, .NET Standard, and whether they are already referenced from the
[compat pack](https://github.com/dotnet/corefx/blob/master/pkg/Microsoft.NETFramework.Compatibility/Microsoft.NETFramework.Compatibility.pkgproj#L16).

The proposal is to add all the packages that aren't part of .NET Core. Funny
enough this means three packages shouldn't be part of the compat pack but are
currently referenced:

* System.ComponentModel.Annotations
* System.Numerics.Vectors
* System.Reflection.TypeExtensions

**Question: why is that?**

| Package ID                                        | Already in Compat Pack | In .NET Core | In .NET Standard | Windows Only | Proposal | Comment                |
|---------------------------------------------------|------------------------|--------------|------------------|--------------|----------|------------------------|
| Microsoft.Cci                                     | No                     | No           | No               |              | Out      | Not relevant           |
| Microsoft.CSharp                                  | No                     | Yes          | No               |              | Out      | Already referenced     |
| Microsoft.VisualBasic                             | No                     | Yes          | No               |              | Out      | Already referenced     |
| Microsoft.Win32.Primitives                        | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| Microsoft.Win32.Registry                          | No                     | No           | No               | Yes          | In       |                        |
| Microsoft.Win32.Registry.AccessControl            | No                     | No           | No               | Yes          | In       |                        |
| System.AppContext                                 | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.CodeDom                                    | Yes                    | No           | No               |              | In       |                        |
| System.Collections                                | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Collections.Concurrent                     | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Collections.NonGeneric                     | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Collections.Specialized                    | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.ComponentModel                             | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.ComponentModel.Annotations                 | Yes                    | Yes          | No               |              | Out      | Already referenced     |
| System.ComponentModel.EventBasedAsync             | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.ComponentModel.Primitives                  | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.ComponentModel.TypeConverter               | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Configuration.ConfigurationManager         | Yes                    | No           | No               |              | In       |                        |
| System.Console                                    | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Data.Common                                | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Data.SqlClient                             | Yes                    | No           | No               |              | In       |                        |
| System.Diagnostics.Contracts                      | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Diagnostics.Debug                          | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Diagnostics.FileVersionInfo                | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Diagnostics.Process                        | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Diagnostics.StackTrace                     | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Diagnostics.TextWriterTraceListener        | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Diagnostics.Tools                          | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Diagnostics.TraceSource                    | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Diagnostics.Tracing                        | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Drawing.Primitives                         | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Dynamic.Runtime                            | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Globalization                              | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Globalization.Calendars                    | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Globalization.Extensions                   | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.IO                                         | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.IO.Compression                             | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.IO.Compression.ZipFile                     | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.IO.FileSystem                              | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.IO.FileSystem.AccessControl                | Yes                    | No           | No               | Yes          | In       |                        |
| System.IO.FileSystem.DriveInfo                    | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.IO.FileSystem.Primitives                   | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.IO.FileSystem.Watcher                      | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.IO.IsolatedStorage                         | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.IO.MemoryMappedFiles                       | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.IO.Packaging                               | Yes                    | No           | No               |              | In       |                        |
| System.IO.Pipes                                   | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.IO.Pipes.AccessControl                     | Yes                    | No           | No               | Yes          | In       |                        |
| System.IO.Ports                                   | Yes                    | No           | No               |              | In       |                        |
| System.IO.UnmanagedMemoryStream                   | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Linq                                       | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Linq.Expressions                           | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Linq.Parallel                              | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Linq.Queryable                             | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Net.Http                                   | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Net.NameResolution                         | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Net.NetworkInformation                     | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Net.Ping                                   | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Net.Primitives                             | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Net.Requests                               | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Net.Security                               | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Net.Sockets                                | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Net.Utilities                              | No                     | No           | No               |              | In       |                        |
| System.Net.WebHeaderCollection                    | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Net.WebSockets                             | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Net.WebSockets.Client                      | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Numerics.Vectors                           | Yes                    | Yes          | No               |              | Out      | Already referenced     |
| System.ObjectModel                                | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Private.DataContractSerialization          | No                     | No           | No               |              | Out      | Implementation details |
| System.Private.Uri                                | No                     | No           | No               |              | Out      | Implementation details |
| System.Reflection                                 | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Reflection.Context                         | No                     | No           | No               |              | In       |                        |
| System.Reflection.Emit                            | No                     | No           | No               |              | In       |                        |
| System.Reflection.Emit.ILGeneration               | No                     | No           | No               |              | In       |                        |
| System.Reflection.Emit.Lightweight                | No                     | No           | No               |              | In       |                        |
| System.Reflection.Extensions                      | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Reflection.Primitives                      | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Reflection.TypeExtensions                  | Yes                    | Yes          | No               |              | Out      | Already referenced     |
| System.Resources.Reader                           | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Resources.ResourceManager                  | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Resources.Writer                           | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Runtime                                    | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Runtime.CompilerServices.VisualC           | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Runtime.Extensions                         | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Runtime.Handles                            | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Runtime.InteropServices                    | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Runtime.InteropServices.RuntimeInformation | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Runtime.InteropServices.WindowsRuntime     | No                     | No           | No               |              | In       |                        |
| System.Runtime.Numerics                           | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Runtime.Serialization.Formatters           | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Runtime.Serialization.Json                 | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Runtime.Serialization.Primitives           | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Runtime.Serialization.Xml                  | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Security.AccessControl                     | Yes                    | No           | No               | Yes          | In       |                        |
| System.Security.Claims                            | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Security.Cryptography.Algorithms           | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Security.Cryptography.Cng                  | Yes                    | No           | No               | Yes          | In       |                        |
| System.Security.Cryptography.Csp                  | No                     | Yes          | Yes              | Yes          | Out      | Already referenced     |
| System.Security.Cryptography.Encoding             | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Security.Cryptography.Pkcs                 | Yes                    | No           | No               | Yes          | In       |                        |
| System.Security.Cryptography.Primitives           | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Security.Cryptography.ProtectedData        | Yes                    | No           | No               | Yes          | In       |                        |
| System.Security.Cryptography.X509Certificates     | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Security.Cryptography.Xml                  | Yes                    | No           | No               | Yes          | In       |                        |
| System.Security.Permissions                       | Yes                    | No           | No               |              | In       |                        |
| System.Security.Principal                         | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Security.Principal.Windows                 | Yes                    | No           | No               | Yes          | In       |                        |
| System.Security.SecureString                      | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.ServiceModel.Duplex                        | No                     | No           | No               |              | In       |                        |
| System.ServiceModel.Http                          | No                     | No           | No               |              | In       |                        |
| System.ServiceModel.NetTcp                        | No                     | No           | No               |              | In       |                        |
| System.ServiceModel.Primitives                    | No                     | No           | No               |              | In       |                        |
| System.ServiceModel.Security                      | No                     | No           | No               |              | In       |                        |
| System.ServiceProcess.ServiceController           | Yes                    | No           | No               |              | In       |                        |
| System.Text.Encoding                              | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Text.Encoding.Extensions                   | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Text.RegularExpressions                    | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Threading                                  | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Threading.AccessControl                    | Yes                    | No           | No               | Yes          | In       |                        |
| System.Threading.Overlapped                       | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Threading.Tasks                            | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Threading.Tasks.Parallel                   | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Threading.Thread                           | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Threading.ThreadPool                       | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Threading.Timer                            | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.ValueTuple                                 | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Xml.ReaderWriter                           | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Xml.XDocument                              | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Xml.XmlDocument                            | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Xml.XmlSerializer                          | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Xml.XPath                                  | No                     | Yes          | Yes              |              | Out      | Already referenced     |
| System.Xml.XPath.XDocument                        | No                     | Yes          | Yes              |              | Out      | Already referenced     |

## Immo

* Mark the packages in our list and link to issues
* Create a deck for the directors

## Wes

* Where does the current set of packages work? .NET Standard X? UWP?