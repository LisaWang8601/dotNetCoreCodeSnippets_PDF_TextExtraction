# dotnet-Core notes of creating a web application and manipulate a relational database 

1.	Create container, name whatever
2.	Add new project RC on container name in solution explorer, remember change authentication  to “individual user account”
3.	warning: Better don’t change added controller names, delete controller and view folder and add it again/  if need to change the name, the view folder name needed to be changed accordingly manually.!!! 
???namespace  using LWBusService. Models & LWBusService.Data
4.	//teacher's connection  - save and comment it out
Data Source=(localdb)\mssqllocaldb;Initial Catalog=BusService;Integrated Security=True;

Scaffold-DbContext –Connection "Data Source=(localdb)\mssqllocaldb;Initial Catalog=BusService;Integrated Security=True;" -Provider "Microsoft.EntityFrameworkCore.SqlServer" -OutputDir "Models" –Context "BusServiceContext" –Verbose    -Force

5.	// my connection string and scaffold
// add connection string inside appsettings.json
  "ConnectionStrings": {
    "BusServiceConnection": "Data Source=.\\SQLEXPRESS; Initial Catalog=BusService; Integrated Security=True;"
  },
The forward slashes numbers are different in the data connection string for appsetting.json and for scaffolding.
Data Source=.\\SQLEXPRESS;Initial Catalog=BusService;Integrated Security=True
Scaffold-DbContext –Connection "Data Source=.\SQLEXPRESS;Initial Catalog=BusService;Integrated Security=True;" -Provider "Microsoft.EntityFrameworkCore.SqlServer" -OutputDir "Models" –Context "BusServiceContext" –Verbose    -Force

// Run Scaffolding and run the solution once before adding any controllers.
=========footer inside shared/_layout.cshtml         - contain my name with link to my email, about and contact float to the right, on same row. 
<footer>
            <p>
                <span style="float: right;">
                    <a asp-area="" asp-controller="Home" asp-action="About">About &nbsp;</a>
                    <a asp-area="" asp-controller="Home" asp-action="Contact">Contact</a>
                </span>
                &copy; 2019 - LWOEC <a href="mailto:lwang2597@conestogac.on.ca">Lisa Wang</a>
            </p>
</footer>
6.	add startup.cs connection string: copy “Services…..defaultConnection” change to “Services…..BusServiceConnection”
            services.AddDbContext<BusServiceContext>(options =>
                options.UseSqlServer(
                   Configuration.GetConnectionString("BusServiceConnection")));

7. !! Delete (comment out ) connection string in BusRouteContext.cs around the warning!!!


8. Add LWBusRouteController // parent table  -- view the table keys in the database diagram in SSMS!
  Add LWRouteStopController   // child table  
// following 2 steps can be done earlier!
// following steps 9-11 are in _layout.cshtml
9. change footer as teacher’s example
		<footer>
            <div class="row">
                <div style="padding-top:0.5em;" class="left col-sm-6">
                    <a href="mailto:lwang2597@conestogac.on.ca">lwang2597@conestogac.on.ca</a>
                    <p>Bus Service</p>
                </div><!--end col01-->
                <div class="col-sm-6">
                    <ul style="float: right;" class="nav navbar-nav">
                        <li><a asp-area="" asp-controller="Home" asp-action="About">About</a></li>
                        <li><a asp-area="" asp-controller="Home" asp-action="Contact">Contact</a></li>
                    </ul>
                </div><!--end col02-->
            </div><!--end row01-->
        </footer>
10. change menu links after “home” link. 
<ul class="nav navbar-nav">
                    <li><a asp-area="" asp-controller="Home" asp-action="Index">Home</a></li>
                    <li><a asp-area="" asp-controller="LWBusRoute" asp-action="Index">Bus Routes</a></li>
                    <li><a asp-area="" asp-controller="LWRouteStop" asp-action="Index">Route Stops</a></li>
               </ul>
11. <!--TempData notification area #1c9046-->
<div style="background-color:lightseagreen; color:coral; height: 3em; padding-top:0.65em; border:3px dotted yellow; text-shadow: 1px 1px black; text-align:center; font-weight:800;">
@TempData["message"]
</div>
* Of course, this has to have a matching action in the Controller… *
12. style for <body > in site.css
body {
    padding-top: 50px; padding-bottom: 20px;  height: auto;  width: auto;  margin: 1em auto;  background-color:#222;  color: antiquewhite;
}
????  what are these styles for .center{
    height:auto;
    width:auto;
    margin:1em auto;
}
.left{
    float:left;
}
.right{
    float:right;
}
.clear{
    clear:both;
}

13. Page titles & headings all reference the target table. Basically, the Titles are all that appear in the browser tab.
Headings are the <h2> tags at the top of all the views. 
Ex. BusRoute   index.cshtml
@{
    ViewData["Title"] = "Bus Routes Listing";
}
<h2>Bus Routes</h2>
<p>
    <a asp-action="Create">Create New Bus Route</a>
</p>

14.  // BusRoute, index.cshtml, @foreach (var item in Model) {    <tr>     <td> 
// add link to busRouteCode, so clicking on the route code, will go to route stop page. (select list need to be done still)
<a asp-controller="LWRouteStop" asp-action="Index" asp-route-BusRouteCode="@item.BusRouteCode" asp-route-RouteName="@item.RouteName">@Html.DisplayFor(modelItem => item.BusRouteCode)</a>


15. In BusRouteController:
        // GET: LWBusRoute
        public async Task<IActionResult> Index()
        {
            TempData["message"] = "Welcome to our Bus Routes Home page...";

            var BusRouteContext = _context.BusRoute.Include(br => br.RouteStop).OrderBy(br => br.BusRouteCode).ToListAsync();

            return View(await BusRouteContext);
            //return View(await _context.BusRoute.ToListAsync());
        }
16. // Hyperlink on each line redirects user to RouteStop listing   index.cshtml
// Link passes busRouteCode
<td>
<a asp-controller="SHRouteStop" asp-action="Index" asp-route-BusRouteCode="@item.BusRouteCode" asp-route-RouteName="@item.RouteName">@Html.DisplayFor(modelItem => item.BusRouteCode)</a>
</td>







??? Page title & heading correct   
In routeStop index.cshtml, <h2>Stops for route<span>@ViewData["BusRouteCode"]</span> - <span>@ViewData["RouteName"]</span></h2>



