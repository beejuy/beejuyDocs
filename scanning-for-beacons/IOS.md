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
