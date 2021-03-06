# Setup Step: Create a .NET Core app, then set up ASP.NET  

## Objectives
- Create a basic .NET Core app using the .NET CLI
- Put in the core code needed to set up ASP.NET Core - its dependencies and host initialization

## Let's get started

1. Open a command line, create and go into a new directory, and run `dotnet new`, then `dotnet run` to create & run a .NET Core 'Hello World!' app.
1. Type `code .` to open VS Code for directory. Add ASP.NET Core dependencies to project.json:

    ```
    "dependencies": {

        "Microsoft.NETCore.App": {
            "version": "1.0.0",
            "type": "platform"
        },
        "Microsoft.AspNetCore.Server.Kestrel": "1.0.0"

    }
    ```

1. Add ASP.NET Core setup code to the Main method (entry point of all .NET Core applications, including ASP.NET Core) in Program.cs, and remove the console line:

    ```
    // Minimal ASP.NET setup in Main method

    var host = new WebHostBuilder()
        .UseKestrel()
        .Configure(app => app.Run(context => context.Response.WriteAsync("Hello World!")))
        .Build();

    host.Run();


    // NOTE, these are needed and can be added manually, though you can use the IDE (if VS Code + C# extension, or VS)
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Http;
    using Microsoft.AspNetCore.Hosting;
    ```

    At this point, you have a minimal ASP.NET Core app set up. You can navigate to http://localhost:5000 to get a hello world response.

1. Move configuration of the host to the Startup class, per convention.

    1. Tweak your Program.cs to use a Startup class for ASP.NET Core host configuration, and remove the host configuration:

        ```
        //REMOVE THIS: .Configure(app => app.Run(context => context.Response.WriteAsync("Hello World, from ASP.NET!")))                    
        .UseStartup<Startup>()
        ```

    2. Create a class in the project root called Startup.cs
    3. Add this to your Startup.cs class (you will then have to add references, via your IDE ideally):

        ```
        namespace ConsoleApplication
        {
            public class Startup
            {
                // This method gets called by the runtime, after ConfigureServices, and is required. Use this method to configure the HTTP request pipeline.
                // IApplicationBuilder is required; provides the mechanisms to configure an application’s request pipeline.
                // Middleware is configured here.
                public void Configure(IApplicationBuilder app)
                {
                    app.Run(context => context.Response.WriteAsync("Hello World, from ASP.NET!"));
                }
            }
        }
        ```

1. Configure the app to use IIS/IIS Express for the web server & set the content root

    1. Add the following lines before Build() in the Program.cs main method:
        ```     
        //  If the app should work with IIS, the UseIISIntegration method should be called as part of building the host. Note that this does not configure a server, like UseKestrel does.
        //  To use IIS with ASP.NET Core, you must specify both UseKestrel and UseIISIntegration. Kestrel is designed to be run behind a proxy and should not be deployed directly facing the Internet.
        //  UseIISIntegration specifies IIS as the reverse proxy server.
        .UseIISIntegration() // Reverse proxy using IIS & IIS Express. It does not deal with IServer as Kestrel does. This call configures the port and base path the server should listen on when running behind AspNetCoreModule, and also to capture startup errors.

        .UseContentRoot(Directory.GetCurrentDirectory()) //  The server’s content root determines where it searches for content files, like MVC View files. The default content root is the folder from which the application is run.
        ```

    1. You'll need to add this dependency for IIS integration, to project.json:

        ```
        "Microsoft.AspNetCore.Server.IISIntegration": "1.0.0"
        ```

    Then you can restore and run again.

1. We're going to add a watcher now so when we change our source files, it rebuilds and re-runs our app,

    ```
    "tools": {
    "Microsoft.DotNet.Watcher.Tools": "1.0.0-*"
    ```

  Now when we run `dotnet watch run`, the watcher will be active.

When you have completed this step, [continue to step 1 to create a basic ASP.NET Web API app](https://github.com/Wyntuition/aspnetcore-workshop-kit/tree/master/01-BasicApiApp)
