# .net-pratical_7
Develop Feedback Management Module using MVC Layout, Partial View &amp; Validation. (HTML Helpers, Validation, Layouts) FeedbackController.cs 

using System; 
using System.Collections.Generic; using System.Linq; using System.Web.Mvc; using FeedbackManagement.Models; 
namespace FeedbackManagement.Controllers
{ public class FeedbackController : Controller
{ 
// Temporary in-memory storage for this practical private static readonly List<Feedback> feedbackList = 
new List<Feedback>(); private static int nextId = 1; 
public ActionResult Index() 
{ return View(feedbackList); 
} 
[HttpGet] 
public ActionResult Create() 
{ return View(new Feedback()); 
} 
[HttpPost] 
[ValidateAntiForgeryToken] 
public ActionResult Create(Feedback feedback)
{ if (ModelState.IsValid)
{ feedback.Id = nextId++;
feedback.SubmittedOn = DateTime.Now; feedbackList.Add(feedback); 
 
TempData["Message"] = 
"Thank you! Your feedback was submitted successfully."; 
return RedirectToAction("Index"); 
}
returnView(feedback); 
}
}
}
HomeController.cs 
using System; usingSystem.Collections.Generic; usingSystem.Linq; usingSystem.Web; usingSystem.Web.Mvc; namespaceFeedback_Management.Controllers {
publicclassHomeController : Controller
{ publicActionResult Index() { } returnView(); publicActionResult About() {
ViewBag.Message = "Your application description page.";
returnView(); }
publicActionResult Contact()
{
ViewBag.Message = "Your contact page.";
returnView(); 
}
}
}
Model-Feedback.cs 
using System; 
using System.ComponentModel.DataAnnotations; 
namespace FeedbackManagement.Models 
{ public class Feedback
{ public int Id { get; set; } 
[Required(ErrorMessage = "Please enter your name.")] 
[StringLength(50, ErrorMessage = "Name cannot exceed 50 characters.")]
[Display(Name = "Full Name")] public string Name { get; set; } 
[Required(ErrorMessage = "Please enter your email.")] [EmailAddress(ErrorMessage = "Enter a valid email address.")] public string Email { get; set; } 
[Required(ErrorMessage = "Please enter a subject.")] public string Subject { get; set; } 
[Required(ErrorMessage = "Please select a rating.")] 
[Range(1, 5, ErrorMessage = "Rating must be between 1 and 5.")] public int Rating { get; set; } 
[Required(ErrorMessage = "Please enter your comments.")] 
[StringLength(500, ErrorMessage = "Comments cannot exceed 500 characters.")] public string Comments { get; set; } 
public DateTime SubmittedOn { get; set; } 
} 
} 
View-feedback-_feeddbackForm.cshtml 
@model FeedbackManagement.Models.Feedback
<div class="form-group"> 
@Html.LabelFor(m => m.Name)
@Html.TextBoxFor(m => m.Name, new
{ 
@class = "form-control", placeholder = "Enter your full name" 
}) 
@Html.ValidationMessageFor(m => m.Name, "", new { @class = "text-danger" }) 
</div> 
<div class="form-group"> 
@Html.LabelFor(m => m.Email)
@Html.TextBoxFor(m => m.Email, new
{ 
@class = "form-control", type = "email", placeholder = "Enter your email" 
}) 
@Html.ValidationMessageFor(m => m.Email, "", new { @class = "text-danger" }) 
</div> 
<div class="form-group"> 
@Html.LabelFor(m => m.Subject)
@Html.TextBoxFor(m => m.Subject, new
{ 
@class = "form-control", placeholder = "Enter feedback subject" 
}) 
@Html.ValidationMessageFor(m => m.Subject, "", new { @class = "text-danger" }) 
</div> 
<div class="form-group"> 
@Html.LabelFor(m => m.Rating)
@Html.DropDownListFor(m => m.Rating, new SelectList(new[] { 
new { Value = 0, Text = "Select rating" }, new { Value = 1, Text = "1 - Poor" }, new { Value = 2, Text = "2 - Fair" }, new { Value = 3, Text = "3 - Good" }, new { Value = 4, Text = "4 - Very Good" }, new { Value = 5, Text = "5 - Excellent" } 
}, "Value", "Text"), new { @class = "form-control" }) 
@Html.ValidationMessageFor(m => m.Rating, "", new { @class = "text-danger" }) 
</div> 
<div class="form-group"> 
@Html.LabelFor(m => m.Comments)
@Html.TextAreaFor(m => m.Comments, 4, 50, new
{ 
@class = "form-control", 
 	placeholder = "Write your feedback here..." 
}) 
@Html.ValidationMessageFor(m => m.Comments, "", new { @class = "text-danger" }) 
</div> 
View-feedback-Create.cshtml 
@model FeedbackManagement.Models.Feedback
@{ 
ViewBag.Title = "Submit Feedback"; 
} 
<h2>Submit Your Feedback</h2>
<p class="text-muted"> 
Please share your experience with us. 
</p> 
<hr /> 
@using (Html.BeginForm("Create", "Feedback", 
FormMethod.Post, new { @class = "feedback-form" }))
{ @Html.AntiForgeryToken() @Html.ValidationSummary(false, "", new { @class = "text-danger" }) 
@Html.Partial("_FeedbackForm", Model)
<button type="submit" class="btn btn-primary"> Submit Feedback 
</button> 
<a href="@Url.Action("Index", "Feedback")" class="btn btn-default"> Cancel 
</a> 
} 
@section scripts { 
<script src="https://cdnjs.cloudflare.com/ajax/libs/jqueryvalidate/1.19.5/jquery.validate.min.js"></script> 
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery-validationunobtrusive/4.0.0/jquery.validate.unobtrusive.min.js"></script> 
} 
View-feedback-Index.cshtml 
@model IEnumerable<FeedbackManagement.Models.Feedback>
@{ 
ViewBag.Title = "Feedback Dashboard"; 
} 
<h2>Feedback Dashboard</h2>
@if (TempData["Message"] != null)
{ 
<div class="alert alert-success"> 
@TempData["Message"] 
</div> 
}
<p> 
@Html.ActionLink("Submit New Feedback", 
"Create", "Feedback", null, new { @class = "btn btn-primary" }) 
</p> 
@if (Model.Any())
{ 
<div class="table-responsive"> 
<table class="table table-bordered table-striped"> 
<thead> 
<tr> 
<th>Name</th> <th>Email</th>
<th>Subject</th>
<th>Rating</th>
<th>Comments</th>
<th>Submitted On</th> 
</tr> 
</thead>
<tbody> 
@foreach (var item in Model)
{ 
<tr> 
<td>@item.Name</td> <td>@item.Email</td>
<td>@item.Subject</td> 
 	<td>@item.Rating / 5</td> 
<td>@item.Comments</td>
<td>@item.SubmittedOn.ToString("dd-MM-yyyy HH:mm")</td> 
</tr> 
} 
</tbody> 
</table> 
</div> }
else
{ 
<div class="alert alert-info"> No feedback has been submitted yet. 
</div> 
} 
Home-about.cshtml
@{ 
ViewBag.Title = "About"; 
} 
<main aria-labelledby="title"> 
<h2 id="title">@ViewBag.Title.</h2>
<h3>@ViewBag.Message</h3> 
<p>Use this area to provide additional information.</p> 
</main> 
Home-Contact.cshtml
@{ 
ViewBag.Title = "Contact"; 
} 
<main aria-labelledby="title"> 
<h2 id="title">@ViewBag.Title.</h2>
<h3>@ViewBag.Message</h3> 
<address> 
One Microsoft Way<br />
Redmond, WA 98052-6399<br />
<abbr title="Phone">P:</abbr>
425.555.0100 
</address>
<address> 
<strong>Support:</strong> <a href="mailto:Support@example.com">Support@example.com</a><br /> 
<strong>Marketing:</strong> <a href="mailto:Marketing@example.com">Marketing@example.com</a> </address> 
</main> 
Home-Index.cshtml
@{
ViewBag.Title="HomePage"; 
}
<main>
<sectionclass="row"aria-labelledby="aspnetTitle"> 
<h1id="title">ASP.NET</h1> 
<pclass="lead">ASP.NET is a free web framework for building great Web sites and Web applications usingHTML,CSSandJavaScript.</p> 
<p><ahref="https://asp.net" class="btn btn-primary btn-md">Learn more &raquo;</a></p> </section>
<div class="row">
<sectionclass="col-md-4" aria-labelledby="gettingStartedTitle"> <h2id="gettingStartedTitle">Getting started</h2>
<p>
ASP.NETMVCgives you a powerful, patterns-based way to build dynamic websites that enablesacleanseparation of concerns and gives you full control over markup forenjoyable,agile development. 
</p>
<p><aclass="btnbtn-outline-dark" href="https://go.microsoft.com/fwlink/?LinkId=301865">Learn more &raquo;</a></p>
</section>
<sectionclass="col-md-4" aria-labelledby="librariesTitle"> 
<h2id="librariesTitle">Get more libraries</h2> 
<p>NuGetisafreeVisual Studio extension that makes it easy to add, remove, and update libraries andtoolsinVisualStudioprojects.</p> 
<p><aclass="btnbtn-outline-dark" href="https://go.microsoft.com/fwlink/?LinkId=301866">Learn more &raquo;</a></p>
</section>
<sectionclass="col-md-4" aria-labelledby="hostingTitle"> 
<h2id="hostingTitle">Web Hosting</h2> 
<p>Youcaneasilyfind a web hosting company that offers the right mix of features and price for your applications.</p>
<p><aclass="btnbtn-outline-dark" href="https://go.microsoft.com/fwlink/?LinkId=301867">Learn more &raquo;</a></p>
</section>
</div>
</main>
Shared-_Layout.cshtml 
<!DOCTYPE 	html>
<html> 
<head> 
<meta charset="utf-8" /> 
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>@ViewBag.Title - Feedback Management</title> 
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.4.1/dist/css/bootstrap.min.css" /> 
<style> body { 
background: #f4f7fb; font-family: Arial, sans-serif; 
} 
.navbar { border-radius: 0; 
} 
.main-container { max-width: 900px; margin: 35px auto; background: white; padding: 30px; border-radius: 10px; 
box-shadow: 0 2px 12px rgba(0,0,0,.08); 
} 
.text-danger { color: #c62828; 
} 
footer { text-align: center; padding: 20px; color: #666; 
} 
</style> 
</head>
<body> 
<nav class="navbar navbar-inverse"> 
<div class="container"> 
<div class="navbar-header"> 
 	<a class="navbar-brand" href="@Url.Action("Index", "Feedback")"> Feedback Management 
</a> 
</div> 
<ul class="nav navbar-nav navbar-right"> 
<li> 
<a href="@Url.Action("Index", "Feedback")">Home</a> 
</li>
<li> 
<a href="@Url.Action("Create", "Feedback")"> 
Submit Feedback 
</a> 
</li> 
</ul> 
</div> 
</nav> 
<div class="container main-container"> 
@RenderBody() 
</div>
<footer> 
&copy; @DateTime.Now.Year Feedback Management System 
</footer> 
<script src="https://code.jquery.com/jquery-3.7.1.min.js"></script> 
<script src="https://cdn.jsdelivr.net/npm/bootstrap@3.4.1/dist/js/bootstrap.min.js"></script> 
@RenderSection("scripts", required: false) 
</body> </html>
<img width="457" height="470" alt="image" src="https://github.com/user-attachments/assets/5d437303-38dd-4ad6-a5f5-ef7915d13877" />
