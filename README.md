# geo_courts

This repo is a home for an experiment in web scraping and using geopandas to plot locations on a map.

# Data source
Once I located a site that listed the Immigration Courts, their location, and  the judges presiding at each I used Beautiful Soup to scrape the content.  
First I grabbed all of the ‘a’ tags and took a look.  The Chief Immigrations Judges started at row 53 and went to 93.  I printed the list to make sure that I was pulling what I thought I was pulling.

# Grabbing what I needed
Then I wanted the individual courts and that information was in tabular form.  I used a comprehension to both search for the table and strip the text from the data all at the same time.

# Filtering the data
This resulted in a list made up of the text values of the table.  Each section of the table had a header of Court, Address, Judges, and Court Administrator so I got that and then the corresponding information next in that order and then again for the next court and so on for all the courts.  I didn’t need the text of the headers so I made a list of the terms I wanted to exclude and used a lambda filter to pull those terms out of the list and leave me with just the meat of the data. One of the courts was closed and so it didn’t have any info.  I deleted it so that I could group the information by 4s.

# Prep for Pandas
I made the list into a numpy array grouping each court into its own row of data.  I looped over the groupings of 4 elements and appended them to a list.  I read that numpy array into pandas and assigned the column names that I had pulled from the table headers.

# Cleaning it up
Now all the information is there but I had to clean up the text formatting so that I could extract the addresses and separate the phone numbers.  I used replace to pull out tabs (‘\t’) and replace them with spaces and then split the information in the Address column by the newlines(‘\n’) into their own columns of Street, City Zip, and Phone.

This was a little tricky because some courts had named buildings while most started with a street number.  Some also had room or suite designations. I used try/except statements to see if the first thing in the field was a a number or not to determine if it was a street address or a building name

I decided to attack the issue from the ‘back’ instead of the front and found a way to determine which element was the Zip Code by using a try/except loop forcing an int() function on the element.  If it “int()ed” without an error then I knew it was in the right place, if it didn’t then I knew I had to move over one more.

Then I split the judges names out and formatted them.

# Translating Location to Points
I used OpenStreet map to find a lat and long for each location by feeding the url the zipcode of each court, querying the latitude and longitude, and adding them to a list. Then I added those lists to the dataframe so that each court had their coordinates.

I used the latitude and longitude to create a Point series and added that to the dataframe in the form of a  geopandas geodataframe that could be then plotted on a map.

# Plotting the Map
I read in a .shp file of the US, cut out Alaska, and plotted the map and the data points on top.

###