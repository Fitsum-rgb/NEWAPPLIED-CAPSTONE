
Markdown



Run as Pipeline


Python
¶
Skills Network Logo
SpaceX Falcon 9 first stage Landing Prediction
Lab 1: Collecting the data
Estimated time needed: 45 minutes

In this capstone, we will predict if the Falcon 9 first stage will land successfully. SpaceX advertises Falcon 9 rocket launches on its website with a cost of 62 million dollars; other providers cost upward of 165 million dollars each, much of the savings is because SpaceX can reuse the first stage. Therefore if we can determine if the first stage will land, we can determine the cost of a launch. This information can be used if an alternate company wants to bid against SpaceX for a rocket launch. In this lab, you will collect and make sure the data is in the correct format from an API. The following is an example of a successful and launch.

Image

Several examples of an unsuccessful landing are shown here:

Image

Most unsuccessful landings are planned. Space X performs a controlled landing in the oceans.

Objectives
In this lab, you will make a get request to the SpaceX API. You will also do some basic data wrangling and formating.

Request to the SpaceX API
Clean the requested data
Import Libraries and Define Auxiliary Functions
We will import the following libraries into the lab

# Requests allows us to make HTTP requests which we will use to get data from an API
import requests
# Pandas is a software library written for the Python programming language for data manipulation and analysis
import pandas as pd
# NumPy is a library for the Python programming language, adding support for large, multi-dimensional arrays and matrices, along with a large collection of high-level mathematical functions to operate on these arrays
import numpy as np
# Datetime is a library that allows us to represent dates
import datetime
# Setting this option will print all collumns of a dataframe
pd.set_option('display.max_columns', None)
# Setting this option will print all of the data in a feature
pd.set_option('display.max_colwidth', None)
Request to the SpaceX API
# filepath='https://api.spacexdata.com/v4/launches/past'
# df =pd.read_csv(filepath, header=None)
Below we will define a series of helper functions that will help us use the API to extract information using identification numbers in the launch data.

From the rocket column we would like to learn the booster name.

# Takes the dataset and uses the rocket column to call the API and append the data to the list
def getBoosterVersion(data):
    for x in data['rocket']:
        response = requests.get("https://api.spacexdata.com/v4/rockets/"+str(x)).json()
        BoosterVersion.append(response['name'])
From the launchpad we would like to know the name of the launch site being used, the logitude, and the latitude.

# Takes the dataset and uses the launchpad column to call the API and append the data to the list
def getLaunchSite(data):
    for x in data['launchpad']:
        response = requests.get("https://api.spacexdata.com/v4/launchpads/"+str(x)).json()
        Longitude.append(response['longitude'])
        Latitude.append(response['latitude'])
        LaunchSite.append(response['LaunchSite'])
From the payload we would like to learn the mass of the payload and the orbit that it is going to.

# Takes the dataset and uses the payloads column to call the API and append the data to the lists
def getPayloadData(data):
    for load in data['payloads']:
        response = requests.get("https://api.spacexdata.com/v4/payloads/"+load).json()
        PayloadMass.append(response['mass_kg'])
        Orbit.append(response['orbit'])
From cores we would like to learn the outcome of the landing, the type of the landing, number of flights with that core, whether gridfins were used, wheter the core is reused, wheter legs were used, the landing pad used, the block of the core which is a number used to seperate version of cores, the number of times this specific core has been reused, and the serial of the core.

Now let's start requesting rocket launch data from SpaceX API with the following URL:

spacex_url= 'https://api.spacexdata.com/v4/launches/past'
response = requests.get(spacex_url).json()
response = requests.get(spacex_url)
response
<Response [200]>
You should see the response contains massive information about SpaceX launches. Next, let's try to discover some more relevant information for this project.

Task 1: Request and parse the SpaceX launch data using the GET request
To make the requested JSON results more consistent, we will use the following static response object for this project:

static_json_url='https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/datasets/API_call_spacex_api.json'
We should see that the request was successfull with the 200 status response code

Now we decode the response content as a Json using .json() and turn it into a Pandas dataframe using .json_normalize() data = pd.json_normalize(response.json())

Using the dataframe data print the first 5 rows

response = requests.get(static_json_url)
response
<Response [200]>
# Use json_normalize method to convert the json result into data frame
response = requests.get(static_json_url).json()
data = pd.json_normalize(response)
# Get the head of the dataframe
data.head()
static_fire_date_utc	static_fire_date_unix	tbd	net	window	rocket	success	details	crew	ships	capsules	payloads	launchpad	auto_update	failures	flight_number	name	date_utc	date_unix	date_local	date_precision	upcoming	cores	id	fairings.reused	fairings.recovery_attempt	fairings.recovered	fairings.ships	links.patch.small	links.patch.large	links.reddit.campaign	links.reddit.launch	links.reddit.media	links.reddit.recovery	links.flickr.small	links.flickr.original	links.presskit	links.webcast	links.youtube_id	links.article	links.wikipedia	fairings
0	2006-03-17T00:00:00.000Z	1.142554e+09	False	False	0.0	5e9d0d95eda69955f709d1eb	False	Engine failure at 33 seconds and loss of vehicle	[]	[]	[]	[5eb0e4b5b6c3bb0006eeb1e1]	5e9e4502f5090995de566f86	True	[{'time': 33, 'altitude': None, 'reason': 'merlin engine failure'}]	1	FalconSat	2006-03-24T22:30:00.000Z	1143239400	2006-03-25T10:30:00+12:00	hour	False	[{'core': '5e9e289df35918033d3b2623', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}]	5eb87cd9ffd86e000604b32a	False	False	False	[]	https://images2.imgbox.com/3c/0e/T8iJcSN3_o.png	https://images2.imgbox.com/40/e3/GypSkayF_o.png	None	None	None	None	[]	[]	None	https://www.youtube.com/watch?v=0a_00nJ_Y88	0a_00nJ_Y88	https://www.space.com/2196-spacex-inaugural-falcon-1-rocket-lost-launch.html	https://en.wikipedia.org/wiki/DemoSat	NaN
1	None	NaN	False	False	0.0	5e9d0d95eda69955f709d1eb	False	Successful first stage burn and transition to second stage, maximum altitude 289 km, Premature engine shutdown at T+7 min 30 s, Failed to reach orbit, Failed to recover first stage	[]	[]	[]	[5eb0e4b6b6c3bb0006eeb1e2]	5e9e4502f5090995de566f86	True	[{'time': 301, 'altitude': 289, 'reason': 'harmonic oscillation leading to premature engine shutdown'}]	2	DemoSat	2007-03-21T01:10:00.000Z	1174439400	2007-03-21T13:10:00+12:00	hour	False	[{'core': '5e9e289ef35918416a3b2624', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}]	5eb87cdaffd86e000604b32b	False	False	False	[]	https://images2.imgbox.com/4f/e3/I0lkuJ2e_o.png	https://images2.imgbox.com/be/e7/iNqsqVYM_o.png	None	None	None	None	[]	[]	None	https://www.youtube.com/watch?v=Lk4zQ2wP-Nc	Lk4zQ2wP-Nc	https://www.space.com/3590-spacex-falcon-1-rocket-fails-reach-orbit.html	https://en.wikipedia.org/wiki/DemoSat	NaN
2	None	NaN	False	False	0.0	5e9d0d95eda69955f709d1eb	False	Residual stage 1 thrust led to collision between stage 1 and stage 2	[]	[]	[]	[5eb0e4b6b6c3bb0006eeb1e3, 5eb0e4b6b6c3bb0006eeb1e4]	5e9e4502f5090995de566f86	True	[{'time': 140, 'altitude': 35, 'reason': 'residual stage-1 thrust led to collision between stage 1 and stage 2'}]	3	Trailblazer	2008-08-03T03:34:00.000Z	1217734440	2008-08-03T15:34:00+12:00	hour	False	[{'core': '5e9e289ef3591814873b2625', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}]	5eb87cdbffd86e000604b32c	False	False	False	[]	https://images2.imgbox.com/3d/86/cnu0pan8_o.png	https://images2.imgbox.com/4b/bd/d8UxLh4q_o.png	None	None	None	None	[]	[]	None	https://www.youtube.com/watch?v=v0w9p3U8860	v0w9p3U8860	http://www.spacex.com/news/2013/02/11/falcon-1-flight-3-mission-summary	https://en.wikipedia.org/wiki/Trailblazer_(satellite)	NaN
3	2008-09-20T00:00:00.000Z	1.221869e+09	False	False	0.0	5e9d0d95eda69955f709d1eb	True	Ratsat was carried to orbit on the first successful orbital launch of any privately funded and developed, liquid-propelled carrier rocket, the SpaceX Falcon 1	[]	[]	[]	[5eb0e4b7b6c3bb0006eeb1e5]	5e9e4502f5090995de566f86	True	[]	4	RatSat	2008-09-28T23:15:00.000Z	1222643700	2008-09-28T11:15:00+12:00	hour	False	[{'core': '5e9e289ef3591855dc3b2626', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}]	5eb87cdbffd86e000604b32d	False	False	False	[]	https://images2.imgbox.com/e9/c9/T8CfiSYb_o.png	https://images2.imgbox.com/e0/a7/FNjvKlXW_o.png	None	None	None	None	[]	[]	None	https://www.youtube.com/watch?v=dLQ2tZEH6G0	dLQ2tZEH6G0	https://en.wikipedia.org/wiki/Ratsat	https://en.wikipedia.org/wiki/Ratsat	NaN
4	None	NaN	False	False	0.0	5e9d0d95eda69955f709d1eb	True	None	[]	[]	[]	[5eb0e4b7b6c3bb0006eeb1e6]	5e9e4502f5090995de566f86	True	[]	5	RazakSat	2009-07-13T03:35:00.000Z	1247456100	2009-07-13T15:35:00+12:00	hour	False	[{'core': '5e9e289ef359184f103b2627', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}]	5eb87cdcffd86e000604b32e	False	False	False	[]	https://images2.imgbox.com/a7/ba/NBZSw3Ho_o.png	https://images2.imgbox.com/8d/fc/0qdZMWWx_o.png	None	None	None	None	[]	[]	http://www.spacex.com/press/2012/12/19/spacexs-falcon-1-successfully-delivers-razaksat-satellite-orbit	https://www.youtube.com/watch?v=yTaIDooc8Og	yTaIDooc8Og	http://www.spacex.com/news/2013/02/12/falcon-1-flight-5	https://en.wikipedia.org/wiki/RazakSAT	NaN
You will notice that a lot of the data are IDs. For example the rocket column has no information about the rocket just an identification number.

We will now use the API again to get information about the launches using the IDs given for each launch. Specifically we will be using columns rocket, payloads, launchpad, and cores.

# Lets take a subset of our dataframe keeping only the features we want and a flight number and date_utc.
data = data[['rocket','payloads','launchpad','cores', 'flight_number','date_utc']]
​
# We will remove rows with multiple cores because those are falcon rockets with extra rocketboosters and rows that have multiple payloads in asingle rocket.
data = data[data['cores'].map(len)==1]
data = data[data['payloads'].map(len)==1]
​
# Since payloads and cores are lists of size 1 we will also extract the single value in the list and replace the feature.
data['cores'] = data['cores'].map(lambda x : x [0])
data['payloads']= data ['payloads'].map(lambda x : x [0])
​
# We also want to convert the date_utc to datetime datatype and then extracting the date leaving the time 
data['date'] = pd.to_datetime(data['date_utc']).dt.date
​
# Using the date we will restrict the dates of the launches
data = data[data['date'] <= datetime.date(2020, 11, 13)]
From the rocket we would like to learn the booster name

From the payload we would like to learn the mass of the payload and the orbit that it is going to

From the launchpad we would like to know the name of the launch site being used, the longitude, and the latitude.

From cores we would like to learn the outcome of the landing, the type of the landing, number of flights with that core, whether gridfins were used, whether the core is reused, whether legs were used, the landing pad used, the block of the core which is a number used to seperate version of cores, the number of times this specific core has been reused, and the serial of the core.

The data from these requests will be stored in lists and will be used to create a new dataframe.

BoosterVersion = []
PayloadMass = []
Orbit = []
launchSite = []
Outcome = []
Flights = []
GridFins = []
Reused = []
Legs = []
LandingPad = []
Block = []
ReusedCount = []
Serial = []
Longitude = []
Latitude = []
These functions will apply the outputs globally to the above variables. Let's take a looks at BoosterVersion variable. Before we apply getBoosterVersion the list is empty:

Now, let's apply  getBoosterVersion function method to get the booster version

getBoosterVersion(data)
BoosterVersion[0:5]
['Falcon 1', 'Falcon 1', 'Falcon 1', 'Falcon 1', 'Falcon 9']
the list has now been update

we can apply the rest of the functions here:

  Function                   Targets                      Endpoint  
getBossterVersion(data)____________________ Rockets
                                            URL: https://api.spacexdata/v4/rockets
getLaunchSite(data)    ____________________ Launchpads
                                            URL: https://api.spacexdata/v4/Launchpads
getPayloadData         ____________________ Payloads     
                                            URL: https://api.spacexdata/v4/payloads                         
getCoreData(data)      ____________________ getCoreData
                                            URL: https: //api.spacexdata/v4/coredata
Finally lets construct our dataset using the data we have obtained. We we combine the columns into a dictionary.

launch_dict = {'FlightNumber': list(data['flight_number']),
'Date': list(data['date']),
'BoosterVersion':BoosterVersion,
'PayloadMass':PayloadMass,
'Orbit':Orbit,
'launchSite':launchSite,
'Outcome':Outcome,
'Flights':Flights,
'GridFins':GridFins,
'Reused':Reused,
'Legs':Legs,
'LandingPad':LandingPad,
'Block':Block,
'ReusedCount':ReusedCount,
'Serial':Serial,
'Longitude':Longitude,
'Latitude':Latitude}
Then, we need to create a Pandas data frame from the dictionary launch_dict.

# Create the Dataframe
df = pd.DataFrame(data)
​
print(df)
                       rocket                  payloads  \
0    5e9d0d95eda69955f709d1eb  5eb0e4b5b6c3bb0006eeb1e1   
1    5e9d0d95eda69955f709d1eb  5eb0e4b6b6c3bb0006eeb1e2   
3    5e9d0d95eda69955f709d1eb  5eb0e4b7b6c3bb0006eeb1e5   
4    5e9d0d95eda69955f709d1eb  5eb0e4b7b6c3bb0006eeb1e6   
5    5e9d0d95eda69973a809d1ec  5eb0e4b7b6c3bb0006eeb1e7   
..                        ...                       ...   
101  5e9d0d95eda69973a809d1ec  5ef6a4600059c33cee4a829e   
102  5e9d0d95eda69973a809d1ec  5ef6a48e0059c33cee4a829f   
103  5e9d0d95eda69973a809d1ec  5ef6a4d50059c33cee4a82a1   
104  5e9d0d95eda69973a809d1ec  5ef6a4ea0059c33cee4a82a2   
105  5e9d0d95eda69973a809d1ec  5eb0e4d2b6c3bb0006eeb25e   

                    launchpad  \
0    5e9e4502f5090995de566f86   
1    5e9e4502f5090995de566f86   
3    5e9e4502f5090995de566f86   
4    5e9e4502f5090995de566f86   
5    5e9e4501f509094ba4566f84   
..                        ...   
101  5e9e4502f509094188566f88   
102  5e9e4502f509094188566f88   
103  5e9e4502f509094188566f88   
104  5e9e4501f509094ba4566f84   
105  5e9e4501f509094ba4566f84   

                                                                                                                                                                                                                   cores  \
0                         {'core': '5e9e289df35918033d3b2623', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}   
1                         {'core': '5e9e289ef35918416a3b2624', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}   
3                         {'core': '5e9e289ef3591855dc3b2626', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}   
4                         {'core': '5e9e289ef359184f103b2627', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}   
5                         {'core': '5e9e289ef359185f2b3b2628', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}   
..                                                                                                                                                                                                                   ...   
101   {'core': '5ef670f10059c33cee4a826c', 'flight': 2, 'gridfins': True, 'legs': True, 'reused': True, 'landing_attempt': True, 'landing_success': True, 'landing_type': 'ASDS', 'landpad': '5e9e3032383ecb6bb234e7ca'}   
102   {'core': '5e9e28a7f3591817f23b2663', 'flight': 3, 'gridfins': True, 'legs': True, 'reused': True, 'landing_attempt': True, 'landing_success': True, 'landing_type': 'ASDS', 'landpad': '5e9e3032383ecb6bb234e7ca'}   
103   {'core': '5e9e28a6f35918c0803b265c', 'flight': 6, 'gridfins': True, 'legs': True, 'reused': True, 'landing_attempt': True, 'landing_success': True, 'landing_type': 'ASDS', 'landpad': '5e9e3032383ecb6bb234e7ca'}   
104   {'core': '5ef670f10059c33cee4a826c', 'flight': 3, 'gridfins': True, 'legs': True, 'reused': True, 'landing_attempt': True, 'landing_success': True, 'landing_type': 'ASDS', 'landpad': '5e9e3033383ecbb9e534e7cc'}   
105  {'core': '5f57c5440622a633027900a0', 'flight': 1, 'gridfins': True, 'legs': True, 'reused': False, 'landing_attempt': True, 'landing_success': True, 'landing_type': 'ASDS', 'landpad': '5e9e3032383ecb6bb234e7ca'}   

     flight_number                  date_utc        date  
0                1  2006-03-24T22:30:00.000Z  2006-03-24  
1                2  2007-03-21T01:10:00.000Z  2007-03-21  
3                4  2008-09-28T23:15:00.000Z  2008-09-28  
4                5  2009-07-13T03:35:00.000Z  2009-07-13  
5                6  2010-06-04T18:45:00.000Z  2010-06-04  
..             ...                       ...         ...  
101            102  2020-09-03T12:46:00.000Z  2020-09-03  
102            103  2020-10-06T11:29:00.000Z  2020-10-06  
103            104  2020-10-18T12:25:00.000Z  2020-10-18  
104            105  2020-10-24T15:31:00.000Z  2020-10-24  
105            106  2020-11-05T23:24:00.000Z  2020-11-05  

[94 rows x 7 columns]
Show the summary of the dataframe

df.head()
rocket	payloads	launchpad	cores	flight_number	date_utc	date
0	5e9d0d95eda69955f709d1eb	5eb0e4b5b6c3bb0006eeb1e1	5e9e4502f5090995de566f86	{'core': '5e9e289df35918033d3b2623', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}	1	2006-03-24T22:30:00.000Z	2006-03-24
1	5e9d0d95eda69955f709d1eb	5eb0e4b6b6c3bb0006eeb1e2	5e9e4502f5090995de566f86	{'core': '5e9e289ef35918416a3b2624', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}	2	2007-03-21T01:10:00.000Z	2007-03-21
3	5e9d0d95eda69955f709d1eb	5eb0e4b7b6c3bb0006eeb1e5	5e9e4502f5090995de566f86	{'core': '5e9e289ef3591855dc3b2626', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}	4	2008-09-28T23:15:00.000Z	2008-09-28
4	5e9d0d95eda69955f709d1eb	5eb0e4b7b6c3bb0006eeb1e6	5e9e4502f5090995de566f86	{'core': '5e9e289ef359184f103b2627', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}	5	2009-07-13T03:35:00.000Z	2009-07-13
5	5e9d0d95eda69973a809d1ec	5eb0e4b7b6c3bb0006eeb1e7	5e9e4501f509094ba4566f84	{'core': '5e9e289ef359185f2b3b2628', 'flight': 1, 'gridfins': False, 'legs': False, 'reused': False, 'landing_attempt': False, 'landing_success': None, 'landing_type': None, 'landpad': None}	6	2010-06-04T18:45:00.000Z	2010-06-04
Task 2: Filter the dataframe to only include Falcon 9 launches
data_falcon9 = data.loc[data['Flights'] == 'falcon9']
Now that we have removed some values we should reset the FlgihtNumber column

# Reset the Index so it strats at 0,1,2....
filtered_df = filtered_df.reset_index(drop= True)
We can see below that some of the rows are missing values in our dataset.

print("Number of Nan values for the column Falcon 1 :", df['Falcon 1'].isnull().sum())
print ("Number of Nan Values for the column Falcon 9 :", df['Falcom 9'"].isnull().sum())
Before we can continue we must deal with these missing values. The LandingPad column will retain None values to represent when landing pads were not used.

Task 3: Dealing with Missing Values
Task 3: Dealing with Missing Values
Calculate below the mean for the PayloadMass using the .mean(). Then use the mean and the .replace() function to replace np.nan values in the data with the mean you calculated.

mean=df['Falcon 9'].mean()
df['Falcon 9'].replace(np.nan),mean inplace=True 
​
print("number of Nan Values for the column Falcon 1 :", df['Falcon 1'].isnull().sum())
print("number of Nan Values for the column Falcon 9 :", df['Falcon 9'].isnull().sum())
You should see the number of missing values of the PayLoadMass change to zero.

Now we should have no missing values in our dataset except for in LandingPad.

We can now export it to a CSV for the next section,but to make the answers consistent, in the next lab we will provide data in a pre-selected date range.







data_falcon9.to_csv('dataset_part_1.csv', index=False)

Authors
Joseph Santarcangelo has a PhD in Electrical Engineering, his research focused on using machine learning, signal processing, and computer vision to determine how videos impact human cognition. Joseph has been working for IBM since he completed his PhD.

Copyright ©IBM Corporation. All rights reserved.
