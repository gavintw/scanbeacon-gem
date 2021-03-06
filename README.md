# ScanBeacon gem

A ruby gem that allows you to scan for beacon advertisements using IOBluetooth (on Mac OS X), BlueZ (on Linux), or a BlueGiga BLE112 device (on mac or linux)

# Example Usage

## Install the gem
```
gem install scan_beacon
```
[Tips for installing & using on Ubuntu](https://github.com/RadiusNetworks/scanbeacon-gem/wiki/ubuntu)

## Create your scanner
``` ruby
require 'scan_beacon'
# to scan using the default device on mac or linux
scanner = ScanBeacon::DefaultScanner.new
# to scan using CoreBluetooth on a mac
scanner = ScanBeacon::CoreBluetoothScanner.new
# to scan using BlueZ on Linux (make sure you have privileges)
scanner = ScanBeacon::BlueZScanner.new
# to scan using a BLE112 device
scanner = ScanBeacon::BLE112Scanner.new
```

## Start a scan, yield beacons in a loop
``` ruby
scanner.scan do |beacons|
  beacons.each do |beacon|
    puts beacon.inspect
  end
end
```

## Set a specific scan cycle period
``` ruby
require 'scan_beacon'
scanner = ScanBeacon::BLE112Scanner.new cycle_seconds: 5
scanner.scan do |beacons|
  beacons.each do |beacon|
    puts beacon.inspect
  end
end
```

## Scan once for a set period and then return an array of beacons
``` ruby
scanner = ScanBeacon::CoreBluetoothScanner.new cycle_seconds: 2
beacons = scanner.scan
```

## Add a custom beacon layout
By default, this gem supports AltBeacon and Eddystone advertisements. But you can add a beacon parser to support other major beacon formats as well.

Example:
``` ruby
scanner = ScanBeacon::BLE112Scanner.new
scanner.add_parser( ScanBeacon::BeaconParser.new(:mybeacon, "m:2-3=0000,i:4-19,i:20-21,i:22-23,p:24-24") )
...
```

## Advertise as a beacon on Linux using BlueZ or a Mac using IOBluetooth
Example:
``` ruby
# altbeacon
beacon = ScanBeacon::Beacon.new(
  ids: ["2F234454CF6D4A0FADF2F4911BA9FFA6", 11,11],
  power: -59,
  mfg_id: 0x0118,
  beacon_type: :altbeacon
)
advertiser = ScanBeacon::DefaultAdvertiser.new(beacon: beacon)
advertiser.start
...
advertiser.stop

# Eddystone UID
beacon = ScanBeacon::Beacon.new(
  ids: ["2F234454F4911BA9FFA6", 3],
  power: -20,
  service_uuid: 0xFEAA,
  beacon_type: :eddystone_uid
)
advertiser = ScanBeacon::DefaultAdvertiser.new(beacon: beacon)
advertiser.start
...
advertiser.stop

# Eddystone URL (PhysicalWeb)
beacon = ScanBeacon::EddystoneUrlBeacon.new(
  url: "http://radiusnetworks.com",
  power: -20,
)
advertiser = ScanBeacon::DefaultAdvertiser.new(beacon: beacon)
advertiser.start
...
advertiser.stop
```


# Dependencies
To scan for beacons or advertise, you must have a Linux machine with BlueZ installed, or a Mac, or a BLE112 device plugged in to a USB port (on Mac or Linux).
