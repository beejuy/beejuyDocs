FORMAT: 1A
HOST: https://api.beejuy.com/

Beejuy API
==========

Beejuy created an API to serve and interact with the Beacons for third party applications that want to use the services provided by Beejuy. 


## API conventions
We strive to make it as easy as possible to retrieve offer data from Beejuy and get you up and running in no time.

Here are some rule of thumb items to follow when working with Beejuy's API

> + 'Client' refers to the system attempting to communicate with Beejuy's API
> + All requests and responses are in JSON format
> + Every request will be authenticated
> + We use Basic authentication using base64 encode of your APIKEY and your SECRETKEY
> + You can have multiple APIKEYs in order to manage multiple applications
> + Your APIKEY and SECRETKEY are only available from your user's dashboard

## Testing our API
We make it as easy as possible to test our API without having to register for an account.  
Use a mock server and get responses just as if you were accesing our server.
Get sample code in multiple languages and read our documents in a developer friendly format

> Just go to http://docs.beejuy.apiary.io/#

## Authentication
To secure the API we use HTTP Basic Authentication over SSL. 
On every request you make to the API you will need to include the header 
Authorization using the Basic authentication.

    Authorization: Basic {TOKEN}
            
TOKEN (string) - string base 64 encode of the APIKEY and SECRET separated by colon.
            
    base64(APIKEY + ':' + SECRET)

Let's assume the following values
+ APIKEY =  “eb9SQx7s3KEu7Hm1qsg”
+ SECRET = “OsgabyCUtq2JYMIZaog”

You need to encode the String: “eb9SQx7s3KEu7Hm1qsg:OsgabyCUtq2JYMIZaog”

    base64(eb9SQx7s3KEu7Hm1qsg + ':' + OsgabyCUtq2JYMIZaog)
    
and the header then becomes
    
    Authorization: Basic ZWI5U1F4N3MzS0V1N0htMXFzZzpPc2dhYnlDVXRxMkpZTUlaYW9n

## Scanning for beacons

### IOS

Here is a simple snippet that will allow your application to discover the Beacons in iOS using Objective-C

Detecting iBeacons is done through the Core Location Framework
First we need to import the CoreLocation Framework:

    #import <CoreLocation/CoreLocation.h>
    
Then we need to add some properties:

    @property (strong, nonatomic) CLBeaconRegion *beaconRegion;
    @property (strong, nonatomic) CLLocationManager *locationManager;
    
In the ViewDidLoad method we need to add the following:

    self.locationManager = [[CLLocationManager alloc] init];
    self.locationManager.delegate = self;
    [self initRegion];
    
We also need to set the CLLocationManagerDelegate to the Controller you are using:

    @interface YourController : UIViewController <CLLocationManagerDelegate>
    
Now you will need to add an initRegion method . That is called from the viewDidLoad

    - (void)initRegion {
        NSUUID *uuid = [[NSUUID alloc] initWithUUIDString:@"11111111-1111-1111-1111-111111111111"];
        self.beaconRegion = [[CLBeaconRegion alloc] initWithProximityUUID:uuid identifier:@"com.devfright.myRegion"];
        [self.locationManager startMonitoringForRegion:self.beaconRegion];
    }
    
>Remember to replace “11111111-1111-1111-1111-111111111111” with the Beacon UUID that is returned from the method: /beacon-identifier 

Now we are monitoring the Region for that specific BeaconUUID and we need to tell the application what it will happen when it finds the beaconUUID

You need to add these two methods:

    - (void)locationManager:(CLLocationManager *)manager didEnterRegion:(CLRegion *)region {
        [self.locationManager startRangingBeaconsInRegion:self.beaconRegion];
        NSLog(@“Start Ranging Beacons”);
    
    }
    -(void)locationManager:(CLLocationManager *)manager didExitRegion:(CLRegion *)region {
        [self.locationManager stopRangingBeaconsInRegion:self.beaconRegion];
        NSLog(@“Stop Ranging Beacons”);
    }
    
The first method is the didEnterRegion: method. When you start picking up the transmitter (ie, you are close enough to it), this method is called. What we do here is then call the startRangingBeaconsInRegion: method on locationManager. We specify the beacon region that we created earlier in this class (self.beaconRegion).

On the second method, we do exactly the same bit we tell the app to stopRangingBeaconsInRegion: instead.

Lets take a look at the didRangeBeacons method, that is the last method you will need to add.

    -(void)locationManager:(CLLocationManager *)manager didRangeBeacons:(NSArray *)beacons inRegion:(CLBeaconRegion *)region {
    	if(beacons.count > 0) {
    		for (id arrayElement in beacons) {
    			CLBeacon *beacon = (CLBeacon *)arrayElement;
    			int beaconMajor = [nearestBeacon.major intValue];
                int beaconMinor = [nearestBeacon.minor intValue];
                NSString* beaconUUID = nearestBeacon.proximityUUID.UUIDString;
    			
    			// NOW YOU HAVE ALL THE VALUES NEEDED TO START MAKING THE API CALLS TO GET THE OFFERS FOR THE BEACONS
    			// CUSTOM CODE
    			// ......			
    		}
    	}
    }
    
Now you have all the data that you need to start making the API call to get the offers for the beacon you found.

### Android

To get started with Beacon discovery on Android there is a great library used by mostly everyone to discover Beacons, because unlike iOS this library is not included OOTB in the OS like iOS. So this library has done a great job wrapping the functionality to emulate the same behavior as the iOS version.

>Here is the tutorial: http://altbeacon.github.io/android-beacon-library/samples.html

##REST API

# Group offers
All resources related to your offers.  Get offers by id, or by beacon information

## offer [/offers/{id}]

+ Parameters
    + id (string) - the offer id
    
+ Attributes (object)
    + adType: IMAGE (string) - valid values are IMAGE, VIDEO, or TEXT 
    + description: share me and receive free coffee on us (string) - offer's description
    + link: https://beejuy.com (string) - url to point to when user clicks on the offer
    + name: coffee for sharing (string) - offer's name
    + campaignId: '1' (string) - the offer's id
    + photoCampaign: photoUrl (string) - url pointing to the offer's image, only shown when 
    + text: Free coffee on us (string) - text for the offer if the offer type is TEXT
    + title: Get your free coffee (string) - offer's title
    + videoId: 123456 (string) - offer's video id if the offer is of type VIDEO
    + videoUrl: https://youtube.com/123456 (string) - offer's video url
    + beaconId: 5R76N (string) - unique identifier for the beacon, save this field as it will be used when saving stats

### Get offer [GET]

This method allows you to get offer details based on their unique identifier.  Useful to cache data on the device,
or when getting updated offer values

+ Request (application/json)
    
    + Headers
    
            Authorization: Basic {TOKEN}

+ Response 200 (application/json)

    + Attributes (offer)
    
    + body 
        
            {
                "adType": "IMAGE",
                "description": "share me and receive free coffee on us",
                "link": "https://beejuy.com",
                "name": "coffee for sharing",
                "campaignId": "'1'",
                "photoCampaign": "photoUrl",
                "text": "Free coffee on us",
                "title": "Get your free coffee",
                "videoId": "123456",
                "videoUrl": "https://youtube.com/123456",
                "beaconId": "5R76N"
            }

## offers [/offers]

### Get offers by beacon [GET]

get offers for the beacon being passed in the parameters

+ Request (application/json)
    
    + Headers
    
            Authorization: Basic {TOKEN}

    + Attributes (object)
        + uuid: "10008483-f999-40b0-b852-720282f87e94" (guid) - the beacons uuid
        + major: 65535 (number) - beacon major value, int between 1 and 65535
        + minor: 1 (number) - beacon minor value, int between 1 and 65535

    + Body
    
            {
                "uuid": "10008483-f999-40b0-b852-720282f87e94",
                "major": "65535",
                "minor": "1"
            }

+ Response 200 (application/json)

    + Body
    
            {
                [
                    {
                        "adType": "IMAGE", 
                        "description": "share me and receive free coffee on us",
                        "link": "https://beejuy.com",
                        "name": "coffee for sharing",
                        "campaignId": "1",
                        "photoCampaign": "photoUrl",
                        "text": "Free coffee on us",
                        "title": "Get your free coffee",
                        "videoId": "123456",
                        "videoUrl": "https://youtube.com/123456",
                        "resource": "https://api.beejuy.com/offers/1"
                    },
                    {
                        "adType": "IMAGE", 
                        "description": "share me and receive free coffee on us",
                        "link": "https://beejuy.com",
                        "name": "coffee for sharing",
                        "campaignId": "1",
                        "photoCampaign": "photoUrl",
                        "text": "Free coffee on us",
                        "title": "Get your free coffee",
                        "videoId": "123456",
                        "videoUrl": "https://youtube.com/123456",
                        "resource": "https://api.beejuy.com/offers/1"
                    }
                ]
            }

# Group users
All resources related to your end users.  Set statistics, create and update profiles, and much more

## users [/users]

### add user [POST]

This method will link the current user that exists in your application to the Beejuy System by creating a user in our database.
It allows your users to get more meaningful content based on their profile settings

+ Request (application/json)
    
    + Headers
    
            Authorization: Basic {TOKEN}

    + Attributes (user)

+ Response 201 (application/json)

    + Attributes (user)
    
    + body
    
            {
                "beejuyId": "12124545",
                "vendorId": "45616151",
                "birthdate": "'1985",
                "gender": "male",
                "maritalStatus": "married",
                "name": "Jose Delgado",
                "email": "user@beejuy.com",
                "photoUrl": "https://gravatar.com/fkalfjfjlafd",
                "deviceId": "kaaaaafjafj;daf"
            }

## user [/users/{id}]

+ Parameters
    + id (string) - the beejuy user id
    
+ Attributes (object)
    + beejuyId: 12124545 (string) - user id on beejuy's system, empty when user does not exist
    + vendorId: 45616151 (string) - user id on vendor's system
    + birthdate: '1985-01-21' (date) - user's date of birth
    + gender: male (string) - user's gender
    + maritalStatus: married (string) - user's marital status
    + name: Jose Delgado (string) - user's name
    + email: user@beejuy.com (string) - user's email
    + photoUrl: https://gravatar.com/fkalfjfjlafd (string) - url for user's profile photo
    + deviceId: kaaaaafjafj;daf (string) - GUID for the user's device

### get user [GET]

This method will returns the profile information for a given user

+ Request (application/json)
    
    + Headers
    
            Authorization: Basic {TOKEN}

+ Response 200 (application/json)

    + Attributes (user)
    
    + body
    
            {
                "beejuyId": "12124545",
                "vendorId": "45616151",
                "birthdate": "'1985",
                "gender": "male",
                "maritalStatus": "married",
                "name": "Jose Delgado",
                "email": "user@beejuy.com",
                "photoUrl": "https://gravatar.com/fkalfjfjlafd",
                "deviceId": "kaaaaafjafj;daf"
            }

### update user [PUT]

This method will update the information for a given user, we search for the user based on the beejuy id, not the vendor id

+ Request (application/json)
    
    + Headers
    
            Authorization: Basic {TOKEN}

    + Attributes (user)

+ Response 204 (application/json)
    
## interests [/users/{id}/interests]

+ Parameters
    + id (string) - the beejuy user id
    
+ Attributes (object)
    + interests: technology, music (string) - list of user interests, think of them as comma separated labels that can be used to enhance user offers

### update user interests [PUT]

This method will update the user's interests list

+ Request (application/json)
    
    + Headers
    
            Authorization: Basic {TOKEN}

    + Attributes (interests)
    
    + body
    
            {  
                'interests': 'technology, music'
            }

+ Response 204 (application/json)

## stats [/users/{id}/stats]

+ Parameters
    + id (string) - the beejuy user id
    
+ Attributes (object)
    + beaconId: 5R76N (string) - unique identifier for the beacon
    + campaignId: 1 (string) - unique offer identifier
    + beejuyUserId: 12124545 (string) - user id on beejuy system, empty when user does not exist
    + action: favorite (string) - action performed by the user, values are favorite, shared, clickedLink, watched, displayed
    + sharedOn: facebook (string) - if offer was shared, where was it shared.  values are facebook, twitter, instagram, email, whatsapp

### add stats [POST]

This method will record campaign and user statistics such as setting an offer as favorite, or sharing it on social media

+ Request (application/json)
    
    + Headers
    
            Authorization: Basic {TOKEN}

    + Attributes (stats)
    
    + body
    
            {
                'beaconId': '5R76N',
                'campaignId': '1',
                'beejuyUserId': '12124545',
                'action': 'shared',
                'sharedOn': 'facebook'
            }

+ Response 201 (application/json)

    + Attributes (stats)
    
    + body
    
            {
                'beaconId': '5R76N',
                'campaignId': '1',
                'beejuyUserId': '12124545',
                'action': 'shared',
                'sharedOn': 'facebook'
            }

# Group beacon
All resources related to getting beacon data to use with your application

## beacon [/beacons/{id}]

+ Parameters
    + id (string) - the beacon's unique idetifier.  You will receive it when searching for offers
    
+ Attributes (object)
    + beaconUuid: 10008483-f999-40b0-b852-720282f87e94 (string) - unique identifier for the beacon

### get beacon [GET]

This method will return the Beacon UUID that the application will need to use to be able to find the beacons

+ Request (application/json)
    
    + Headers
    
            Authorization: Basic {TOKEN}

+ Response 200 (application/json)

    + Attributes (beacon)
    
    + body
        
            {
                'beaconUuid': '10008483-f999-40b0-b852-720282f87e94'
            }
