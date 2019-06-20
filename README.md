# TravelBooking

Building Micro Frontends

We will build a sample travel booking system in this article. Let’s spin up two Angular projects using CLI: travel-booking and flight-booking.

We will need a few dependencies to build and run Angular custom elements. Install the following dependencies inside flight-booking using following commands.

ng add @angular/elements

ng add ngx-build-plus
These dependencies can also be installed via NPM. @angular/elements provides support for Angular elements. ngx-build-plus is a build tool for Angular which is an extension of Angular CLI.

Note: You may need to update the version for document-register-element module to 1.8.1 in /flight-booking/package.json as described in this issue.

Let us also install http-server module inside flight-booking project.

npm i -g http-server --save
Create a component booking in flight-booking/src/app/. Let’s modify the component as follows:

flight-booking/src/app/booking/booking.component.html

<!--The content below is only a placeholder and can be replaced.-->
<div style="text-align:center">
  <a href="javascript:alert('Welcome to Flight Booking App!!');" style="font-size:25px;">{{ title }}</a>
</div>
flight-booking/src/app/booking/booking.component.ts

import { Component, OnInit } from '@angular/core';

@Component({
selector: 'app-flight-booking',
templateUrl: './booking.component.html',
styleUrls: ['./booking.component.scss']
})
export class BookingComponent implements OnInit {

title = 'Flight Booking App';

constructor() { }

ngOnInit() {
}
Let’s define the component booking as custom element in flight-booking/src/app/app.module.ts.

import { BrowserModule } from '@angular/platform-browser';
import { NgModule, Injector } from '@angular/core';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { createCustomElement } from '@angular/elements';
import { BookingComponent } from './booking/booking.component';
@NgModule({
declarations: [
AppComponent,
BookingComponent
],
imports: [
BrowserModule,
AppRoutingModule
],
providers: [],
bootstrap: [],
entryComponents: [
BookingComponent
]
})
export class AppModule {
constructor(private injector: Injector) {
}
ngDoBootstrap() {
const myCustomElement = createCustomElement(BookingComponent, { injector: this.injector });
customElements.define('app-flight-booking', myCustomElement);
}
}
To build the project in a single JS file, we need to tell angular to use ngx-build-plus module. Modify flight-booking/angular.json in three places as follows:

"architect": {   
"build": {  "builder": "ngx-build-plus:build",  ....

"serve": {   
"builder": "ngx-build-plus:dev-server",  ...
"test": {   
"builder": "ngx-build-plus:karma",
Running the project
Run the following command to build the project into a single JS file.

ng build --prod --output-hashing none --single-bundle true
–output-hashing (none) will avoid hashing the file names.

–single-bundle (true) will bundle all the compiled files into a single JS file.

Start the server.

http-server ./dist/flight-booking -p 8081
Similarly, create another custom element train-booking and run the server with port 8082.

http-server ./dist/train-booking -p 8082
Wrapping Custom Element
Let us include flight-booking and train-booking custom elements in travel-booking app. Modify /travel-booking/index.html as follows.

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>TravelBooking</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
  <div style="margin-bottom: 10px;" id="flight-booking-container"><app-flight-booking></app-flight-booking></div>
  <div id="train-booking-container"><app-train-booking></app-train-booking></div>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/zone.js/0.9.1/zone.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/webcomponentsjs/2.2.10/custom-elements-es5-adapter.js"></script>
  <script type="text/javascript" src="http://localhost:8081/main.js"></script>
  <script type="text/javascript" src="http://localhost:8082/main.js"></script>
</body>
</html>
Here, Angular requires zone.js and custom-elements-es5-adapter.js provides custom element support within the browser. We also included main.js from our custom elements.

Modify travel-booking/angular.json to override the default server port.

"serve": {
"builder": "@angular-devkit/build-angular:dev-server",
"options": {
"browserTarget": "travel-booking:build",
"port": 8080
}
…
Running Main Application
Run the application using ng serve.
