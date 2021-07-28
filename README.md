# PyBer Analysis
## 1.Scope of Project

  Create a summary of the ride-sharing data by city type during the period of Jan to April. 
  The city types are:
  - Rural
  - Suburban
  - Urban
  
  ## 2. Resources
   * Data source: ride_data.csv, city_data.csv
   * Software: Python 3.7.6, Visual Studio Code version 1.58
   * web application: Jupyter Notebook
  
  ## 3. Analysis
  
  For the analysis. we need to get:
  - The total number of rides, 
  - Total number of drivers, 
  - The total fares for each city type. 
  Then, we need to calculate the follwing:
  - Average fare per ride and average fare per driver for each city type. 
  The final stage is to add this data to a new DataFrame, and format the columns.

### 3.1 Python code
  The following python code is used:
  First part is to load and read the csv.files and merge the two dataframes on their combined column "city"
     # Add Matplotlib inline magic command
    %matplotlib inline
    # Dependencies and Setup
    import matplotlib.pyplot as plt
    import pandas as pd
    import numpy as np

    # File to Load (Remember to change these)
    city_data_to_load = "city_data.csv"
    ride_data_to_load = "ride_data.csv"

    # Read the City and Ride Data
    city_data_df = pd.read_csv(city_data_to_load)
    ride_data_df = pd.read_csv(ride_data_to_load)

    # Combine the data into a single dataset
    pyber_data_df = pd.merge(ride_data_df, city_data_df, how="left", on=["city", "city"])

    # Display the data table for preview
    pyber_data_df.head()
    
    The next section is to carry out some analysis on the dataframe to get averages, sums and count and create a PyBer summary Dataframe and formatting:
    #  1. Get the total rides for each city  
    total_rides_per_city=pyber_data_df.groupby(["type"]).count()["ride_id"]
  
    # 2. Get the total drivers for each city type
    total_drivers_per_city = city_data_df.groupby(["type"]).sum()["driver_count"]
    
    #  3. Get the total amount of fares for each city type
    total_fares= pyber_data_df.groupby(["type"]).sum()["fare"] 
    
    #  4. Get the average fare per ride for each city type. 
    average_fare_per_ride = pyber_data_df.groupby(["type"]).mean()["fare"]
    
    # 5. Get the average fare per driver for each city type. 
    #sum of all the fares/total drivers
    average_fare_per_driver= pyber_data_df.groupby(["type"]).sum()["fare"]/city_data_df.groupby(["type"]).sum()["driver_count"] 
    
    #  6. Create a PyBer summary DataFrame. 
    # Create a DataFrame
    pyber_summary_df= pd.DataFrame({
              "Total Rides":total_rides_per_city, 
              "Total Drivers": total_drivers_per_city, 
              "Total Fares": total_fares,
              "Average Fare per Ride": average_fare_per_ride, 
              "Average Fare per Driver":average_fare_per_driver})
    # Format the columns:
    # Format the "Total Students" to have the comma for a thousands separator.
    pyber_summary_df["Total Rides"] = pyber_summary_df["Total Rides"].map("{:,}".format)

    # Format the "Total Budget" to have the comma for a thousands separator, a decimal separator and a "$".
    pyber_summary_df["Total Drivers"] = pyber_summary_df["Total Drivers"].map("{:,}".format)
    # Format the columns.
    pyber_summary_df["Total Fares"] = pyber_summary_df["Total Fares"].map("${:,.2f}".format)
    pyber_summary_df["Average Fare per Ride"] = pyber_summary_df["Average Fare per Ride"].map("${:.2f}".format)

    pyber_summary_df["Average Fare per Driver"] = pyber_summary_df["Average Fare per Driver"].map("${:.2f}".format) 

    # Display the data frame
    pyber_summary_df
    
    We also need to clean up the summary by deleting the index name ("type"):
    #  7. Cleaning up the DataFrame. Delete the index name
    pyber_summary_df.index.name = None

 output 1.
 ![image](https://user-images.githubusercontent.com/85843030/127341071-158b77dd-39cc-40dc-95d9-23fe1590d267.png)

 
    Next we need to create a multiple line plot that shows the total weekly of the fares for each type of city:
    # 1. Read the merged DataFrame
    pyber_data_df.info()

    # 2. Using groupby() to create a new DataFrame showing the sum of the fares for each date where the indices are the city type and date
    fare_by_date_df= pyber_data_df.groupby(["type", "date"]).sum()[['fare']]
    fare_by_date_df

    # 3. Reset the index on the DataFrame you created in #1. This is needed to use the 'pivot()' function.
    fare_by_date_df= fare_by_date_df.reset_index()
    fare_by_date_df.info() 
    
    # 4. Create a pivot table with the 'date' as the index, the columns ='type', and values='fare' to get the total fares for each type of city by the date. 
    fare_by_date_df_pivot = fare_by_date_df.pivot(index="date", columns="type", values="fare")
    fare_by_date_df_pivot.head(10)
    
    # 5. Create a new DataFrame from the pivot table DataFrame using loc on the given dates, '2019-01-01':'2019-04-29'.
    fare_by_month= fare_by_date_df_pivot.loc['2019-01-01':'2019-04-29']
    fare_by_month
    
    # 6. Set the "date" index to datetime datatype. This is necessary to use the resample() method in Step 8.
    fare_by_month.index = pd.to_datetime(fare_by_month.index)
    
    # 7. Check that the datatype for the index is datetime using df.info()
    fare_by_month.info()
    
    # 8. Create a new DataFrame using the "resample()" function by week 'W' and get the sum of the fares for each week. 
    fare_by_month_df=fare_by_month.resample('w').sum()
    fare_by_month_df
    
output 2.
![image](https://user-images.githubusercontent.com/85843030/127343445-87e3ce03-a870-489a-967c-5adbe7c75441.png)

    
    
    # 9. Using the object-oriented interface method, plot the resample DataFrame using the df.plot() function. 

    # Import the style from Matplotlib.
    from matplotlib import style

    # Use the graph style fivethirtyeight.
    style.use('fivethirtyeight')

    montley_fares = fare_by_month_df.plot( figsize = (30,6))
    montley_fares.set_xlabel("Month")
    montley_fares.set_ylabel("Fare($USD)")

    # Add a title 
    montley_fares.set_title("Total Fare by City")
    plt.savefig("Resources/PyBer_fare_summary.png")
    plt.show()
    
  Output 3.
  ![image](https://user-images.githubusercontent.com/85843030/127343583-5fb1e842-ae70-46bf-a509-67658b3cb6ee.png)

  
## 4. Results

    Lokking at graph in output 3, it can be seen that the fares in the rural areas are far less than the fares in the urban city areas.
    The graphs show quite a steady continious curves, where there are no massive peaks to suggest any abnormal behaviour in trend.
    Overall, the fares in the urban city areas are much higher than those for rural.
    
