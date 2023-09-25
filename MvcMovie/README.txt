Name: Patricia Bialek
Project: MvcMovie (#3)
Date: 2023-09-18 IS0 8601 1000
- JSON in Properties folder to comment out //"sslPort": 44303
- ASP.NET Core MVC NET.3.1
https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-mvc-app/start-mvc?view=aspnetcore-3.1&tabs=visual-studio

{CONTROLLERS}
1. Added a new file to controllers - HelloWorldController.cs
2. replaced whats in the file with new code:
***
using Microsoft.AspNetCore.Mvc;
using System.Text.Encodings.Web;

namespace MvcMovie.Controllers
{
    public class HelloWorldController : Controller
    {
        // 
        // GET: /HelloWorld/

        public string Index()
        {
            return "This is my default action...";
        }

        // 
        // GET: /HelloWorld/Welcome/ 

        public string Welcome()
        {
            return "This is the Welcome action method...";
        }
    }
}
***

WHAT IS BEING DONE:
- The protocol used: HTTPS.
- The network location of the web server, including the TCP port: localhost:5001.
- The target URI: HelloWorld.
The first comment states this is an HTTP GET method that's invoked by appending /HelloWorld/ to the base URL.
The second comment specifies an HTTP GET method that's invoked by appending /HelloWorld/Welcome/ to the URL.

MVC invokes controller classes, and the action methods within them, depending on the incoming URL. The default URL routing logic used by MVC, uses a format like this to determine what code to invoke:
/[Controller]/[ActionName]/[Parameters]

PART 1
3. Type in /HelloWorld in search bar - "This is my default action..."; will pop up
4. /HelloWorld/Welcome - "This is the Welcome action method..."; will pop up
For this URL, the controller is HelloWorld and Welcome is the action method. You haven't used the [Parameters] part of the URL yet.

PART 2
5. Change the Welcome method to include two parameters as shown in the following code.
pass some parameter information from the URL to the controller.

***
// GET: /HelloWorld/Welcome/ 
// Requires using System.Text.Encodings.Web;
public string Welcome(string name, int numTimes = 1)
{
    return HtmlEncoder.Default.Encode($"Hello {name}, NumTimes is: {numTimes}");
}
***
6. /HelloWorld/Welcome?name=Rick&numtimes=4 - ($"Hello {name}, NumTimes is: {numTimes}"); Can change the name & number
- numTimes parameter defaults to 1 if no value is passed for that parameter.
- Uses HtmlEncoder.Default.Encode to protect the app from malicious input, such as through JavaScript.
- Try different values for name and numtimes in the URL. The MVC model binding system automatically maps the named parameters from the query string to parameters in the method.
- The name and numTimes parameters are passed in the query string.
- The ? (question mark) in the above URL is a separator, and the query string follows.
- The & character separates field-value pairs.

PART 3
7.Replace the Welcome method with the following code:

***
public string Welcome(string name, int ID = 1)
{
    return HtmlEncoder.Default.Encode($"Hello {name}, ID: {ID}");
}
***
8. /HelloWorld/Welcome/3?name=Rick - ($"Hello {name}, ID: {ID}"); Can change the name & number
- The third URL segment matched the route parameter id.
- The Welcome method contains a parameter id that matched the URL template in the MapControllerRoute method.
- The trailing ? starts the query string.



{VIEWS}
PART 1
1. Replace the default index method in the .cs file with:
***
public IActionResult Index()
        {
            return View();
        }
***
- Calls the controller's View method.
- Uses a view template to generate an HTML response.

2. Add a new folder in Views folder called - HelloWorld
3. Add a new file in that new folder - new view + keep the index name for it

4. Replac the code with this:
***
@{
    ViewData["Title"] = "Index";
}

<h2>Index</h2>

<p>Hello from our View Template!</p>
***
5. /HelloWorld - in the search bar
- The Index method in the HelloWorldController ran the statement return View();, which specified that the method should use a view template file to render a response to the browser.
- A view template file name wasn't specified, so MVC defaulted to using the default view file. When the view file name isn't specified, the default view is returned.

PART 2: CHANGE VIEWS AND LAYOUT PAGE
6. Open Views>Shared>Layout page
7. Add new changes:

***
<title>@ViewData["Title"] - Movie App</title>
<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>
&copy; 2020 - Movie App - <a asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
***
- Select the Home link. Notice that the title and anchor text display Movie App. 
The changes were made once in the layout template and all pages on the site reflect the new link text and new title.

8. Go to Views>ViewStart and replace with
***
@{
    Layout = "_Layout";
}
***

9. in the helloworld folder with the index file replace:
***
@{
    ViewData["Title"] = "Movie List";
}

<h2>My Movie List</h2>

<p>Hello from our View Template!</p>
***
- ViewData["Title"] = "Movie List"; in the code above sets the Title property of the ViewData dictionary to "Movie List". 
- The Title property is used in the <title> HTML element in the layout page

Notice that the following have changed:
- Browser title.
- Primary heading.
- Secondary headings.

PART 3: PASSING DATA FROM THE VIEW TO THE CONTROLLER
Controller actions are invoked in response to an incoming URL request. A controller class is where the code is written that handles the incoming browser requests. 
The controller retrieves data from a data source and decides what type of response to send back to the browser. 
View templates can be used from a controller to generate and format an HTML response to the browser.
Controllers are responsible for providing the data required in order for a view template to render a response.

A view template should work only with the data that's provided to it by the controller. Maintaining this "separation of concerns" helps keep the code:\
- Clean.
- Testable.
- Maintainable.

Currently, the Welcome method in the HelloWorldController class takes a name and an ID parameter and then outputs the values directly to the browser.
Rather than have the controller render this response as a string, change the controller to use a view template instead. 
The view template generates a dynamic response, which means that appropriate data must be passed from the controller to the view to generate the response. 
Do this by having the controller put the dynamic data (parameters) that the view template needs in a ViewData dictionary. The view template can then access the dynamic data.

10. In HelloWorldController.cs, change the Welcome method to add a Message and NumTimes value to the ViewData dictionary.
The ViewData dictionary is a dynamic object, which means any type can be used. The ViewData object has no defined properties until something is added. 
The MVC model binding system automatically maps the named parameters name and numTimes from the query string to parameters in the method. The complete HelloWorldController:

11. Create a Welcome view template named Views/HelloWorld/Welcome.cshtml. And add this code:
***
@{
    ViewData["Title"] = "Welcome";
}

<h2>Welcome</h2>

<ul>
    @for (int i = 0; i < (int)ViewData["NumTimes"]; i++)
    {
        <li>@ViewData["Message"]</li>
    }
</ul>
***
12. /HelloWorld/Welcome?name=Rick&numtimes=4 - in the search bar (can change name and number)

- You'll create a loop in the Welcome.cshtml view template that displays "Hello" NumTimes.
- Data is taken from the URL and passed to the controller using the MVC model binder. The controller packages the data into a ViewData dictionary and passes that object to the view. 
The view then renders the data as HTML to the browser.

{MODEL}
PART 1: Add a data model class/create a NuGet package
1. Right-click the Models folder > Add > Class. Name the file Movie.cs.
2. add the code in the new file:
***
using System;
using System.ComponentModel.DataAnnotations;

namespace MvcMovie.Models
{
    public class Movie
    {
        public int Id { get; set; }
        public string Title { get; set; }

        [DataType(DataType.Date)]
        public DateTime ReleaseDate { get; set; }
        public string Genre { get; set; }
        public decimal Price { get; set; }
    }
}
***

2. Add NuGet PACKAGE: From the Tools menu, select NuGet Package Manager > Package Manager Console (PMC).
3. run the command: Install-Package Microsoft.EntityFrameworkCore.SqlServer
- tools > NuGet package Manager > Manage NuGet package for solutions > find (Microsoft.EntityFrameworkCore.SqlServer) and install it
- Dependencies > Packages > click Microsoft.EntityFrameworkCore.SqlServer(3.0.0)


PART 2: Create a database context class/register the database context/examine the DB connection string
- A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the Movie model. 
The database context is derived from Microsoft.EntityFrameworkCore.DbContext and specifies the entities to include in the data model.

1. Create a Data folder (not in a specific folder). Add a Data/MvcMovieContext.cs file with the following code:
***
using Microsoft.EntityFrameworkCore;
using MvcMovie.Models;

namespace MvcMovie.Data
{
    public class MvcMovieContext : DbContext
    {
        public MvcMovieContext (DbContextOptions<MvcMovieContext> options)
            : base(options)
        {
        }

        public DbSet<Movie> Movie { get; set; }
    }
}
***
- The preceding code creates a DbSet<Movie> property for the entity set. 
In Entity Framework terminology, an entity set typically corresponds to a database table. An entity corresponds to a row in the table.

- ASP.NET Core is built with dependency injection (DI). Services (such as the EF Core DB context) must be registered with DI during application startup. 
Components that require these services (such as Razor Pages) are provided via constructor parameters. The constructor code that gets a DB context instance 
is shown later in the tutorial. In this section, you register the database context with the DI container.

2. Add the following using statements at the top of Startup.cs:
***
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
***

3. Add the following highlighted code in Startup.ConfigureServices:
***
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();

    //services.AddDbContext<MvcMovieContext>(options =>
    //options.UseSqlServer(Configuration.GetConnectionString("MvcMovieContext")));
}
***
- The name of the connection string is passed in to the context by calling a method on a DbContextOptions object. 
For local development, the ASP.NET Core configuration system reads the connection string from the appsettings.json file.

4. Add a connection string to the appsettings.json file:
***
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*",
  //"ConnectionStrings": {
    //"MvcMovieContext": "Server=(localdb)\\mssqllocaldb;Database=MvcMovieContext-1;Trusted_Connection=True;MultipleActiveResultSets=true"
  }
}
***


PART 3: Scaffold movie pages/Initial migration/The InitialCreate class
1. Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.
- In Solution Explorer, right-click the Controllers folder > Add > Controller
- add MVC Controller with views using entity framework
- add the neccessary info and add everything - there should be no error and in your Views folder > Movies folder: CRUD 

Complete the Add Controller dialog:
- Model class: Movie (MvcMovie.Models)
- Data context class: MvcMovieContext (MvcMovie.Data)
- Views: Keep the default of each option checked
- Controller name: Keep the default MoviesController
- Select Add

Visual Studio creates:
- A movies controller (Controllers/MoviesController.cs)
- Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/`.cshtml`)
- The automatic creation of these files is known as scaffolding.

*You can't use the scaffolded pages yet because the database doesn't exist. If you run the app and click on the Movie App link, you get a Cannot open database or no such table: Movie error message.

2. Use the EF Core Migrations feature to create the database. Migrations is a set of tools that let you create and update a database to match your data model.
- From the Tools menu, select NuGet Package Manager > Package Manager Console (PMC). In the PMC, enter the following commands:
***
- put them in seperatly
Add-Migration InitialCreate
Update-Database
***
- Created a Migrations folder > {#}_InitialCreate.cs
- Add-Migration InitialCreate: Generates a Migrations/{timestamp}_InitialCreate.cs migration file. 
The InitialCreate argument is the migration name. Any name can be used, but by convention, a name is selected that describes the migration. 
Because this is the first migration, the generated class contains code to create the database schema. The database schema is based on the model specified in the MvcMovieContext class.

- Update-Database: Updates the database to the latest migration, which the previous command created. This command runs the Up method in the Migrations/{time-stamp}_InitialCreate.cs file, 
which creates the database

3. Examine the Migrations/{timestamp}_InitialCreate.cs migration file:
***
public partial class InitialCreate : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.CreateTable(
            name: "Movie",
            columns: table => new
            {
                Id = table.Column<int>(nullable: false)
                    .Annotation("SqlServer:ValueGenerationStrategy", 
                                 SqlServerValueGenerationStrategy.IdentityColumn),
                Title = table.Column<string>(nullable: true),
                ReleaseDate = table.Column<DateTime>(nullable: false),
                Genre = table.Column<string>(nullable: true),
                Price = table.Column<decimal>(nullable: false)
            },
            constraints: table =>
            {
                table.PrimaryKey("PK_Movie", x => x.Id);
            });
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropTable(
            name: "Movie");
    }
}
***
- The Up method creates the Movie table and configures Id as the primary key. The Down method reverts the schema changes made by the Up migration.

*Test the app: Run the app and click the Movie App link. >click Create and create your first DB input


PART 4: Goes throguh file and explains it 
**always (Update-Database) in Console.


{WORKING WITH A DATABASE}
PART 1: SQL Server Express LocalDB/Seed the database/Add the seed initializer
1. From the View menu, open SQL Server Object Explorer (SSOX). *Refresh at top 
2. Right-click on the Movie table > View Designer 
3. Right-click on the Movie table > View Data

*ViewData["Title"] = "Index"; add to Views>Movies>Index at the layout null
*ViewData["Title"] = "Create"; add to Views>Movies>Create at the layout null
*do for all

1. Create a new class named SeedData in the Models folder. Replace the generated code with the following:
***
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;
using MvcMovie.Data;
using System;
using System.Linq;

namespace MvcMovie.Models
{
    public static class SeedData
    {
        public static void Initialize(IServiceProvider serviceProvider)
        {
            using (var context = new MvcMovieContext(
                serviceProvider.GetRequiredService<
                    DbContextOptions<MvcMovieContext>>()))
            {
                // Look for any movies.
                if (context.Movie.Any())
                {
                    return;   // DB has been seeded
                }

                context.Movie.AddRange(
                    new Movie
                    {
                        Title = "When Harry Met Sally",
                        ReleaseDate = DateTime.Parse("1989-2-12"),
                        Genre = "Romantic Comedy",
                        Price = 7.99M
                    },

                    new Movie
                    {
                        Title = "Ghostbusters ",
                        ReleaseDate = DateTime.Parse("1984-3-13"),
                        Genre = "Comedy",
                        Price = 8.99M
                    },

                    new Movie
                    {
                        Title = "Ghostbusters 2",
                        ReleaseDate = DateTime.Parse("1986-2-23"),
                        Genre = "Comedy",
                        Price = 9.99M
                    },

                    new Movie
                    {
                        Title = "Rio Bravo",
                        ReleaseDate = DateTime.Parse("1959-4-15"),
                        Genre = "Western",
                        Price = 3.99M
                    }
                );
                context.SaveChanges();
            }
        }
    }
}
***

1. Replace the contents of Program.cs with the following code:
***
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using MvcMovie.Data;
using MvcMovie.Models;
using System;

namespace MvcMovie
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();

            using (var scope = host.Services.CreateScope())
            {
                var services = scope.ServiceProvider;

                try
                {
                    SeedData.Initialize(services);
                }
                catch (Exception ex)
                {
                    var logger = services.GetRequiredService<ILogger<Program>>();
                    logger.LogError(ex, "An error occurred seeding the DB.");
                }
            }

            host.Run();

        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
    }
}
***
2. Test the app. The app shows the seeded data.


{CONTROLLER METHODS AND VIEWS}
1. Open the Models/Movie.cs file and add the highlighted lines shown below:
***
using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace MvcMovie.Models
{
    public class Movie
    {
        public int Id { get; set; }
        public string Title { get; set; }

        [Display(Name = "Release Date")]
        [DataType(DataType.Date)]
        public DateTime ReleaseDate { get; set; }
        public string Genre { get; set; }

        [Column(TypeName = "decimal(18, 2)")]
        public decimal Price { get; set; }
    }
}
***
- The Display attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate"). 
The DataType attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.

- The [Column(TypeName = "decimal(18, 2)")] data annotation is required so Entity Framework Core can correctly map Price to currency in the database. 