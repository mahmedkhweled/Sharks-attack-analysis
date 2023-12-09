# Sharks-attack-analysis
## Sharks attack from 1980 to 2017:
### Introduction:
Shark attacks, though relatively rare, captivate our imaginations and fuel fears about the dangers lurking beneath the waves. These incidents, often sensationalized in the media, prompt questions about why sharks might attack humans. In this brief exploration, we will unravel the basics of shark attacks, understanding the factors that contribute to these encounters and the measures taken to address the risks associated with sharing the ocean with these powerful predators. This project analyses data on shark attacks from around the world to see if there are any patterns in where and when shark attacks occur.
### Data Collection:
For this analysis, comprehensive data on shark attacks was sourced from ‘Kaggle’ link. The dataset spans a period of 37 years, from 1980 to 2017, capturing incidents worldwide. This dataset contains 13 columns and 2911 rows after data cleaning.
#### Key Variables:
 **The dataset contains several key variables, including:**
**<br>• Case Number:** A unique identifier for each shark attack incident.
**<br>• Date:** The date of the incident.
**<br>•	Year:** The year of the incident.
**<br>•	Type:** The cause of the attack.
**<br>•	Country:** The country where the incident occurred.
**<br>•	Area:** The specific area or region within the country.
**<br>•	Location:** The specific location within the area.
**<br>•	Activity:** The activity the victim was engaged in at the time of the attack.
**<br>•	Name:** The name of the victim.
**<br>•	Sex:** The gender of the victim.
**<br>•	Age:** age of the victim.
**<br>•	Injury:** Description of the injuries sustained.
**<br>•	Fatal (Y/N):** Indicates whether the attack resulted in a fatality.
**<br>•	Time:** The time of day when the incident occurred.
**<br>•	Species:** The species of shark involved in the attack.
**<br>•	Investigator or source:** The one who investigated or published the case.
**<br>•	Pdf:** The pdf document that contain the case
**<br>•	Href formula:** The formula used to generate hyperlinks to the PDF documents associated with each incident. 
**<br>•	Href:** Direct hyperlink to the associated PDF document. 
**<br>•	Case number 2:** A unique identifier for each shark attack incident.
**<br>•	Case number 3:** A unique identifier for each shark attack incident.
**<br>•	Original order:** The original order of the incident in the dataset.

<br>-We will start by removing several un-needed columns, looking at ‘Case number’, ‘Name’, ‘Investigator or source’, ’Pdf’, ‘Href formula’, ’Case number 2’, ‘Case number 3’, ’Original order’.

<br>-After close examination, it has become that a massive portion of our dataset contains a noticeable amount of missing data across various columns. As our dataset is small, I have made the decision to review and revise the URLs within the ‘href’ column to fill in the missing data manually. Despite the time and effort required, undertaking this task is essential to ensure and preserve the integrity and accuracy of our dataset.

<br>-‘Age’ and ‘Time’ columns are missing info in the documents as the investigator didn’t concern to gather this information.so I will leave it as it is.
### Goals and objectives:
-This phase marks the beginning of our data analysis journey, where we define the project's scope, set overarching goals, and establish specific objectives to guide our analysis.

<br>1-Create a graph showing the annual number of shark attacks and Which trends are you noticing and which months had the highest cases?

<br>2-Why do sharks attack people?

<br>3-Which countries record the highest number of shark attacks?

<br>4-What are the most attacked activities?

<br>5-How many attacks are fatal and what is the gender ratio?

<br>6-Which shark species is most frequently attacking people? 

<br>7- Which body part sustains the most injuries?

<br>8-How many not injured cases?

### Tools used:
#### Microsoft Excel:
<br>-Power Query.

<br>-Pivot tables.

<br>-Pivot charts.

<br>-‘IF’ function.

<br>-'SUMPRODUCT', 'LEN' and 'SUBSTITUTE' Functions.
### Data Cleaning and Preprocessing:
-The provided Power Query script is designed to transform and clean a dataset related to shark attacks.
#### Power Query Advanced Editor:
<pre>
```
  let
    // The data is sourced from the Excel workbook, specifically from the table named "Table1."
    
    Source = Excel.CurrentWorkbook(){[Name="Table1"]}[Content],

    // Certain columns ("Case Number," "Name," "Investigator or Source," "pdf," "href formula," "Case Number2," "Case Number3," "original order") are removed to focus on relevant information.

    #"Removed Columns" = Table.RemoveColumns(Source,{"Case Number", "Name", "Investigator or Source", "pdf", "href formula", "Case Number2", "Case Number3", "original order"}),
    
    // Ensures unique records by removing duplicate rows based on selected columns.

    #"Removed Duplicates" = Table.Distinct(#"Removed Columns", {"Date", "Year", "Type", "Country", "Area", "Location", "Activity", "Sex ", "Age", "Injury", "Fatal (Y/N)", "Time", "Species "}),


    // The data type of the "Date" column is transformed to text for subsequent modifications.

    #"Changed Type" = Table.TransformColumnTypes(#"Removed Columns",{{"Date", type text}}),

    // A custom column named "Custom" is created by splitting the "Date" column using the text "Reported " as a delimiter.

    #"Added Custom Column" = Table.AddColumn(#"Changed Type", "Custom", each let splitDate = Splitter.SplitTextByDelimiter("Reported ", QuoteStyle.None)([Date]) in Text.Combine(splitDate), type text),

    // The columns are reordered for better readability and analysis.

    #"Reordered Columns" = Table.ReorderColumns(#"Added Custom Column",{ "Date", "Custom", "Year", "Type", "Country", "Area", "Location", "Activity","Sex ", "Age", "Injury", "Fatal (Y/N)", "Time", "Species ", "href"}),

    // The data type of the new "Custom" column is transformed to datetime.

    #"Changed Type1" = Table.TransformColumnTypes(#"Reordered Columns",{{"Custom", type datetime}}),

    // The data type of the "Custom" column is further transformed to date.

    #"Changed Type2" = Table.TransformColumnTypes(#"Changed Type1",{{"Custom", type date}}),

    // Unnecessary columns ("Date" and "href") are removed, keeping the cleaned "Custom" column as the new "Date."

    #"Removed Columns1" = Table.RemoveColumns(#"Changed Type2",{"Date","href"}),

    // The "Custom" column is renamed to "Date" for clarity.

    #"Renamed Columns" = Table.RenameColumns(#"Removed Columns1",{{"Custom", "Date"}}),

    // A new column named "Fatal" is created based on conditions in the "Fatal (Y/N)" column.

    #"Added Conditional Column" = Table.AddColumn(#"Renamed Columns", "Fatal", each if [#"Fatal (Y/N)"] = "N" then "No" else if [#"Fatal (Y/N)"] = "Y" then "Yes" else null, type text),

    // A new column named "Species" is created by extracting text before the comma in the "Species " column.

    #"Inserted Text Before Delimiter" = Table.AddColumn(#"Added Conditional Column", "Species", each Text.BeforeDelimiter([#"Species "], ","), type text),

    // The "Species " column is further modified based on multiple conditional statements to address variations in text.

    #"Added Conditional Column1" = Table.AddColumn(#"Inserted Text Before Delimiter", "Species  ", each if Text.Contains([#"Species "], "Shark involvement") then "Shark involvement not confirmed" else if Text.Contains([#"Species "], "Shark invovlement not cofirmed") 
    then "Shark involvement not confirmed" else if Text.Contains([#"Species "], "Shark involvement not comfirmed") then "Shark involvement not confirmed" else if Text.Contains([Species], "Shark involvement prior to death not confirmed")
     then "Shark involvement not confirmed" else if Text.Contains([Species], "shark involvement not confirmed") then "Shark involvement not confirmed" else if Text.Contains([Species], "Shark invovlement not confirmed") then "Shark involvement not confirmed" else if Text.Contains([Species], "shark invo")
      then "Shark involvement not confirmed" else [Species]),

    // Leading and trailing whitespaces are removed from selected text columns.

    #"Trimmed Text" = Table.TransformColumns(#"Added Conditional Column1",{{"Activity", Text.Trim, type text}, {"Injury", Text.Trim, type text}, {"Species ", Text.Trim, type text}, {"Species", Text.Trim, type text}, {"Fatal", Text.Trim, type text}}),

    // Unnecessary columns ("Fatal (Y/N)," "Species ", "Species") are removed.

    #"Removed Columns2" = Table.RemoveColumns(#"Trimmed Text",{"Fatal (Y/N)", "Species ", "Species"}),

    // The columns are reordered again for final presentation.

    #"Reordered Columns1" = Table.ReorderColumns(#"Removed Columns2",{"Date", "Year", "Type", "Country", "Area", "Location", "Activity", "Sex ", "Age", "Injury", "Fatal", "Species  ", "Time"}),
    
    // The "Date" column is transformed to date type.

    #"Changed Type3" = Table.TransformColumnTypes(#"Reordered Columns1",{{"Date", type date }}),

    // The activity "Boogie boarding" is replaced with "Bodyboarding" for consistency.

    #"Replaced Value" = Table.ReplaceValue(#"Changed Type3","Boogie boarding","Bodyboarding",Replacer.ReplaceText,{"Activity"}) ,

    // Any errors in the "Date" column are replaced with a default date value.

    #"Replaced Errors" = Table.ReplaceErrorValues(#"Replaced Value", {{"Date", #date(1900, 1, 1)}}),

    // The occurrence of "REUNION" in the "Country" column is replaced with "REUNION ISLAND" for uniformity.

    #"Replaced Value1" = Table.ReplaceValue(#"Replaced Errors","REUNION","REUNION ISLAND",Replacer.ReplaceText,{"Country"})
in
    #"Replaced Value1"
```
</pre>

<br>-The resulting dataset is cleaned, organized, and ready for further analysis, with special attention to date-related transformations.
![image](https://github.com/mahmedkhweled/Sharks-attack-analysis/assets/67151245/32ec5c57-b4cb-456b-8c66-8e27419b153b)

### Exploratory Data Analysis (EDA):
### Key Findings:
#### 1-Create a graph showing the annual number of shark attacks. Which trends are you noticing and which months had the highest cases?

<br>-The dataset spans 38 years, from 1980 to 2017.Notable peaks in the number of cases are observed in the years 2000, 2005, and 2015.

<br>-The third quarter (Qtr3) consistently shows higher case counts which start with the last month of the second quarter (Qtr2) June as In the summer season, the water becomes a focal point for more people than any exceeding attendance in any other season, with July being the highest among the months

![image](https://github.com/mahmedkhweled/Sharks-attack-analysis/assets/67151245/58ce1a37-ed35-4590-acc8-e4132a6df3e9)

#### 2-Why do sharks attack people?
<br>-Shark attacks can either be provoked or unprovoked by humans. Which we will focus on these two types only. 

<br>-The majority of sharks attack (79.59%) are unprovoked, suggesting that most incidents occur without explicit provocation from humans. sharks may occasionally mistake humans for their natural prey, such as seals or sea lions. This misidentification can occur due to factors like visual similarities or the way humans move in the water, particularly when engaging in activities.

<br>-Approximately 7.77% of cases involve provoked attacks, where there may be intentional interaction or provocation by humans.

<br>-A notable portion (7.56%) falls under the category of "Invalid”.

![image](https://github.com/mahmedkhweled/Sharks-attack-analysis/assets/67151245/4c4305a1-9db0-4c8a-ba49-284acbeb418b)

#### 3-Which countries record the highest number of shark attacks?
-USA, AUSTRALIA, and SOUTH AFRICA are prominent in the dataset.

<br>-Male cases significantly outnumber female cases.
  
  ![image](https://github.com/mahmedkhweled/Sharks-attack-analysis/assets/67151245/c9b283dd-783b-4773-a22c-0ad87c3ca183)

#### 4-What are the most attacked activities?
-Surfing and Swimming are the highest activities with total number of cases.

<br>-Swimming constitutes the highest percentage of fatal cases, accounting for approximately 28%. This elevated fatality rate may be attributed to the direct exposure of swimmers to sharks, making it a comparatively riskier activity in terms of shark encounters.

<br>-Diving and scuba diving have lower total cases, but the proportion of fatal cases is relatively higher. 

![image](https://github.com/mahmedkhweled/Sharks-attack-analysis/assets/67151245/82b228ed-6700-4834-8f21-c8a62f713b2f)

#### 5-How many attacks are fatal and what is the gender ratio?
-I create in ‘Sex’ column a new category called ‘Group’ which in some cases sharks attacked group of people at the same time.

<br>-Approxmatly 81% of cases are men ,15% women and 4% Group.

<br>-Among the cases analyzed, 14% proved to be fatal,11.3% of these fatal outcomes being identified among male individuals.
### 6-Which shark species is most frequently attacking people? 
-White sharks are the most frequently associated species in shark attacks.

<br>-The type "Shark involvement not confirmed" indicates the uncertainty in linking these incidents to shark involvement in cases where sharks are not clearly identified as the cause of injury or death.
<br>-The category labeled "Not identified" is the most prevalent, suggesting a significant number of cases where the specific shark involved remains unknown.  This confusion could result from circumstances in which the victim was ignorant of shark species or failed to notice the shark.

#### 7- Which body part sustains the most injuries and How many not injured cases?

-Firstly, we will make a new columns ‘Body Parts’ and ‘Counter’ to count the number of injuries in every body part by using ‘SUMPRODUCT’, ‘LEN’ and ‘SUBSTITUTE’ functions to extract it from the ‘Injury’ column as we used ‘PROPER’ function to normalize all words in the column to not having any confusion while extracting the words.

` =SUMPRODUCT(LEN(Sharks_attack__3[Injury])-LEN(SUBSTITUTE(Sharks_attack__3[Injury],P3,"")))/LEN(P3)` 


<br>-Leg and foot cases are the most affected body parts.
<br>-'No Injury' represents 17.5% of all cases, which is a significant portion of the total.

![image](https://github.com/mahmedkhweled/Sharks-attack-analysis/assets/67151245/e0a0a23f-a21b-493a-864a-71d6f0c879ca)


### Visualization:

![ahmeddashboard](https://github.com/mahmedkhweled/Sharks-attack-analysis/assets/67151245/cd5c02bf-513e-4d69-9387-4ec41e3676e9)
