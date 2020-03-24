

# garmin-connect  
A powerful JavaScript library for connecting to Garmin Connect for sending and receiving health and workout data. It comes with some predefined methods to get and set different kinds of data for your Garmin account, but also have the possibility to make [custom requests](#custom-requests) `GET`, `POST` and `PUT` are currently supported. This makes it easy to implement whatever may be missing to suite your needs.

## Prerequisites
This library will require you to add a configuration file to your project root called `garmin.config.json` containing your username and password for the Garmin Connect service.
```json
{
	"username": "my.email@example.com",
	"password": "MySecretPassword" 
}
```
## How to install
```shell
$ npm install garmin-connect
```
## How to use
```js
const { GarminConnect } = require('garmin-connect');
// Create a new Carmin Connect Client
const GCClient = new GarminConnect();
// Uses credentials from garmin.config.json or uses supplied params
await GCClient.login("my.email@example.com", "MySecretPassword");
const userInfo = await GCClient.getUserInfo();
```
Now you can check `userInfo.emailAddress` to verify that your login was successful.

### Add workout
To add a custom workout, use the `addWorkout` or more specifically `addRunningWorkout`.
```js
 GCClient.addRunningWorkout('My 5k run', 5000, 'Some description');
```
Will add a running workout of 5km called 'My 5k run' and return a JSON object representing the saved workout.
### Schedule workout
To add a workout to your calendar, first find your workout and then add it to a specific date.
```js
const workouts = await GCClient.getWorkouts();
const id = workouts[0].workoutId;
GCClient.scheduleWorkout({ workoutId: id }, new Date('2020-03-24'));
```
This will add the workout to a specific date in your calendar and make it show up automatically if you're using any of the Garmin watches.
### Delete workout
Deleting a workout is very similar to [scheduling](#schedule-workout) one.
```js
const workouts = await GCClient.getWorkouts();
const id = workouts[0].workoutId;
GCClient.deleteWorkout({ workoutId: id });
```

### Custom requests
This library will handle custom requests to your active Garmin Connect session. There are a lot of different url's that is used, which means that this library probably wont cover them all. By using the network analyze tool you can find url's that are used by Garmin Connect to fetch data.

Let's assume I found a `GET` requests to the following url:
```
https://connect.garmin.com/modern/proxy/wellness-service/wellness/dailyHeartRate/22f5f84c-de9d-4ad6-97f2-201097b3b983?date=2020-03-24
```
The request can be sent using `GCClient` by running
```js
// You can get your displayName by using the getUserInfo method;
const displayName = '22f5f84c-de9d-4ad6-97f2-201097b3b983';
const url = 'https://connect.garmin.com/modern/proxy/wellness-service/wellness/dailyHeartRate/';
const dateString = '2020-03-24';
GCClient.get(displayName + url, { date: dateString });
```
and will net you the same result as using the provided way
```js
GCClient.getHeartRate();
```
Notice how the client will keep track of the url's, your user information as well as keeping the session alive.
## Limitations
For now, this library only supports the following:
* Get user info
* Get heart rate
* Set body weight