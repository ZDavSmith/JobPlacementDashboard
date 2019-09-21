# Live Project
This repository is a summary of my code for a live project I spent time on throughout the final two weeks of my coursework at The Tech Academy


### CalendarDatabase

One of the most challenging user stories I tackled was fixing some errors present in the following code:

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
        
   This is a section from the CalendarController which takes a list of schedule items from the Schedules table and converts them into a format for the site's CalendarEvents table. The code iterates through each schedule item and checks to see if the exact item already exists within the CalendarEvents table. If it does match, then the code checks each record's field to see if any changes have been edited in since the calendar was last rendered and adds in the change. If match = null, then a new record is created in the CalendarEvents table. To make sure that the start and end dates would not throw an error, I added @readonly attributes to the two text boxes to force the user to select a date using the jquery datepicker calendar. 

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
Another issue I handled was a bug that threw a java alert everytime a user attempted to input a date 


![ScreenShot](/READMEImages/CreateNewsItemsCalendar.png)
