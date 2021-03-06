# CakeApp
A .Net Core solution template using the [Cake](http://cakebuild.net/) build system. It provides an initial console project and a corresponding [XUnit](https://xunit.github.io/) test project. Furthermore it adds cake build scripts for Windows and Linux.

## Template layout
This template creates the following structure on your disk, where *CakeApp* is replaced by the name of your solution.

```
|-> CakeApp.sln
|-> src
|   |-> CakeApp.Console
|   |   |-> CakeApp.Console.csproj
|   |   |-> Program.cs
|   |   |-> appsettings.json
|-> test
|   |-> CakeApp.Console_Test
|   |   |-> CakeApp.Console_Test.csproj
|   |   |-> UnitTest1.cs
|-> .gitignore
|-> build.cake
|-> build.ps1
|-> build.sh
|-> Dockerfile
|-> docker.sh
|-> Readme.md
```

## Installation
On Windows PowerShell:
```
Install-Package CakeApp -Destination .\CakeApp.1.0.1.nupkg

dotnet new -i .\CakeApp.1.0.1.nupkg
```
It could be necessary to run the installation from an elevated shell.

On Linux:
```
wget https://www.nuget.org/api/v2/package/CakeApp/1.0.1 -O CakeApp.1.0.1.nupkg

dotnet new -i ./CakeApp.1.0.1.nupkg
```  

## Usage
This section will shortly describe how to use the template.

### Create a new solution
To create a new solution based on the template use

`dotnet new cake -o [your solution name]`

This will create a corresponding Visual Studio solution with the structure described above on your disk. You can now proceed to write your code in the given projects or at more projects if you need so.

### Build the solution
To build the solution the [Cake](http://cakebuild.net/) build system is used. This template comes with a pre-defined Cake build script `build.cake` with some default build targets. Of course feel free to alter the script if it doesn't fit all your needs.

To run a build on the PowerShell type:
`build.ps1 -Target [target name]` 
or just `build.ps1` to run the default target `test`.

On Linux run:
```
dos2unix build.sh
build.sh --target [target name]
```
or just `build.sh` to run the default target `test`. The *dos2unix* command is needed since Windows changes the line endings in the bash script when the NuGet package is created. You have to to the command only once.

| Build Target | Description | Depends on |
| ------------ | ----------- | ---------- |
| PrepareDirectories | Ensures that all needed directories for the build are available in your solution directory. | - |
| Clean | Cleans your last Cake build and deletes all build artifacts. | PrepareDirectories |
| Restore | Restores all NuGet packages in your projects. It will try it up to five times, since sometimes the restore does not work on the first try. | - |
| Build | Builds your whole solution with the *Release* configuration. | Restore |
| Test | Runs all Unit test projects in the *test* folder which project names are ending with **_Test*. Other projects are ignored. The test results **.trx* files are put into the *test-results* folder. | Clean, Build | 
| Pack | Packages all projects from the *src* folder into corresponding NuGet packages. The packages are placed in the *artifacts* folder. | Clean, Test |
| Publish | Publishes all projects from *src* to the *artifacts* folder. You can use the published projects to run them every where else. | Clean, Test |
| Default | The same as *Test*. If no target is given, this one is used. | Clean, Build |

### Dockerize the application
You may have noticed the *Dockerfile* that was created in your solution. It will use your *CakeApp.Console* application from the *src* folder as the starting point of a Docker app if you build an image with it.

To build an Docker image containing your application type:
```
build.sh --target publish
docker build -t cakeapp .
```
This will create an image in your local Docker registry called *cakeapp*. You can run it with `docker run cakeapp`


