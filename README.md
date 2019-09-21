# Live Project

## Introduction
This repository is a summary of my code for a live project I spent time on throughout the final two weeks of my coursework at The Tech Academy. The project was a management portal used for scheduling different people under different jobs under different managers utilizing the MVC model with the C# language and .NET frameworks. The project experience as a whole was a thorough exploration into the complexities of the coding universe, assembling my knowledge in HTML, CSS, JAVASCRIPT, SQL, and C# into one cohesive piece. Overall, I was able to greatly expand my problem-solving toolbox as a result of this project and equip myself with the knowledge I need to succeed as a developer. 

### CalendarDatabase

One of the most challenging user stories I tackled focused on backend error handling and editing which turned into the following code:

      [HttpGet]
        public void ShowScheduleItems()
        {
            var schedules = db.Schedules.ToList();
            foreach (var schedule in schedules)
            {
                var schEvent = new CalendarEvent();
                //convert schedule event properties into calendar event properties
                schEvent.Subject = Convert.ToString(schedule.Job.JobTitle);
                schEvent.Start = schedule.StartDate;
                schEvent.End = schedule.EndDate;
                schEvent.Description = "Employee: " + Convert.ToString(schedule.Person.DisplayName) + "  " + "\nJob Type: " + Convert.ToString(schedule.Job.JobType);
                if (schedule.EndDate != null)
                {
                    TimeSpan interval = schedule.EndDate.Value - schedule.StartDate;
                    if (interval.Days > 1)
                    {
                        schEvent.IsFullDay = false;
                    }
                    else
                    {
                        schEvent.IsFullDay = true;
                    }
                }
                else
                {
                    schEvent.IsFullDay = false;
                }
                schEvent.ScheduleId = Convert.ToString(schedule.ScheduleId);
                if (schEvent.End > DateTime.Now.Date && schEvent.Start < DateTime.Now.Date)
                {
                    schEvent.Start = DateTime.Now.AddMinutes(1);
                }
                try
                {
                    if (ModelState.IsValid)
                    {
                        //check if an event with a matching ScheduleId is currently on the calendar
                        var match = db.CalendarEvents.Where(a => a.ScheduleId == schEvent.ScheduleId).SingleOrDefault();
                        if (match != null)
                        {
                            var newSchEvent = match;
                            //check for differences in any properties between events with matching ScheduleId and update db
                            if (newSchEvent.Subject != schEvent.Subject)
                            {
                                schEvent.Subject = newSchEvent.Subject;
                            }
                            if (newSchEvent.Start != schEvent.Start)
                            {
                                schEvent.Start = newSchEvent.Start;
                            }
                            if (newSchEvent.End != schEvent.End)
                            {
                                schEvent.End = newSchEvent.End;
                            }
                            if (newSchEvent.Description != schEvent.Description)
                            {
                                schEvent.Description = newSchEvent.Description;
                            }
                            if (newSchEvent.IsFullDay != schEvent.IsFullDay)
                            {
                                schEvent.IsFullDay = newSchEvent.IsFullDay;
                            }
                            //update database entry
                            db.Entry(newSchEvent).State = EntityState.Modified;
                            db.SaveChanges();
                        }
                        else //add new event
                        {
                            db.CalendarEvents.Add(schEvent);
                            db.SaveChanges();
                        }
                    }
            }
                catch
            {
                throw new ArgumentException("Event cannot end before today's date.");
            }
        }
        }
        
This is a section from the CalendarController which takes a list of schedule items from the Schedules table and converts them into a format for the site's CalendarEvents table. The code iterates through each schedule item and checks to see if the Schedule ID already exists within the CalendarEvents table. If it does match, then the code checks each record's field to see if any changes have been edited in since the calendar was last rendered and adds in that change. If match = null, then a new record is created in the CalendarEvents table with the schedule items information. Further, to make sure that the start and end dates would not throw an error from user input, I added @readonly attributes to the two text boxes to force the user to select a date using the jquery datepicker calendar. 

              <div class="form-group">
            @Html.LabelFor(model => model.EndDate, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.EndDate, new { htmlAttributes = new { @class = "form-control datepicker", id = $"secondDateCheck_", @readonly = "readonly" } })
                @Html.ValidationMessageFor(model => model.EndDate, "", new { @class = "text-danger" })
            </div>
        </div>

        <div class="form-group">
            <div class="col-md-offset-2 col-md-10 createBtn">
                <input type="submit" value="Create" class="btn btn-default" id="invalidCheck"/>
            </div>
        </div>
    </div>
    }
   
### CompanyNews
   Another issue I handled was a bug which threw a java alert everytime a user attempted to input an expiration date in the past, yet still allowed the user to save the invalid date. I added a jquery function to prevent the user from selecting either the current date or any other date in the past from the calendar on top of an @Readonly attribute. 


      @section Scripts {
          @Scripts.Render("~/bundles/jqueryval")
          @Scripts.Render("~/bundles/jqueryui")

    <script type="text/javascript">

        $("#expDate_").datepicker({
            defaultDate: "+1d",
            changeMonth: false,
            numberOfMonths: 1,
            minDate: "+1d"
        });
    </script>
    }
    
    
   ![ScreenShot](/READMEImages/CreateNewsItemsCalendar.png)


### Container Agreement
When I was first assigned to this project, most of the views for the project had already been created. However, many of the tables and other bootstrap elements failed to have either a consistent default container, or any container at all. To fix this, I added a new style of container within the Site.CSS folder and renamed a few of the styling elements to contain the word "container" for future clarity in editing the code. A few examples are below:


            @using ManagementPortal.Common
            @using ManagementPortal.Models

            @model CompanyNews

            @{
                ViewBag.Title = "Details";
                Layout = "~/Views/Shared/_Layout.cshtml";
            }

            <h2>News Item Details</h2>

            <div class="defaultContainer">
                <hr />
                <dl class="dl-horizontal">
                    <dt>
                        @Html.DisplayNameFor(model => model.Title)
                    </dt>

                    <dd>
                        @Html.DisplayFor(model => model.Title)
                    </dd>

                    <dt>
                        @Html.DisplayNameFor(model => model.NewsItem)
                    </dt>

                    <dd>
                        @Html.DisplayFor(model => model.NewsItem)
                    </dd>

                    <dt>
                        @Html.DisplayNameFor(model => model.ExpirationDate)
                    </dt>

                    <dd>
                        @Html.DisplayFor(model => model.ExpirationDate)
                    </dd>
                </dl>
            </div>

            <p>
                @Html.Partial(AnchorButtonGroupHelper.PartialView, AnchorButtonGroupHelper.GetEditDeleteBack(Model.NewsId.ToString()))
            </p>

![ScreenShot](/READMEImages/News.png)


                        /* Default Container Style*/
      .defaultContainer {
          background-color: rgba(255, 255, 255,0.8);
          padding: 10px;
          width: auto;
      }
      
       .formContainer {
          background-color: rgba(201,173,167, 0.82);
          padding: 20px;
          border-radius: 10px;
          color: #000;
          min-width: 265px;
      }

## Other Skills
