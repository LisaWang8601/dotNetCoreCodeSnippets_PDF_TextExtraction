# dotnet-Core notes of creating a web application and manipulate a relational database 

1.	Create container, name LW+(database name)+container
2.	Add new project RC on container name in solution explorer, remember change authentication  to “individual user account”
3.	warning: Better don’t change added controller names, delete controller and view folder and add it again/  if need to change the name, the view folder name needed to be changed accordingly manually.!!! 
???namespace  using LWBusService. Models & LWBusService.Data
4.	//teacher's connection  - save and comment it out
Data Source=(localdb)\mssqllocaldb;Initial Catalog=BusService;Integrated Security=True;

5.	Scaffold-DbContext –Connection "Data Source=(localdb)\mssqllocaldb;Initial Catalog=BusService;Integrated Security=True;" -Provider "Microsoft.EntityFrameworkCore.SqlServer" -OutputDir "Models" –Context "BusServiceContext" –Verbose    -Force

6.	// my connection string and scaffold
// add connection string inside appsettings.json
  "ConnectionStrings": {
    "BusServiceConnection": "Data Source=.\\SQLEXPRESS; Initial Catalog=BusService; Integrated Security=True;"
  },
The forward slashes numbers are different in the data connection string for appsetting.json and for scaffolding.
Data Source=.\\SQLEXPRESS;Initial Catalog=BusService;Integrated Security=True
7.	Scaffold-DbContext –Connection "Data Source=.\SQLEXPRESS;Initial Catalog=BusService;Integrated Security=True;" -Provider "Microsoft.EntityFrameworkCore.SqlServer" -OutputDir "Models" –Context "BusServiceContext" –Verbose    -Force

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
8.	add startup.cs connection string: copy “Services…..defaultConnection” change to “Services…..BusServiceConnection”
            services.AddDbContext<BusServiceContext>(options =>
                options.UseSqlServer(
                   Configuration.GetConnectionString("BusServiceConnection")));

8. !! Delete (comment out ) connection string in BusRouteContext.cs around the warning!!!
          // Run Scaffolding and run the solution once before adding any controllers.


9. Add LWBusRouteController // parent table  -- view the table keys in the database diagram in SSMS!
  Add LWRouteStopController   // child table  
// following 2 steps can be done earlier!
// following steps 9-11 are in _layout.cshtml
10. change footer as teacher’s example
		<footer>
            <div class="row">
                <div style="padding-top:0.5em;" class="left col-sm-6">
                    <a href="mailto:lwang2597@conestogac.on.ca">lwang2597@conestogac.on.ca</a>
                    <p>Section 1</p>
                </div><!--end col01-->
                <div class="col-sm-6">
                    <ul style="float: right;" class="nav navbar-nav">
                        <li><a asp-area="" asp-controller="Home" asp-action="About">About</a></li>
                        <li><a asp-area="" asp-controller="Home" asp-action="Contact">Contact</a></li>
                    </ul>
                </div><!--end col02-->
            </div><!--end row01-->
        </footer>
11. change menu links beside the “home” link. 
<ul class="nav navbar-nav">
                    <li><a asp-area="" asp-controller="Home" asp-action="Index">Home</a></li>
                    <li><a asp-area="" asp-controller="LWBusRoute" asp-action="Index">Bus Routes</a></li>
                    <li><a asp-area="" asp-controller="LWRouteStop" asp-action="Index">Route Stops</a></li>
               </ul>
12. <!--TempData notification area #1c9046-->
<div style="background-color:lightseagreen; color:coral; height: 3em; padding-top:0.65em; border:3px dotted yellow; text-shadow: 1px 1px black; text-align:center; font-weight:800;">
@TempData["message"]
</div>
* Of course, this has to have a matching action in the Controller… *
12. style for <body > in site.css
body {
    padding-top: 50px; padding-bottom: 20px;  height: auto;  width: auto;  margin: 1em auto;  background-color:#222;  color: antiquewhite;
}
????  what are these styles for 
.center{
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
<a asp-controller="LWRouteStop" asp-action="Index" asp-route-BusRouteCode="@item.BusRouteCode" asp-route-RouteName="@item.RouteName">@Html.DisplayFor(modelItem => item.BusRouteCode)</a>
</td>
17. // change title // pass data  // change heading
@{
    ViewData["Title"] = "Route Stop Listing";

    ViewData["BusRouteCode"] = ViewBag.BusRouteCode;

    ViewData["RouteName"] = ViewBag.RouteName;
}
<!--heading shows route code and route name just passed above it-->
<h2>Stops for Route <span>@ViewData["BusRouteCode"]</span> - <span>@ViewData["RouteName"]</span></h2>

18.         public async Task<IActionResult> Index(int? BusRouteCode, string RouteName)
        {
            string busRouteCode = BusRouteCode.ToString();

            //Pull RouteName from BusRoute
            //How you can assign string value to a pulled value
            var routeName = _context.BusRoute
                .Include("RouteName")
                .Where(rn => rn.RouteName == RouteName)
                .Select(rn => rn.RouteName)
                .FirstOrDefault();

            //you can then pass that value to the View
            ViewBag.Message = "RouteName";

            if (BusRouteCode == null)
            {
                //return to View and Display Message if BusRouteCode is null
                TempData["message"] = "Please select a Bus Route...";
                return RedirectToAction("LWBusRoute", "Index");
            }

            // session, set session variables
            HttpContext.Session.SetString("RouteName", RouteName);
            HttpContext.Session.SetString("BusRouteCode", BusRouteCode.ToString());

            //add them to our ViewBag objects (you can't display ViewBag on Razor)
            ViewBag.RouteName = HttpContext.Session.GetString("RouteName");
            ViewBag.BusRouteCode = HttpContext.Session.GetString("BusRouteCode");

            //get all our stops that match the BusRouteCode passed in

            var RouteStopContext = _context.RouteStop
                .Include(r => r.BusRouteCodeNavigation)//RouteCodeId
                .Include(r => r.BusStopNumberNavigation)//stop locations from here
                .Where(r => r.BusRouteCode == BusRouteCode.ToString())//comparison check to passed in BusRouteCode               
                .OrderBy(r => r.OffsetMinutes)
                .ToListAsync();

            return View(await RouteStopContext);
            //public async Task<IActionResult> Index()
            //{
            //var busServiceContext = _context.RouteStop.Include(r => r.BusRouteCodeNavigation).Include(r => r.BusStopNumberNavigation);
            //return View(await busServiceContext.ToListAsync());
            }

19.                 // in startup.cs, 2 services & 1 app.UseSession below for session variable to persist, inside 2 different methods, easy to identify where to put . 
            services.AddDistributedMemoryCache();
            services.AddSession();     // before the last service in that method. 

app.UseSession();   // after Use.Authentication 



20. \routestops, how to show the page of the complete route stops page? 

21. // banners are in the solution folder/wwwroot/images, 1140x360
//change banner images names in home-index
// text on carousal images right below the name. Change accordingly.
// Microsoft links – the fat footer thing, delete and same some links and change link name to fit web content

