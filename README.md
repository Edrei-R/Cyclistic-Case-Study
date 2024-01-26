# Cyclistic Case Study

## Introduction

This case study centers around the analysis of Cyclistic, a bike-sharing company. The purpose of this document is to guide you through the entire process in RStudio, starting from data loading to the creation of data visualizations. I am Edrei Rodriguez and will be the Data Analyst guiding you through this analysis. 


### Company Objective

Cyclistic is a bike-share company in Chicago, which has grown significantly since its 2016 launch; currently operating with 5,824 bicycles and 692 stations. Cyclistic's primary goal is to transition casual riders, and users of single-ride or full-day passes, into profitable annual members.

The focus of this analysis is to delve into user behavior, aiming to answer critical questions. These include understanding the differences in bike usage between annual members and casual riders, with the objective of identifying the motivations that could prompt casual riders to opt for memberships.  The resulting insights will guide Cyclistic in effectively utilizing digital media and other strategies to influence user decisions.

As the analyst leading this investigation, I will be dissecting Cyclistic's historical bike trip data, uncovering patterns, and gaining insights. By understanding these usage dynamics, we can develop data-driven strategies that optimize Cyclistic's bike-share program, aligning it with the company's goal of maximizing member conversions.


### Data

The dataset utilized in this analysis encompasses bike riding data spanning the entire year of 2022. This dataset has been made available through Motivate International Inc.


# Analysis

## Wrangling Data


### Folders on Your Computer

Within the Documents Folder on your computer, create a folder called 'R'. 

Within this 'R' folder, create a new folder called 'Cyclistic_Data' and place the raw data files in this folder. We will reference this folder later when pulling the data files.


### Creating New Project In RStudio

In RStudio click on 'File' in the upper right hand corner, then click 'New Project'. (This can also be done by clicking 'New Project' on the top right of the screen) 

Once the pop up opens, click 'New Directory', then click 'New Project' from the list. 

You will see a screen asking for 'Directory name' with a blank for input, and also a 'Create project as subdirectory of:' with the option to select where the project will be stored. This will be a critical reference point of the location we will pull data files from later. 

Under 'Directory name:' label the file "Cyclistic_Case_Study". Under 'Create project as subdirectory of:' click the R folder we made previously, which is found within the Documents folder on your computer. 


### Packages Needed For Import of Data

Install the packages below:

* tidyverse: Essential R packages for streamlined data tasks including manipulation, plotting, and analysis.

* lubridate: A tidyverse package for simplified date and time data handling in R.

* janitor: A tidyverse package that simplifies data cleaning by removing emptiness and improving data quality.

* dplyr: A data manipulation package that provides a set of functions for efficient data wrangling, filtering, grouping, summarizing, and arranging data tables.

* ggplot2: A visualization package for creating customized graphics, plots and additional visuals.

```{r message=FALSE, warning=FALSE}

# Mirror for installing packages
options(repos = c(CRAN = "https://cran.rstudio.com"))
```

We are including 'quiet= TRUE' in order to prevent the directory showing in the report. 

```{r message=FALSE, warning=FALSE}
install.packages("tidyverse", quiet = TRUE)
install.packages("lubridate", quiet = TRUE)
install.packages("janitor", quiet = TRUE)
install.packages("dplyr", quiet = TRUE)
install.packages("ggplot2", quiet = TRUE)
```

Load the packages into the library

```{r message=FALSE, warning=FALSE}
library(tidyverse)
library(lubridate)
library(janitor)
library(dplyr)
library(tidyr)
library(ggplot2)
```


### Load Data

```{r}
df1 <- read.csv("./Cyclistic_Data/202201-divvy-tripdata.csv")
df2 <- read.csv("./Cyclistic_Data/202202-divvy-tripdata.csv")
df3 <- read.csv("./Cyclistic_Data/202203-divvy-tripdata.csv")
df4 <- read.csv("./Cyclistic_Data/202204-divvy-tripdata.csv")
df5 <- read.csv("./Cyclistic_Data/202205-divvy-tripdata.csv")
df6 <- read.csv("./Cyclistic_Data/202206-divvy-tripdata.csv")
df7 <- read.csv("./Cyclistic_Data/202207-divvy-tripdata.csv")
df8 <- read.csv("./Cyclistic_Data/202208-divvy-tripdata.csv")
df9 <- read.csv("./Cyclistic_Data/202209-divvy-tripdata.csv")
df10 <- read.csv("./Cyclistic_Data/202210-divvy-tripdata.csv")
df11 <- read.csv("./Cyclistic_Data/202211-divvy-tripdata.csv")
df12 <- read.csv("./Cyclistic_Data/202212-divvy-tripdata.csv")
```


### Combine Data

```{r}
year_2022_data <- rbind(df1, df2, df3, df4, df5, df6, df7, df8, df9, df10, df11, df12)
```


### Remove Empty Rows & Columns

```{r}
year_2022_data <- janitor::remove_empty(year_2022_data, which = "rows")
year_2022_data <- janitor::remove_empty(year_2022_data, which = "cols")
```


### Checking and Correcting Data Types

In your environment you can click on the arrow next to 'year_2022_data' in order to see the data type for each column. We are doing this to make sure that the values are listed as the right type before performing calculations. 

As we go through the data list, we notice that the 'started_at' and 'ended_at' columns are currently written as words (text). However, we need to change this data into a timestamp format. If we don't do this, RStudio won't recognize them as dates or times, making it impossible to calculate time-related things unless we switch them to the right type of data.

In order to convert these columns from characters to timestamps we can use the 'lubridate' package we loaded earlier.

In the operations below you will see additional symbols:

* $ is used to access a specific column (variable) within a data frame. In this case 'started_at' and 'ended_at' within the year_2022_data data frame.
 
* :: is used to specify that you're using a function from a specific package. In this case, lubridate::ymd_hms() means you're using the ymd_hms() function from the lubridate package.

```{r}
year_2022_data$started_at <- lubridate::ymd_hms(year_2022_data$started_at)
year_2022_data$ended_at <- lubridate::ymd_hms(year_2022_data$ended_at)
```


### Refining Data

Considering our objectives, there's some information in our data frame that we can remove. Yet, to ensure we have a copy for reference, we'll create a new data frame with the adjustments and name it 'year_2022_data_clean'.

We will eliminate latitude and longitude columns, then eliminate rows with empty values, then eliminate duplicates. The order of this operation is important as we wouldn't want to delete rows with, for example, an empty longitude coordinate value since we are not using that column. It is best to remove the unneeded columns first, then eliminate any rows with missing values, then duplicates. 

In the code below you will see additional symbols:

* %>% is an operator, or pipe operator, that is used to enhance code readability by allowing the continuation of operations across lines. It aids in creating clearer and more sequential representations of code, especially in data manipulation tasks.

```{r}
year_2022_data_clean <- year_2022_data %>%
  select(-start_lat, -start_lng, -end_lat, -end_lng) %>%
  na.omit() %>% 
  distinct()
```


### Calculate Trip Duration

Next, we'll compute the duration of the trips in seconds, minutes, and hours, removing any values that fall below zero.

```{r}
year_2022_data_clean <- year_2022_data_clean %>%
  mutate(duration_sec = difftime(ended_at, started_at, units = "secs")) %>%
  filter(duration_sec > 0)%>%
  mutate(duration_min = difftime(ended_at, started_at, units = "min")) %>%
  filter(duration_min > 0)%>%
  mutate(duration_hours = difftime(ended_at, started_at, units = "hours")) %>%
  filter(duration_hours > 0)
```


### Checking Values and Setting Parameters

Now that we have calculated the durations of the trips in seconds, minutes, and hours, it's time to inspect the data. We will check the min, max, and average values to understand if there are any jarring outliers that might impact our analysis. This step helps us decide whether we need to set certain limits or conditions for a more accurate interpretation of the data.

We will check the min, max, and average values for both minutes and hours.

```{r}
year_2022_hr_stats <- year_2022_data_clean %>%
  summarise(
    average_trip = mean(duration_hours, na.rm = TRUE),
    min_trip = min(duration_hours, na.rm = TRUE),
    max_trip = max(duration_hours, na.rm = TRUE)
  )
print(year_2022_hr_stats)


year_2022_min_stats <- year_2022_data_clean %>%
  summarise(
    average_trip = mean(duration_min, na.rm = TRUE),
    min_trip = min(duration_min, na.rm = TRUE),
    max_trip = max(duration_min, na.rm = TRUE)
  )
print(year_2022_min_stats)
```

In looking at the results we see a wide range values, with trips ranging from '0.01666667 min' (or roughly 1 second) to '689.7875 hours' (or almost 29 days). These outliers, including trips lasting mere seconds or spanning weeks, are highly implausible for a bike-sharing service. Factors such as system tests, bike removals without return, or forgotten rides could contribute to these outliers.

In order to better understand how many of these values exist, lets set a parameter we will use to measure. We will check and see how many values are below  1 minute in trip duration, and above 24 hours. 

```{r}
# Count trips above 24 hours
trips_above_24_hours <- sum(year_2022_data_clean$duration_hours > 24)
print(trips_above_24_hours)
# Count trips below 1 minute
trips_below_1_minute <- sum(year_2022_data_clean$duration_min < 1)
print(trips_below_1_minute)
```

We now identified 5360 trips exceeding 24 hours and 120558 trips lasting less than a minute. To minimize the potential impact of these outliers on our analysis, we made the decision to focus exclusively on trips falling within a reasonable time frame: between 1 minute and 24 hours. This deliberate restriction ensures that our analysis is based on data that aligns more closely with typical bike-sharing patterns, providing a more accurate foundation for our insights.

With this being our trajectory moving forward, we will create a new data frame with trip durations ranging from 1 minute to 24 hours. In order to do this we will write code that filters out the values we want, keeping trip duration above 1 min and below 24 hours. 

```{r}
year_2022_data_filtered <- year_2022_data_clean %>%
  filter(duration_min >= 1) %>%
  filter(duration_hours <= 24)
```

With our project scope refined, let's proceed to modify the data for our analysis. Our next step involves calculating summaries and averages to better emphasize the distinctions between casual riders and members. This will provide a clearer picture of the patterns and trends within each rider group.


### Extracting Relevant Data For Analysis

Now that our data is clean and our parameters are defined, we can focus on our objective: discerning the differences between casual and member riders. We will be extracting data from the 'year_2022_data_filtered' data frame.

To gain an initial perspective, we'll start by examining the percentage distribution of casual and member riders.

In the following code, the data is grouped based on the 'member_casual' column, categorizing trips as either 'member' or 'casual'. The 'total_trips' for each category are then calculated using the summarise function. The data is ungrouped to ensure subsequent operations apply to the entire dataset. Finally, a new column 'percentage' is added, representing the proportion of total trips each category contributes, calculated by dividing the 'total_trips' by the sum of all trips and multiplying by 100.

```{r}
overall_trip_summary <- year_2022_data_filtered %>%
  group_by(member_casual) %>%
  summarise(total_trips = n()) %>%
  ungroup() %>%
  mutate(percentage = total_trips / sum(total_trips) * 100)
```

Our next objective is to obtain a comprehensive overview of the annual riding data for both casual and member riders. Before proceeding, we'll optimize our data preprocessing by making essential modifications to the 'year_2022_data_filtered' dataset. This ensures efficiency in our subsequent data manipulations. In the upcoming code sections, we'll focus on adding month names, weekday names, and extracting the starting hour for each trip.  

We will begin by incorporating a column for months. This code adds a new column to the 'year_2022_data_filtered' data frame, named 'month_name'. This new column contains the month names corresponding to the 'started_at' column values, which represent the starting date and time of each record. The %B format specifier is used to extract the full month names, providing a clearer representation of the data in terms of months.

```{r}
# Add a new column with month names
year_2022_data_filtered <- year_2022_data_filtered %>%
  mutate(month_name = format(started_at, "%B"))
```

Subsequently, we'll shift our focus to a weekly analysis, encompassing the total number of trips, average trip duration, and the peak start time for each day. 

This code introduces a new column called 'day_name' into the 'year_2022_data_filtered' data frame. The values in this column are taken from the 'started_at' column, representing the starting date and time of each record. The %A format specifier is used to extract the full day names, providing a more descriptive and understandable categorization of the data based on the days of the week.

```{r}
# Add a new column with day names
year_2022_data_filtered <- year_2022_data_filtered %>%
  mutate(day_name = format(started_at, "%A"))
```

This code adds a new column named 'starting_hour' to the 'year_2022_data_filtered' data frame. The values in this column are obtained by extracting the starting hour from the 'started_at' column, which contains the timestamp for the beginning of each trip in the dataset. This allows for a convenient and simplified representation of the trip starting hours for further analysis or visualization.

```{r}
# Extract starting hour of trips
year_2022_data_filtered$starting_hour <- hour(year_2022_data_filtered$started_at) 
```

Next, using the additional information from the newly added columns, we will generate specialized data frames. This approach enables us to conduct more detailed analyses, honing in on specific aspects of the riding data.


### Creating New Data Frames

Now that we have the necessary new columns, we can tailor specific data frames to analyze the information of interest. These data frames are being created from the 'year_2022_data_filtered' data frame. 

We will proceed by crafting code for casual and member riders, detailing the monthly total ride count and average duration for each month. 

The subsequent code segments generate dedicated data frames summarizing monthly trends for both rider groups. By filtering the 'year_2022_data_filtered' data frame, these codes isolate data for each rider type. Subsequently, the data is grouped based on the month names, then they calculate the total number of rides and the average ride duration for each month. The resulting summaries retain key information, specifically the month name, total rides, and average duration, providing a concise overview for each rider group.

```{r}       
# Create a new data frame for monthly summaries for casual riders
casual_month_summary <- year_2022_data_filtered %>%
  filter(member_casual == "casual") %>%
  group_by(month_name) %>%
  summarise(
    total_rides = n(),
    average_duration = mean(duration_min, na.rm = TRUE)
  ) %>%
  select(month_name, total_rides, average_duration)

# Display the result
print(casual_month_summary)

# Create a new data frame for monthly summaries for member riders
member_month_summary <- year_2022_data_filtered %>%
  filter(member_casual == "member") %>%
  group_by(month_name) %>%
  summarise(
    total_rides = n(),
    average_duration = mean(duration_min, na.rm = TRUE)
  ) %>%
  select(month_name, total_rides, average_duration)

# Display the result
print(member_month_summary)
```

We will now modify the data frames we just created, by arranging the months in chronological order. This will be achieved using the factor function.

The factor() function is used to transform categorical data into a factor, converting character or numeric vectors to a categorical or ordinal type with defined levels. 

```{r}
# Putting the months in chronological order.
casual_month_summary$month_name <- factor(casual_month_summary$month_name, 
                                          levels = month.name, ordered = TRUE)

member_month_summary$month_name <- factor(member_month_summary$month_name, 
                                          levels = month.name, ordered = TRUE)
```

Now that we've organized the data frames for both casual and member riders throughout different months, our next focus is on constructing data frames that specifically highlight patterns on weekdays.

The provided code below generates separate data frames summarizing daily trends for casual and member riders. First, for casual riders, the code filters the 'year_2022_data_filtered' data frame to include only casual riders, groups the data by both day name and starting hour, calculates the total number of rides and the average ride duration for each combination, selects the row with the maximum total rides for each day, arranges the results by day name, and finally displays the summary. The process is repeated for member riders. The resulting summaries highlight the day name, total rides, and average duration, emphasizing peak hours for each rider type.

```{r}
# Create a data frame for casual riders
casual_daily_summary <- year_2022_data_filtered %>%
  filter(member_casual == "casual") %>%
  group_by(day_name, starting_hour) %>%
  summarise(
    total_rides = n(),
    average_duration = mean(duration_min, na.rm = TRUE)
  ) %>%
  slice(which.max(total_rides)) %>%
  arrange(day_name) %>%
  ungroup()

# Display the result
print(casual_daily_summary)


# Create a data frame for member riders
member_daily_summary <- year_2022_data_filtered %>%
  filter(member_casual == "member") %>%
  group_by(day_name, starting_hour) %>%
  summarise(
    total_rides = n(),
    average_duration = mean(duration_min, na.rm = TRUE)
  ) %>%
  slice(which.max(total_rides)) %>%
  arrange(day_name) %>%
  ungroup()

# Display the result
print(member_daily_summary)

```

The weekdays will be arranged in chronological order in the code below:

```{r}
# Putting the Weekdays in chronological order.
casual_daily_summary$day_name <- factor(casual_daily_summary$day_name, 
                                        levels = c("Sunday", "Monday", "Tuesday", 
                                                   "Wednesday", "Thursday", "Friday", 
                                                   "Saturday"), ordered = TRUE)

member_daily_summary$day_name <- factor(member_daily_summary$day_name, 
                                        levels = c("Sunday", "Monday", "Tuesday", 
                                                   "Wednesday", "Thursday", "Friday", 
                                                   "Saturday"), ordered = TRUE)
```

We will now repeat these calculations for the generation of hourly data frames for both casual and member riders.

The provided code generates separate data frames summarizing hourly trends for casual and member riders. For casual riders, the code filters the 'year_2022_data_filtered' data frame to include only casual riders, groups the data by the starting hour, calculates the total number of trips and the average trip duration for each hour, arranges the results by the starting hour, and finally displays the summary. The process is repeated for member riders. The resulting summaries highlight the starting hour, total trips, and average duration, offering insights into the distribution of rides throughout the day for each rider type.

```{r warning=FALSE}
# Create a data frame for casual riders
casual_hourly_summary <- year_2022_data_filtered %>%
  filter(member_casual == "casual") %>%
  group_by(starting_hour) %>%
  summarise(
    total_trips = n(),
    average_duration = mean(duration_min, na.rm = TRUE)
  ) %>%
  arrange(starting_hour) %>%
  ungroup()

# Display the result
print(casual_hourly_summary)


# Create a data frame for member riders
member_hourly_summary <- year_2022_data_filtered %>%
  filter(member_casual == "member") %>%
  group_by(starting_hour) %>%
  summarise(
    total_trips = n(),
    average_duration = mean(duration_min, na.rm = TRUE)
  ) %>%
  arrange(starting_hour) %>%
  ungroup()

# Display the result
print(member_hourly_summary)
```

In our data analysis journey, we began by cleaning and setting parameters for the 'year_2022_data' dataset. Focusing on the distinctions between casual and member riders, we extracted insights from 'year_2022_data_filtered.' 

We explored the percentage of casual and member riders, created monthly summaries detailing total rides and average durations, and further analyzed weekly and hourly riding patterns. By filtering and grouping data frames we've structured our data to facilitate meaningful visualizations. The next section will visualize these summaries to gain a comprehensive understanding of rider behaviors and patterns throughout the year.


## Plotting Data

Having organized our data into various frames, the next step involves visualizing it to uncover patterns and relationships. Through plots, we aim to discern correlations and distinctions between casual and member riders, providing a visual narrative to enhance our understanding of the data.

This code generates a stacked bar chart using the ggplot2 package. It visualizes the distribution of overall trips between casual and member riders. The bar represents total trips, split into segments based on the percentage contribution of each rider type. A white border enhances visual clarity, and percentage labels on the bar sections provide precise information. The chart's title, axis labels, and color scheme are set to communicate the comparison effectively, creating an informative and visually appealing representation of the data.

```{r warning=FALSE}
# Member vs Casual: Create a stacked bar chart
ggplot(overall_trip_summary, aes(x = "", y = percentage, fill = member_casual)) +
  geom_bar(stat = "identity", width = 1, color = "white") +  # White border for clarity
  geom_text(aes(label = paste0(round(percentage), "%")), 
            position = position_stack(vjust = 0.5)) +
  labs(title = "Overall Trip Distribution - Casual vs. Member Riders", 
       x = "Total Trips - Member vs. Casual", y = "Percentage", fill = "Rider") +
  theme_minimal() +
  theme(legend.position = "bottom") +
  scale_y_continuous(labels = scales::percent_format(scale = 1)) +
  scale_fill_manual(values = c("coral2", "steelblue"))  # Colors for plot
```

![Overall Trip Distribution - Casual vs. Member Riders](https://github.com/Edrei-R/Cyclistic-Case-Study/blob/822f1ff7459f63a30e6298c5a12fff79ccb8c0e5/Plots/Overall%20Trip%20Distribution%20-%20Casual%20vs.%20Member%20Riders.jpg)

Based on the overall distribution casual riders make up 41% of the amount of riders, while members make up 59%. 

With this being the case, lets understand the behavioral pattern differences between these two groups.

Firstly, lets look at their patters throughout the year, using months as increments. The code below generates a line plot to compare the monthly total trips between casual and member riders. It uses two separate data frames, 'casual_month_summary' and 'member_month_summary', representing summarized information for each rider type. The x-axis represents months, the y-axis represents the total number of trips, and different colors distinguish between casual and member riders. The plot includes custom titles, color scales, and y-axis limits for better visualization. Additionally, the x-axis labels are adjusted for better readability by rotating them at a 45-degree angle.

```{r warning=FALSE}
# Months -Total Trips Plot
ggplot() +
  # Line plot for casual_month_summary
  geom_line(data = casual_month_summary, aes(x = month_name, y = total_rides, 
                                             color = "Casual", group = 1), size = 1.25) +
  # Line plot for member_month_summary
  geom_line(data = member_month_summary, aes(x = month_name, y = total_rides, 
                                             color = "Member", group = 1), size = 1.25) +
  # Customize the appearance of the plot
  labs(title = "Monthly Total Trips - Casual vs Member Riders", x = "Month", 
       y = "Trips", color = "Rider") +
  scale_color_manual(values = c("Member" = "steelblue", "Casual" = "coral2")) +
  scale_y_continuous(labels = scales::comma_format(scale = 1), limits = c(0, 500000)) +  
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))  
```

![Monthly Total Trips - Casual vs Member Riders](https://github.com/Edrei-R/Cyclistic-Case-Study/blob/ff2fcf819c2f31fd9911c02fae510a8f9de6ad52/Plots/Monthly%20Total%20Trips%20-%20Casual%20vs%20Member%20Riders.jpg)

Throughout the year, both casual and member riders follow a comparable trend, starting below 100,000 trips and reaching peaks in July for casual riders and August for members. Both groups gradually taper off, ending the year with totals below 150,000. Notably, members consistently take more trips than casual riders throughout the entire year.

Now, let's explore whether there are variations in the average duration of trips taken during these months to glean additional insights.

Below, we write code generating a line plot comparing the monthly average trip duration between casual and member riders. The x-axis represents the months, while the y-axis displays the average trip duration in minutes. Two lines are plotted, one for casual riders and one for member riders, allowing for a visual comparison of how the average trip duration varies for each rider category over the months. The color-coded lines and customized plot aesthetics enhance readability, providing valuable insights into the temporal patterns of trip durations for both casual and member riders.

```{r warning=FALSE}
# Months - Average Trip Duration Plot
ggplot() +
  # Line plot for casual_month_summary
  geom_line(data = casual_month_summary, aes(x = month_name, y = average_duration, 
                                             color = "Casual", group = 1), size = 1.25) +
  # Line plot for member_month_summary
  geom_line(data = member_month_summary, aes(x = month_name, y = average_duration, 
                                             color = "Member", group = 1), size = 1.25) +
  # Customize the appearance of the plot
  labs(title = "Monthly Average Trip Duration - Casual vs Member Riders", x = "Month", 
       y = "Trip Duration (Min)", color = "Rider") +
  scale_color_manual(values = c("Member" = "steelblue", "Casual" = "coral2")) +
  scale_y_continuous(labels = scales::comma_format(scale = 1), limits = c(0, 30)) +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))  
```

![Monthly Average Trip Duration - Casual vs Member Riders](https://github.com/Edrei-R/Cyclistic-Case-Study/blob/cf02d70126c06b9a181e277574a5f6f3893fed62/Plots/Monthly%20Average%20Trip%20Duration%20-%20Casual%20vs%20Member%20Riders.jpg)

Here, distinct patterns emerge. Despite members consistently taking more trips than casual riders throughout the year, as evidenced in the previous plot, we observe that casual riders, on average, embark on longer journeys consistently across all months. Another intriguing observation is that while casual riders experience their highest trip frequency in July, the longest average trips occur more prominently in May and March. On the other hand, members, with their peak trip count in August, maintain a relatively consistent trip duration within the 13-minute range from May to August, with June showing the highest average trip duration.

Now that we have gained insights into the yearly patterns, let's see how this information unfolds on a weekly basis. This approach may reveal additional nuances and insights in the riding behavior of casual and member riders.

This code produces a line plot comparing the daily total trips between casual and member riders. The x-axis represents the days of the week, while the y-axis displays the total number of trips with a range of 20,000 to 70,000 since we know all the trips, for both rider types, fall within this threshold. Two lines are plotted, one for casual riders and one for member riders, allowing for a visual comparison of how the daily total trips vary for each rider category throughout the week. The color-coded lines and customized plot aesthetics enhance readability, providing insights into the differences in riding patterns between casual and member riders on a daily basis.

```{r warning=FALSE}
# Weekdays - Total Trips Plot  
ggplot() +
  # Line plot for casual_daily_summary
  geom_line(data = casual_daily_summary, aes(x = day_name, y = total_rides, 
                                             color = "Casual", group = 1), size = 1.25) +
  # Line plot for member_daily_summary
  geom_line(data = member_daily_summary, aes(x = day_name, y = total_rides, 
                                             color = "Member", group = 1), size = 1.25) +
  # Customize the appearance of the plot
  labs(title = "Daily Total Trips - Casual vs Member Riders", x = "Day", 
       y = "Trips", color = "Rider") +
  scale_color_manual(values = c("Member" = "steelblue", "Casual" = "coral2")) +
  scale_y_continuous(labels = scales::comma_format(scale = 1), 
                     limits = c(20000, 70000)) +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

![Daily Total Trips - Casual vs Member Riders](https://github.com/Edrei-R/Cyclistic-Case-Study/blob/cf02d70126c06b9a181e277574a5f6f3893fed62/Plots/Daily%20Total%20Trips%20-%20Casual%20vs%20Member%20Riders.jpg)

In this visualization, we observe another noteworthy distinction. Member riders exhibit a higher frequency of trips during Monday through Thursday, whereas casual riders are generally spread out throughout the week, with a slight increase on Saturdays. Additionally, it's interesting to note that on both Saturday and Sunday, casual riders surpass member riders in the number of rides.

Lets analyze this further by looking at the breakdown this would have per day on the hour with the most start times. 

```{r warning=FALSE}
# Weekdays - Peak Riding Hour Plot
ggplot() +
  # Line plot for casual_daily_summary
  geom_line(data = casual_daily_summary, aes(x = day_name, y = starting_hour, 
                                             color = "Casual", group = 1), size = 1.25) +
  # Line plot for member_daily_summary
  geom_line(data = member_daily_summary, aes(x = day_name, y = starting_hour, 
                                             color = "Member", group = 1), size = 1.25) +
  # Customize the appearance of the plot
  labs(title = "Daily Peak Riding Hour - Casual Riders vs. Members", x = "Day", 
       y = "Hour", color = "Rider") +
  scale_color_manual(values = c("Member" = "steelblue", "Casual" = "coral2")) +
  scale_y_continuous(labels = scales::comma_format(scale = 1), limits = c(0, 23)) +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

![Daily Peak Riding Hour - Casual Riders vs. Members](https://github.com/Edrei-R/Cyclistic-Case-Study/blob/cf02d70126c06b9a181e277574a5f6f3893fed62/Plots/Daily%20Peak%20Riding%20Hour%20-%20Casual%20Riders%20vs.%20Members.jpg)

Throughout Sunday to Friday, the two lines maintain a close alignment, deviating by only one hour on Saturdays. Despite the earlier plot highlighting distinctions in trip counts between members and casual riders, their peak start times consistently coincide throughout the week. Let's delve into how this consistency reflects on trip durations during weekdays. 

```{r}
# Weekdays -Average Trip Duration Plot
ggplot() +
  # Line plot for casual_daily_summary
  geom_line(data = casual_daily_summary, aes(x = day_name, y = average_duration, 
                                             color = "Casual", group = 1), size = 1.25) +
  # Line plot for member_daily_summary
  geom_line(data = member_daily_summary, aes(x = day_name, y = average_duration, 
                                             color = "Member", group = 1), size = 1.25) +
  # Customize the appearance of the plot
  labs(title = "Daily Average Trip Duration - Casual vs Member Riders", x = "Day", 
       y = "Trip Duration (Min)", color = "Rider") +
  scale_color_manual(values = c("Member" = "steelblue", "Casual" = "coral2")) +
  scale_y_continuous(labels = scales::comma_format(scale = 1), limits = c(0, 30)) +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))

```

![Daily Average Trip Duration - Casual vs Member Riders](https://github.com/Edrei-R/Cyclistic-Case-Study/blob/cf02d70126c06b9a181e277574a5f6f3893fed62/Plots/Daily%20Average%20Trip%20Duration%20-%20Casual%20vs%20Member%20Riders.jpg)

The ongoing trend reveals that casual riders generally have longer ride durations compared to members. Notably, there is a distinctive increase in the duration of rides for casual riders on the weekend. While casual riders typically range between 19 to 28 minutes, members, although experiencing a slight uptick on weekends, tend to maintain a narrower riding window between 12 to 15 minutes.

While this plot provides valuable insights, a more detailed hourly breakdown might offer further nuances. Let's proceed to visualize this for a more comprehensive analysis.

The following code creates a line plot depicting the hourly total trips for casual and member riders. The x-axis represents the hours of the day, ranging from 0 to 23, while the y-axis indicates the total number of trips. Each line corresponds to either casual or member riders, distinguished by color. The plot is customized with a title, axis labels, specific color scheme, and gridlines for better visualization. Additionally, the x-axis labels are angled for clarity, and minor gridlines are removed to enhance readability.

```{r warning=FALSE}
# Hourly - Total Trips Plot
ggplot() +
  # Line plot for casual_hourly_summary
  geom_line(data = casual_hourly_summary, aes(x = starting_hour, y = total_trips, 
                                              color = "Casual", group = 1), size = 1.25) +
  # Line plot for member_hourly_summary
  geom_line(data = member_hourly_summary, aes(x = starting_hour, y = total_trips,
                                              color = "Member", group = 1), size = 1.25) +
  # Customize the appearance of the plot
  labs(title = "Hourly Total Trips - Casual vs Member Riders", x = "Hour", y = "Trips", 
       color = "Rider") +
  scale_color_manual(values = c("Member" = "steelblue", "Casual" = "coral2")) +
  scale_x_continuous(breaks = seq(0, 23, by = 1)) +
  scale_y_continuous(labels = scales::comma_format(scale = 1)) +
  theme_minimal() +
  theme(
    axis.text.x = element_text(angle = 45, hjust = 1),
    panel.grid.major.x = element_line(color = "gray", size = 0.1),
    panel.grid.minor.x = element_blank()
  )
```

![Hourly Total Trips - Casual vs Member Riders](https://github.com/Edrei-R/Cyclistic-Case-Study/blob/cf02d70126c06b9a181e277574a5f6f3893fed62/Plots/Hourly%20Total%20Trips%20-%20Casual%20vs%20Member%20Riders.jpg)

In this plot, we gain deeper insights into the riding patterns. Both casual and member riders show a noticeable increase in rides at 17:00 (5 pm), but their overall trajectories differ. Members exhibit an early spike at 8:00, followed by an irregular rise towards their peak hours from 15:00 (3 pm) to 19:00 (7 pm), and then a gradual decline. In contrast, casual riders experience a relatively steady increase in the number of rides throughout the day, reaching a peak at 17:00 (5 pm) and declining towards later hours. An intriguing observation is the significant difference in the number of rides between member and casual riders, particularly pronounced at 7:00, 8:00, and 17:00 (5 pm).

Let's visualize how this information translates into the average duration of rides throughout the day in the following plot.

This code generates a line plot illustrating the hourly average trip duration for casual and member riders. The x-axis represents the hours of the day (0 to 23), while the y-axis shows the average duration of trips in minutes. Each line corresponds to either casual or member riders, distinguished by color. The plot is customized with a title, axis labels, specific color scheme, and gridlines for clarity. The x-axis labels are angled for better visibility, and minor gridlines are removed for improved readability.

```{r warning=FALSE}
# Hourly - Trip Duration Plot
ggplot() +
  # Line plot for casual_hourly_summary
  geom_line(data = casual_hourly_summary, aes(x = starting_hour, y = average_duration, 
                                              color = "Casual", group = 1), size = 1.25) +
  # Line plot for member_hourly_summary
  geom_line(data = member_hourly_summary, aes(x = starting_hour, y = average_duration, 
                                              color = "Member", group = 1), size = 1.25) +
  # Customize the appearance of the plot
  labs(title = "Hourly Average Trip Duration - Casual vs Member Riders", x = "Hour", 
       y = "Trip Duration (Min)", color = "Rider") +
  scale_color_manual(values = c("Member" = "steelblue", "Casual" = "coral2")) +
  scale_x_continuous(breaks = seq(0, 23, by = 1)) +
  scale_y_continuous(labels = scales::comma_format(scale = 1)) +
  theme_minimal() +
  theme(
    axis.text.x = element_text(angle = 45, hjust = 1),
    panel.grid.major.x = element_line(color = "gray", size = 0.1),
    panel.grid.minor.x = element_blank()
  )
```

![Hourly Average Trip Duration - Casual vs Member Riders](https://github.com/Edrei-R/Cyclistic-Case-Study/blob/cf02d70126c06b9a181e277574a5f6f3893fed62/Plots/Hourly%20Average%20Trip%20Duration%20-%20Casual%20vs%20Member%20Riders.jpg)

Here, it's worth noting once again that casual riders consistently take longer rides, surpassing members throughout the entire day. The peak average durations for casual riders occur between 10:00 and 14:00 (2 pm), reaching above 25 minutes. In contrast, member riders maintain a narrower range, with average durations ranging from 10 to 14 minutes throughout the day, and the longest average ride being 13.5 minutes


# Conclusion

### Overall Data Insights

In our extensive data analysis, we explored the ridership trends and behaviors of casual and member riders. Notably, members consistently surpassed casual riders in the total number of trips, constituting 59% of the overall ridership compared to casual riders' 41%. Throughout the year, both rider groups exhibited similar patterns, starting below 100,000 trips, peaking in July for casual riders and August for members, and tapering off by the year-end.

Differences became apparent when considering average trip durations. Casual riders consistently took longer journeys across all months, with peak average durations exceeding 25 minutes between 10:00 and 14:00 (2 pm). Members, while maintaining a relatively consistent trip duration within the 13-minute range from May to August, recorded their peak trip count in August.

In the weekly analysis, members favor routine-oriented ridership with higher weekday trip frequency (Monday to Thursday), while casual riders show a preference for weekends, surpassing members in trip count on Saturday and Sunday. Casual riders also exhibit longer rides, particularly on weekends (19-28 minutes), whereas members maintain a narrower riding window (12-15 minutes) even with a slight uptick in weekend durations. This showcased distinct weekend riding preferences between the two groups.

Hourly breakdowns revealed interesting patterns. Members exhibited an early spike at 8:00, irregular rises towards peak hours from 15:00 to 19:00, and a gradual decline afterward. Casual riders displayed a steady increase throughout the day, peaking at 17:00 (5 pm), with significant differences in trip count at 7:00, 8:00, and 17:00 compared to members.

This detailed analysis provides nuanced insights into the riding behaviors of casual and member riders, offering valuable information for strategic decision-making, resource allocation, and service optimization within the biking-sharing platform. Understanding these distinctions can contribute to tailoring services to meet the unique preferences and habits of each rider group, enhancing overall user experience and satisfaction.


### Potential Next Steps

Our comprehensive analysis has unveiled intriguing insights into the behaviors of casual and member riders, providing a foundation for strategic considerations. With the overarching goal of converting casual riders into members, we'll explore correlations and propose avenues for further investigation and potential marketing strategies.


#### Deeper Dive for Specific Trends:

While our initial analysis has delivered valuable insights, a more focused exploration into specific months could uncover nuanced trends that are essential for developing targeted business and marketing strategies. By narrowing down our study to specific months, we can refine our approach and gain a more granular understanding of rider behaviors. Another avenue to explore involves expanding the dataset and examining patterns and trajectories over multiple years. Considering both options—expanding the dataset to cover multiple years and examining patterns and trajectories—opens the door to discovering valuable insights.


#### Digital Marketing Strategies:

In the realm of digital strategies, emphasizing the benefits that membership offers for longer rides becomes a key focal point. Casual riders consistently outperform members in the duration of trips on average. This information could serve as a compelling incentive for casual riders to consider the advantages of becoming members, especially if the digital marketing campaigns highlight the perks related to extended rides and unique experiences.


#### In-Person Marketing Tactics:

For in-person marketing approaches, such as billboards, events, and experiences, aligning promotional activities with the months and times exhibiting longer durations and higher frequencies of rides can be advantageous. Strategically placing advertisements or hosting events at popular stations during peak months and days for casual riders increases the likelihood of capturing their attention. This approach capitalizes on statistically higher chances of encountering casual riders at these stations during these times.


#### Endless Opportunities For Targeting Casual Riders:

The possibilities for reaching casual riders as a target audience are vast. Whether through personalized digital campaigns, tailored incentives, or experiential events, understanding the intricacies of their riding patterns provides a foundation for crafting engaging and effective strategies. By continuously refining our approaches based on ongoing data analysis, we can unlock endless opportunities to bridge the gap between casual and member riders.

As we move forward, a dynamic and data-driven strategy will be essential for optimizing outreach efforts and achieving the ultimate objective of converting casual riders into committed members.


### Final Thoughts

In conclusion, our comprehensive analysis has unveiled the intricate behaviors of both casual and member
riders, providing invaluable insights for strategic decision-making. As we progress towards the goal of converting casual riders into dedicated members, a dynamic and data-driven approach becomes essential. The
nuanced understanding of riding patterns, monthly fluctuations, and duration preferences empowers Cyclistic
to formulate targeted business strategies. These insights serve as the foundation for crafting compelling
narratives that resonate with riders, whether conveyed through digital channels or in-person interactions.
The journey toward conversion is ongoing, allowing for continuous refinement of strategies to seamlessly
align with the evolving needs and preferences of riders. Thank you for your engagement in this analytical
journey.
