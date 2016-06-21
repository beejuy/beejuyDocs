#Transmitting as beacon

This sample requires library version 2.1+ and a device with Android 5.0 and peripheral mode support. 
Note that this will transmit an open-source AltBeacon format, which will not be detectable with iOS devices using CoreLocation. 
If you wish to transmit a proprietary beacon format, see the documentation for the BeaconParser for information on how to set a proprietary beaconLayout, 
and take care to set the manufacturer field to a value that is expected by your receiving device.

```java
Beacon beacon = new Beacon.Builder()
        .setId1("2f234454-cf6d-4a0f-adf2-f4911ba9ffa6")
        .setId2("1")
        .setId3("2")
        .setManufacturer(0x0118)
        .setTxPower(-59)
        .setDataFields(Arrays.asList(new Long[] {0l}))
        .build();
BeaconParser beaconParser = new BeaconParser()
        .setBeaconLayout("m:2-3=beac,i:4-19,i:20-21,i:22-23,p:24-24,d:25-25");
BeaconTransmitter beaconTransmitter = new BeaconTransmitter(getApplicationContext(), beaconParser); 
beaconTransmitter.startAdvertising(beacon);
```
