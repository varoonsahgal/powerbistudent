Part 2 starts here:

Instructor: Being able to update, modify, 
and change your data source settings 
for individual connections is great to understand 
and extremely valuable. 
But what if you had, say, like a database connection 
that contained multiple schemas, 
like one for development, test, 
user acceptance, and production, 
and you wanted to easily toggle back 
and forth between the different schemas 
without updating each table connection or path individually? 
Or better yet, what if you developed a report 
and wanted to allow other team members 
to dynamically change the database schemas? 
Well, that's where dynamic data source parameters come 
into play because they allow you to dynamically manage 
and update connection paths within the query editor. 
And parameters can be found within the home tab 
just to the left of the data source settings, 
and they allow you to do things like manage, edit, 
and create new parameters. 
So to get started, you'll need to create a new parameter 
or parameters based on your connection. 
And in this example, I've created two new parameters, 
one for the MySQL server connection 
and another for the database schemas. 
Here I'm using generic names like database 
and then specifying that it's part 
of the Maven Fuzzy Factory, 
just good kind of data hygiene here 
and making sure that you're naming things clearly 
and appropriately. 
Our next input here that we need to set up 
is the parameter input type, 
and this can be set to something like any value 
or text or date, decimal number, et cetera. 
There's a bunch of different options here for the types. 
After that is set, 
the suggested value option can then be configured. 
The default setting here is for any value, 
but you can also create a list 
of values like we're showing here or create a query. 
Now, creating a list of values is a pretty powerful way 
to easily switch between different databases, 
and it really only requires a little bit of setup, 
which again I'll be walking through 
in much more detail shortly. 
Now, the last pieces of the parameter 
to configure are the default and current value settings. 
And when you're setting these up, 
pay attention to any sort of business logic or rules 
and make sure that you're adhering 
to your organization's best practices. 
And depending on the suggested value you choose, 
you may or may not have both of these options available. 
So for example, you may only have the ability 
to set a current value, 
but the idea here is that you can set the same 
or different values. 
So once the parameter is configured, 
we need to update the data source connection credentials 
from a text-based field to the parameter that we created. 
And then after that, we'll be able to check the M code 
of our query connections and see that it's updated. 
So let's head over to Power BI, 
and I'll walk you through this. 
All right, so for this demo, 
we're gonna be continuing to use 
that Maven Fuzzy Factory dataset, 
right, that's up here in all of our demo queries. 
And just to make you aware, 
I've actually amended this a little bit, 
and let me jump over quickly to the MySQL Workbench view 
so you can see 
that I've got two different schemas set up here. 
All right, so we're in MySQL Workbench, 
and you can see that we have one schema 
for development with all our tables 
and another one for production. 
So we've got both of these environments set up. 
Let's head back to Power BI, and we'll start working 
through getting these parameters configured. 
All right, so what we wanna do is add a parameter 
to the source step here, right, 
so it dynamically updates, right? 
And if I click into the source step here 
and click the expand button 
for the M code here for the formula bar, 
you can see that we have 
our MySQL database connection, right? 
And the server is set as text, 
and then our schema is also set as text here. 
But these values are hard-coded in. 
So what we don't want to do is go through 
and like manually update these 
for each of these connections. 
We wanna do this more dynamically, right? 
So what we need to do is head 
into the data source options here, 
and we wanna parameterize both of these, 
the server and the database name. 
But if you remember back to the slides, 
we had this little box to the left here. 
We don't have that right now. 
So what we need to do is actually enable something else 
within the query editor. 
So if we head up to our view menu, 
and you have this option here 
for parameters that says always allow, 
so when I click that, if I head back to the source step, 
we now have this dropdown 
where we can choose a text-based value, a parameter, 
or we can actually create a new parameter 
directly from here. 
So this is awesome. 
What we wanna do is create parameters 
for the entire MySQL connection, 
not at the individual table level. 
So let's get out of this dialogue box, 
and we'll head back to our home menu. 
And then from managed parameters, 
we wanna create a new parameter 
that we can then apply to our entire MySQL connection. 
All right, so manage parameters dialogue box opened up. 
And you can see here 
that we already have an existing parameter here, right? 
That parameter was actually created 
as part of this transform file from sales data. 
So we're not gonna mess with that. 
If we were to edit any of this, 
it would mess up that connection. 
So we're gonna continue to work 
with this Parameter2 here. 
And just like in the slides, we're gonna choose a new name. 
So this is gonna be Server, 
and then I'm gonna name this Fuzzy Factory, 
again just so we have a better idea of what it's for. 
Type, you can see that we have a bunch 
of different types here. 
So we could allow for any decimal, date, time, 
you know, whatever. 
We'll choose this as text. 
Suggested values, we can choose any value, a list of values. 
You know, we can create a query here. 
Because we're connecting to a single database, 
I'm not gonna choose or create a list here, 
but if you had multiple different database connections 
that you needed to connect to, 
you could create a list here as well. 
So we'll do our 127.0.0.1 
from my local machine. 
So what that does is that creates this brand-new parameter, 
and it adds it to our list of queries. 
Now, again, we can follow some organization stuff. 
I'm gonna drag this up into our demo queries. 
And you can see that we have this parameter here 
with a current value. 
What's great is that if we ever wanted to update 
or change this, just click on manage parameter, 
and this brings you back into that parameter dialogue box. 
All right, so you can update, adjust, 
or configure any new settings that you need to. 
So now that we've got this first parameter set up, 
I wanna start updating these queries. 
So if I come up to my data source settings, 
just like we did in the last lecture, 
and I select my MySQL connection and then Change Source, 
I can now update my server from this text value 
to the parameter that we just created, right? 
So we can see that we have our server for Fuzzy Factory. 
For the database, if I were to choose parameter, 
because we haven't created 
a specific database parameter yet, 
all I have available is the Fuzzy Factory one, right? 
So what I can actually do right 
from this window is create a new parameter, 
and that's gonna bring me back 
to this manage-parameters dialogue. 
So we'll create a new name here for database 
and then Fuzzy Factory. 
Type, I'm gonna choose this one as text. 
And then instead of any value here, 
I wanna actually create a list of values. 
And this list of values is gonna be based 
on the different schema names 
that are within my MySQL database. 
So we have mavenfuzzyfactory_development. 
We'll add another record here 
and then mavenfuzzyfactory_production. 
Right, so these are the two values 
that I wanna be able to switch between. 
And then like I was mentioning 
at the beginning of this lecture, 
the default and current values, 
again, make sure that these are set 
in a appropriate way that aligns 
with either the business logic or rules 
or your organization's best practices. 
So here, I'm gonna set both of these to development, 
and we'll click OK. 
Now, when we click into this database option, 
we can see we have both server and the database option. 
So that's great. 
I'm gonna click OK here and then Close. 
Again we'll follow our organization best practices 
and drag that query in there. 
So now when I head into one of my individual queries, 
and I click into my source step, look what happened. 
Instead of having that hard-coded server string 
and the database string, I now have my parameters, right, 
my server and my database parameter. 
And if I go to each one of these queries, 
it's the same thing. 
All of these have been dynamically updated 
with the server and database parameters 
instead of hard-coding in those values. 
So this is awesome. 
Now, the real power of this is also being able 
to change between those development 
and production or, you know, test environments. 
So what you can do is come down here to the database, 
and you see for the current value here, 
we actually have a dropdown list, 
and it's currently selected on development. 
If we update to production, 
oh, so this is actually throwing an error for us, right? 
What I was expecting to happen was that this updates 
to the production database, 
but let's go dig into this error. 
So what the query editor is telling us here 
is that it is still looking 
for the mavenfuzzyfactory_development schema 
in this navigation step. 
And you see when we come up here, 
this is still hard-coded in. 
So again, we can quickly kind of go through 
and replace this value with our parameter value. 
So I'm gonna come back up to my source step here, 
and I am going to select everything 
from the pound or the hash, copy that value, 
come back to the navigation step. 
And then from here, I'm gonna replace everything 
after the schema equal sign and enter, right? 
And that updates our table. 
So I'm gonna go through each one of these steps 
and quickly update this. 
And again, one of the things if you're ever doing this 
or if you're able to follow along with this demo at all 
is make sure that you don't delete an extra comma. 
That'll throw some errors there, 
and it may be kind of nondescript, 
or you might not be quite sure what you're doing. 
So just be careful when you're kind of editing the M code 
in the query editor. 
Like we talked about, it's case-sensitive, 
and again, all of the commas and parentheses 
and all that stuff really are critical 
to making sure that the code runs appropriately. 
All right, so we've got two more here, 
then last one. 
So there we go. 
We've successfully updated all 
of the navigation steps for these schemas. 
And now when we go into our database schema 
and update this from production to development, 
we don't see those same errors there, right? 
So this is great. 
Nothing breaks. 
All right, so let's take this one step further, 
and I'd like to further prove 
to you how easy it is to change 
between these two different schemas. 
So what we're gonna do is we're actually gonna enable load 
for all of these tables. 
And once this is all set, 
we're gonna actually load these tables into the data model. 
So we'll click close and apply here in a moment. 
Right, and Power BI is working through adding all 
of those Maven Fuzzy Factory tables 
into the front end here into our data model, right? 
And pay attention here. 
We've got mavenfuzzyfactory_development, right? 
And we can see that 
for all of these tables that are being loaded, 
they're coming from that development schema. 
And then like we've seen with all of our other tables, 
they pop up over here on the right-hand side 
within the data view, 
same thing here within the report view, right? 
We've got all of our different tables here. 
So what we can actually do is from the transform data menu, 
so far we've only used this menu to go back and forth 
between the query editor and the front end of Power BI. 
But we have some other options here, right? 
So if we click transform data, 
that's gonna bring us right into the query editor. 
We can also access our data source settings 
and edit parameters or variables if we had them configured. 
So let's click on edit parameters here, 
and you can see that our database is set to the development. 
If we change this to production 
and then apply those changes, 
all right, watch what happens here, 
mavenfuzzyfactory_production. 
Right, so Power BI is going through, 
and it's reloading all of the table data, 
but it's swapping that schema 
from development to production. 
And because both of those schemas are set up the same, 
any sort of visuals or measures 
or anything that you had created that are based 
on those tables would automatically update. 
So again, just super, super powerful stuff, 
and it really makes things easy to be able to change 
between different environments very quickly. 
All right, so I'm gonna keep these parameters configured, 
but I'm gonna disable the MySQL queries from loading 
so they don't needlessly clutter up the data model. 
And with that, that's your crash course on creating 
and using data source parameters in Power BI. 
 
 
 
3:16 / 3:20 
Instructor: Next up, I wanna take just a second 
to talk about refreshing queries. 
Now, in the home tab of your Power BI file, 
outside of the Query Editor, 
you'll see this refresh button across all of your views. 
And by default, when you press this button, 
it's going to refresh every single query, every connection, 
every table, that exists in your model by default. 
But you can customize those refresh options. 
So within the Query Editor and the query's pane, 
you can actually right-click 
any one of the individual connections or tables, 
and you can simply deselect or uncheck the box 
that says include and report refresh, right? 
This is located just below the Enable Load button 
that we've been clicking on and off in the previous lessons. 
As you might expect, this prevents that file from refreshing 
whenever you hit the refresh command from the Home tab. 
So helpful pro tip here, 
what you can do is exclude all of your queries 
that don't change often or maybe don't change at all, 
like your lookup tables 
or any static data tables that you might have. 
So let's go ahead and we'll open up the Query Editor 
and we'll make some of these adjustments ourselves. 
All right, so here I am back in the Query Editor. 
And again, I'm gonna collapse these demo queries. 
And for our other queries here, 
you can see we've got our territory lookup, 
product lookup, subcategory, customer, 
calendar lookup, our rolling calendar, right? 
We've got a whole bunch of tables here. 
And if I right-click into these lookups, 
you can see we have include in report refresh, 
and I can scroll down on each of these 
and they're all selected 
to be included within the report refresh. 
In fact, if I go back to my data model view 
and I refresh all of my queries here, 
you'll see that we have all of our tables, our lookups, 
right, customer, the rolling calendar, sales data, 
everything is refreshing when I click Refresh, 
even though nothing has changed with that underlying data. 
So there you go, we've just refreshed all of these tables. 
Right, and now let's head back into the Query Editor. 
And what I wanna actually do here 
is prevent my lookup tables in that rolling calendar table 
from being included within the report refresh. 
So we'll come into these lookup tables one by one, 
include in report refresh. 
If I right-click on this again, 
you can see it's now deselected. 
Come to the product lookup, 
subcategory, 
categories next, 
customer, 
rolling calendar, and we'll select our calendar as well. 
So basically all of these lookup tables 
in our calendar table, 
because they're not going to be changing, 
we have removed them from being refreshed, right? 
The only table here is our sales data table. 
So if we head back over into the data model view 
and now we click Refresh, 
you'll notice that it's only that sales table 
that's being updated, 
right, and that was so much shorter, right? 
And that's because now instead of refreshing 
all of our lookup tables, it's just that sales data table. 
Good rule of thumb, if you have data that's static, 
or if you have tables that aren't going to be changing, 
go ahead and exclude those from the report refresh 
and only refresh the ones that may be changing over time. 
 

 
 
5:53 / 5:57 
Instructor: All right, 
so we've got another pro-tip lecture here 
and this time, we're gonna talk 
about importing entire models directly from Excel. 
So in Power BI, we've got this import menu 
that we really haven't talked about yet 
and you have this option to import Excel workbook contents. 
And note that this is different 
from using the get data option 
and pointing to an Excel workbook. 
What this is doing is actually importing 
additional information about the entire model 
from Excel into Power BI. 
So what's incredibly helpful about this 
is that this process preserves 
just about all of the information about your model. 
It transitions information about the data source connections 
and queries that are in place, all the file paths, 
it maintains the query editing procedures, 
all of the applied steps, the data modeling details, 
like the relationships and hierarchies 
and field settings and formats and et cetera, et cetera. 
And then last but not least, all of your calculated columns 
and DAX measures are also imported 
from your Excel model into Power BI. 
So quick pro tip here, 
if you're more comfortable building models 
in the Excel environment, which is covered 
in Maven's Power Query, Power Pivot, and DAX course, 
then go ahead and continue to build there 
within that environment and then just import those models 
into Power BI for the reporting and visualization phase. 
You'll end up in the exact same place. 
Honestly, it's really just which environment 
you're more comfortable in. 
All right, so sit back, 
relax for a minute and just watch this demo. 
I'm gonna show you what it looks like 
when you actually import a full data model 
from Excel into Power BI. 
So this is the FoodMartDataModelCOMPLETE file 
from the Power Query, Power Pivot, and DAX course. 
And basically here, we've got a fully built data model 
with a whole bunch of tables here, 
lookup tables and data tables. 
We can jump over into the diagram view here. 
That'll show us all of our relationships, 
our table relationships in one place, right? 
We've got all these lookups. 
You can see that we've got our parameters 
and our disconnected tables as well. 
So again, it's a pretty robust model here. 
And then on top of that, we've also got all 
of our measures that we have created. 
And this would be a huge, huge headache 
if we had to recreate all of these different measures 
from scratch in Power BI. 
And luckily with this functionality, 
we won't have to do that. 
Now, one thing to note here, as part of this workbook, 
we've added all sorts of pivot tables with things 
like conditional formatting and data bars and icons. 
And there's really no equivalent Power Pivot view 
quite like this in Power BI. 
There's a matrix visual that's similar 
but not quite the same. 
So all of these views, the actual pivot tables, 
these additional tabs down here, none of that stuff 
will actually transfer over. 
So I just wanna make sure that you're clear on that first. 
All right, so from here, I'm gonna go and open up 
a brand new Power BI file 
so that we can import this model directly into it. 
All right, so I've launched my brand new workbook, 
and I'm just gonna close out of this menu here. 
And again, we are not getting data from the Get Data menu. 
We actually wanna come up to File here 
and we are going to import data 
from Power Query, Power Pivot, or Power View. 
And here is my FoodMartDataModelCOMPLETE file 
and click on this, Excel workbook contents. 
It says we don't directly work with Excel workbook contents 
but we know how to extract the useful content 
so that you can work with it in Power BI Desktop. 
And again, that's great, right? 
We don't necessarily need all 
of those pivot table views and everything like that. 
We really just wanna get all 
of the modeling in DAX and calculated columns 
and all of that stuff imported into Power BI. 
So click Start. 
Oh, and we get an error message. 
Migration failed. 
It's already open in another program. 
So let's head back over and we'll close out of Excel first. 
All right, so we'll just close out of this 
and let's hit Retry. 
All right, there we go. 
So now it says, okay, 
we've got some tables that exist in that original workbook. 
In other words, 
they were created using actual cell ranges in Excel. 
And it's just saying, hey, do you wanna copy the data 
or keep the connection between Power BI and Excel? 
And because I just wanna import everything 
into Power BI, you know, we're gonna copy the data instead 
of trying to keep some sort of connection open. 
So Power BI's running through all of their steps 
in order to import that fully built model into Power BI. 
And that went pretty quickly, right? 
And check it out. 
It's migrated all of these different tables over. 
We've got these data model connections and items, 
KPIs and measures. 
We've got 48 here, a pretty exhaustive list here 
of everything that was imported over 
into our data model. 
Back in our report view, 
we can see that we've got all 
of the tables from the model that were added. 
Jump over into our data view. 
Same thing here, like calendar table, right? 
We've got all of our different months and calculations. 
Same thing. 
Our relationship view or our model view here. 
We'll scroll over a little bit. 
Can collapse these panes so we can see it. 
Again, it's imported our data model as well. 
No, it's a little bit messy. 
We can go through and reorganize things 
but the point here is that it's imported all 
of the tables and all of our calculations. 
If I head back over here 
and we start looking at these measures, 
six-month rolling profit, click in. 
We've got all of those great DAX measures that we created 
in Excel right here in Power BI. 
So some of these details 
aren't really gonna make sense quite yet 
until we dive into the data modeling 
and DAX sections of this course. 
But again, I just wanna give you a little preview here 
and let you know 
that this is an option to be able 
to pull a fully baked model from Excel right into Power BI. 
 

 
 
0:03 / 1:54 
Tutor: All right, everyone, 
time to take this section across the finish line 
with some best practices for connecting and shaping data. 
Now, number one, get yourself organized 
before even loading any data into Power BI. 
I've said it before and I'll say it again, 
defining those clear and intuitive table names 
right from the start 
will save you a ton of time in the future. 
Updating table names can be a big headache down the road, 
especially if you've already referenced them 
in multiple places like DAX Measures. 
Also, I highly recommend 
establishing a clear and organized file folder structure 
that makes sense from the start. 
This will help you avoid having to go in and modify 
your data source settings 
anytime those file names or locations change. 
Number two, I'd recommend disabling report refresh 
for any static sources in your model. 
And at the end of the day, there's no need to constantly 
refresh those sources that don't update frequently 
or in some cases, at all, 
like lookups and static data tables. 
So you only really need to enable refresh 
for tables that you expect to change over time. 
And last but not least, 
when you're working with larger tables or models, 
only load up the data that you need. 
So there's no point 
in including hourly data when you only need daily, 
or product-level sales and transactions 
when you only care about performance at a store level. 
So just keep in mind, 
this can all change over time, 
nothing's set in stone, 
but at the end of the day, 
extra data is only gonna slow you down. 
All right, so congratulations. 
We've officially wrapped up our entire section 
on connecting and shaping data, 
which is the first major phase 
of the business intelligence workflow. 
Keep in mind that we're really just getting started 
and we've got some great stuff ahead. 
Next up, we'll shift gears 
into creating table relationships, 
exploring the data model view, 
and really customizing our data model within Power BI. 
 

 
 
