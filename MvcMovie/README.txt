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

**I added my git to my project
***cloned a repository

{ADD A SEARCH}
PART 1: Add a search
1. Add search capability to the Index action method that lets you search movies by genre or name.
2. Update the Index method found inside Controllers/MoviesController.cs with the following code:
***
public async Task<IActionResult> Index(string searchString)
{
    var movies = from m in _context.Movie
                 select m;

    if (!String.IsNullOrEmpty(searchString))
    {
        movies = movies.Where(s => s.Title.Contains(searchString));
    }

    return View(await movies.ToListAsync());
}
***
- The first line of the Index action method creates a LINQ query to select the movies.
- The query is only defined at this point, it has not been run against the database.
If the searchString parameter contains a string, the movies query is modified to filter on the value of the search string.
- The s => s.Title.Contains() code above is a Lambda Expression. Lambdas are used in method-based LINQ queries as arguments to 
standard query operator methods such as the Where method or Contains (used in the code above). 
LINQ queries are not executed when they're defined or when they're modified by calling a method such as Where, Contains, or OrderBy. Rather, query execution is deferred. 
That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the ToListAsync method is called.

3. Navigate to /Movies/Index. Append a query string such as ?searchString=Ghost to the URL. The filtered movies are displayed.

4. Update the the Index method found inside Controllers/MoviesController.cs with the following code:
***
public async Task<IActionResult> Index(string id)
{
    var movies = from m in _context.Movie
                 select m;

    if (!String.IsNullOrEmpty(id))
    {
        movies = movies.Where(s => s.Title.Contains(id));
    }

    return View(await movies.ToListAsync());
}
***
- You can now pass the search title as route data (a URL segment) instead of as a query string value.(/index/ghost)

5. Update the the Index method found inside Controllers/MoviesController.cs with the following code:
***
public async Task<IActionResult> Index(string searchString)
{
    var movies = from m in _context.Movie
                 select m;

    if (!String.IsNullOrEmpty(searchString))
    {
        movies = movies.Where(s => s.Title.Contains(searchString));
    }

    return View(await movies.ToListAsync());
}
***

6. Open the Views/Movies/Index.cshtml file, and add the <form> markup highlighted below:
***
<form asp-controller="Movies" asp-action="Index">
    <p>
        Title: <input type="text" name="SearchString" />
        <input type="submit" value="Filter" />
    </p>
</form>
***
7. Change the Views/Movies/Index.cshtml file, and add the <form> markup highlighted below:
***
<form asp-controller="Movies" asp-action="Index" method="get">
***
- Now when you submit a search, the URL contains the search query string.

PART 2: Add a search by genre
1. Add the following MovieGenreViewModel class to the Models folder:
***
using Microsoft.AspNetCore.Mvc.Rendering;
using System.Collections.Generic;

namespace MvcMovie.Models
{
    public class MovieGenreViewModel
    {
        public List<Movie> Movies { get; set; }
        public SelectList Genres { get; set; }
        public string MovieGenre { get; set; }
        public string SearchString { get; set; }
    }
}
***
2. Replace the Index method in MoviesController.cs with the following code:
***
// GET: Movies
public async Task<IActionResult> Index(string movieGenre, string searchString)
{
    // Use LINQ to get list of genres.
    IQueryable<string> genreQuery = from m in _context.Movie
                                    orderby m.Genre
                                    select m.Genre;

    var movies = from m in _context.Movie
                 select m;

    if (!string.IsNullOrEmpty(searchString))
    {
        movies = movies.Where(s => s.Title.Contains(searchString));
    }

    if (!string.IsNullOrEmpty(movieGenre))
    {
        movies = movies.Where(x => x.Genre == movieGenre);
    }

    var movieGenreVM = new MovieGenreViewModel
    {
        Genres = new SelectList(await genreQuery.Distinct().ToListAsync()),
        Movies = await movies.ToListAsync()
    };

    return View(movieGenreVM);
}
***
- The LINQ query that retrieves all the genres from the database.

PART 3: Add search by genre to the Index view
1. Update Index.cshtml found in Views/Movies/ as follows:
***
1. @model MvcMovie.Models.MovieGenreViewModel
2. <select asp-for="MovieGenre" asp-items="Model.Genres">
            <option value="">All</option>
        </select>

        Title: <input type="text" asp-for="SearchString" />
3. <thead>
        <tr>
            <th>
                @Html.DisplayNameFor(model => model.Movies[0].Title)
            </th>
            <th>
                @Html.DisplayNameFor(model => model.Movies[0].ReleaseDate)
            </th>
            <th>
                @Html.DisplayNameFor(model => model.Movies[0].Genre)
            </th>
            <th>
                @Html.DisplayNameFor(model => model.Movies[0].Price)
            </th>
            <th></th>
        </tr>
    </thead>
4.  @foreach (var item in Model.Movies)
***


{ADD A NEW FIELD}
PART 1: Add a Rating Property to the Movie Model
1. Add a Rating property to Models/Movie.cs:
***
public string Rating { get; set; }
***
2. Build the app: Ctrl+Shift+B

3. Because you've added a new field to the Movie class, you need to update the property 
binding list so this new property will be included. In MoviesController.cs, update the [Bind] attribute for both the Create and Edit action methods to include the Rating property:
***
,Ratin (add it to end)
***

4. Edit the /Views/Movies/Index.cshtml file and add a Rating field:
***
1.
<th>
            @Html.DisplayNameFor(model => model.Movies[0].Rating)
</th>
2.
<td>
             @Html.DisplayFor(modelItem => item.Rating)
</td>
***

5. Update the /Views/Movies/Create.cshtml,delete,details,edit.
6. Update the SeedData class in models, provides a value or the new column. A sample change is shown below.
***
,
Rating = "R"
***
6. update the DB: 1. Add-Migration Rating 2. Update-Database


{ADD VALIDATION}
Part 1: Add validation rules to the movie model
The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.
DataAnnotations also contains formatting attributes like DataType that help with formatting and don't provide any validation.
1. Update the Movie class to take advantage of the built-in Required, StringLength, RegularExpression, and Range validation attributes.
***
    public class Movie
    {
        public int Id { get; set; }

        [StringLength(60, MinimumLength = 3)]
        [Required]
        public string Title { get; set; }

        [Display(Name = "Release Date")]
        [DataType(DataType.Date)]
        public DateTime ReleaseDate { get; set; }

        [RegularExpression(@"^[A-Z]+[a-zA-Z\s]*$")]
        [Required]
        [StringLength(30)]
        public string Genre { get; set; }

        [Range(1, 100)]
        [DataType(DataType.Currency)]
        [Column(TypeName = "decimal(18, 2)")]
        public decimal Price { get; set; }

        public string Rating { get; set; }
    }
***
Having usses with rating validation.