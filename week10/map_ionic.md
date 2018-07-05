# How to add and display a map on your Ionic app

You should already have an installed Ionic app, we will not go through the installation process of ionic.


### Step 1
Head over to google maps javascript API
Select and get a API KEY copy it and save in a file
At: https://developers.google.com/maps/documentation/javascript/get-api-key

### Step 2

Go to `index.html` and add
``<script src="https://maps.googleapis.com/maps/api/js?key=YOUR-API-KEY" async defer></script>``

Open up your terminal and start up ionic serve

### Step 3
Go to `home.html` and remove the content between `<ion-content>` and ad `<div #map id="map"></div>
`

### Step 4
Head over to your `home.ts` and update your
`import { Component, } from '@angular/core';`
to
`import { Component, ViewChild, ElementRef } from '@angular/core';`

 We then need to capture the reference by adding
 `@ViewChild('map') mapRef: ElementRef;`
 to the `export class HomePage`
 above the `constructor`

Add
	```
	ionViewDidLoad() {
		console.log(this.mapRef);
	}
	```
to `export class HomePage`

Go to your browser and check the console if you can find `nativeElement: div#map`

### Step 5
Now we need to display the map to do that we are going to add a function

 ```
 showMap() {
	 const location = new google.maps.LatLng(59.4024341, 17.946482400000036);
}
```
You will probably get an error for google, to resolve that you need to add
`declare var google: any;` below the imports.

Next we need to set up map options

Add
```
const options = {center: location, zoom: 15}
```
to the `showMap()` function.

Then add
```
this.map = new google.maps.Map(this.mapRef.nativeElement, options);
```
to the `showMap()` function.

Next add `this.showMap()` to the `ionViewDidLoad` and delete the `console.log`

Your code should look like this:
```
ionViewDidLoad() {
  this.showMap();
}
```
Add `map: any;` under the export class

If you look at your app on the browser nothing will be shown, you need to head over to your `home.scss` and add
`#map{height: 100%;}`

### Step 6
Add a Marker

In `home.ts` and `export class HomePage` add

```
addMarker(position, map){
	return new google.maps.Marker({
		position,
		map
	});
}
```

Then add `this.addMarker(location, this.map);` inside your showMap function.

### Step 7 Refactor

We should change the this.map for more readability.

Change  `this.map = new google.maps.Map(this.mapRef.nativeElement, options);`

to

`const map = new google.maps.Map(this.mapRef.nativeElement, options);
`

Delete `map: any;` under the export class

and change `this.addMarker(location, this.map);`

to

`this.addMarker(location, map);`

Well done now you should have a map on your ionic app!