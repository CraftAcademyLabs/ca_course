---
title: "Google Maps on Ionic"
subtitle: "How to add and display a map on your Ionic app"
author: [Craft Academy - Coding as a Craft]
date: Version 1.0
subject: "Google Maps, Map, Ionic, Mobile"
keywords: [Ionic, Google Maps, location]
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

# How to add and display a map on your Ionic app

In this guide we will add a map in our ionic application we will do this by using the Google API. For this guide you should already have an installed Ionic app, we will not go through the installation process of Ionic in this guide. 


## Step 1
Head over to [google maps javascript API](https://developers.google.com/maps/documentation/javascript/get-api-key)
Select and get a API KEY copy it, and save in a file

## Step 2

Go over to `index.html` and add

```html
<script src="https://maps.googleapis.com/maps/api/js?key=YOUR-API-KEY" async defer></script>
```

And open up your terminal and fire up the server with:

```bash 
$ ionic serve
```

## Step 3

Go to 
`home.html` and remove the content between the `ion-content` tags

```html
<ion-content>
// remove the content here
</ion-content>
```


and add 
```html
<div #map id="map"></div>
```

## Step 4
Head over to your `home.ts` and update your

```javascript
import { Component, } from '@angular/core';
//to
import { Component, ViewChild, ElementRef } from '@angular/core';
```

 We then need to capture the reference by adding

```javascript
 @ViewChild('map') mapRef: ElementRef;
```

 to the `export class HomePage`
 above the `constructor`

Add:

```javascript

	ionViewDidLoad() {
		console.log(this.mapRef);
	}
```
to `export class HomePage`

Go to your browser and check the console if you can find `nativeElement: div#map`

## Step 5

**Display the map**

Now we need to display the map to do that we are going to add a function

```javascript
 showMap() {
	 const location = new google.maps.LatLng(59.4024341, 17.946482400000036);
}

```

You will probably get an error for google, to resolve that you need to add

```javascript

declare var google: any;

``` 
below the imports.

Next we need to set up map options

Add

```javascript

const options = {center: location, zoom: 15}

```
to the `showMap()` function.

Then add

```javascript
this.map = new google.maps.Map(this.mapRef.nativeElement, options);

```
to the `showMap()` function.

Next add `this.showMap()` to the `ionViewDidLoad` and delete the `console.log`

Your code should look like this:

```javascript

ionViewDidLoad() {
  this.showMap();
}

```
Add `map: any;` under the export class

If you look at your app on the browser nothing will be shown, you need to head over to your `home.scss` and add
`#map{height: 100%;}`

## Step 6

**Add a Marker**

In `home.ts` and `export class HomePage` add

```javascript
addMarker(position, map){
	return new google.maps.Marker({
		position,
		map
	});
}
```

Then add `this.addMarker(location, this.map);` inside your `showMap` function.

##  Step 7 
**Refactor**

We should change the this.map for more readability.

Change

```javascript
this.map = new google.maps.Map(this.mapRef.nativeElement, options);
//to
const map = new google.maps.Map(this.mapRef.nativeElement, options);
```

Delete
 ```javascript
 map: any;
 ``` 
 under the export class and change
```javascript
this.addMarker(location, this.map);
//to
this.addMarker(location, map);
```
Well done now you should have a map on your ionic app!