---
layout: post
title: Meteor - My Handbook
categories: [meteor, javascript]
tags: [study, tech]
published: false
fullview: true
---

Ok what? so I can not write vietnamese when I type on sublime why?? such a wierd
problem? what is the source of this problem? wait, is it because I did not turn to telex mode?
<br>
Shit I did it already, so that problem I use to type vietnamese can not hook the keyboard event before sublime hmmm? what a interesting discovery :)) hey I figured out the reason at one bit wow akakak this is so fun :)
<br>
Ok so what do I do now? Write in english such a bad idea, is it? I always look for a chance to write in english to improve my english writing skill, don't I? so what can be better than this chance? :) ok why not huh? just go hehe
<br>
Ok after a very long long time, I came back here and write blog with such a bad english skill I have. I know that I am bad at english writing and also vietnamese too. So as people figure out your weakness and improve it. Therefore, nothing is going to stop me from writing this blog in english it will hurt me but makes me learned so no need to procastinate, just go.
<br>
I will write about Meteor Stack, to be more precise this will be about how to do stuff with Meteor in a very very basic form so that I can review and look at when I encouter those stuff. Let start with what I did yesterday, hmmm? what did I make yesterday? fuck me forgot already ><
<br>
Ah yes, I use google map with meteor site, there is a package help me do that. Actually, there are always package to use with Meteor, cool thing right :). But this may make you stupid so be careful use it with care :)
<br>
<h2>Google Map with Meteor</h2>

Package: meteor add dburles:google-maps

Helper:

{{> googleMap name="myMap" options=mapOptions}}

Client:

Meteor.startup(function(){
  GoogleMaps.load();
});

Template.templateName.onCreated(function(){

  GoogleMaps.ready('myMap', function(map){

    var marker = new google.maps.Marker({
          position: new google.maps.LatLng(lat, lng),
          map: map.instance
    });

    var infoWindow = new google.maps.InfoWindow({
      content: '<div>' + 'I am awesome, I know that :3' + ' Tons</div>'
    })

    marker.addListener('mouseover', function(){
      infoWindow.open(map.instance, marker);
    });

    marker.addListener('mouseout', function(){
      infoWindow.close();
    });

    google.maps.event.addListener(map.instance, 'dragend', function(e){

    });

    google.maps.event.addListener(map.instance, 'zoom_changed', function(e){

    });

  });

});

Template.templateName.helpers({
  mapOptions: function(){
    if(GoogleMaps.loaded()){
      return {
        center: new google.maps.LatLng(52.370216, 4.895168),
        zoom: 12
      };
    }
  }
});

<h2>One-to-Many Relationship</h2>

Example: Posts and Comments

Scenario 1: Comments is array inside Posts

Results:
  + All comment of post are resent when adding new comment (because DDP deals with first-level fields only)

Lessons:
  + Only embed data a inside data b when a is not changed frequently

Solutions:
  + Seperate them to 2 collections if there are something like comment changing frequently

Way too get related data:

  Meteor.publish('getSinglePost', function (id) {
    return [
      Posts.find(id),
      Comments.find({postId: id})
    ];
  });

<h2>Many-to-Many Relationship</h2>

Example: Users and Apps, users have many apps, apps haves many users

Scenario 1: userIds are stored inside apps as an array

Problems:
  + When add user to app? -> app reload but user does not know -> no reactive

Solutions: use Meteor Related

  meteor add mrt:related

  Meteor.publish('getCollaborators', function (appId) {
    var appCursor = Apps.find({_id: appId});
    this.related(function (app) {
      return Users.find({_id: {$in: app.collaborators}});
    }, appCursor);
  });

This is not a guarantee solution: performance and consistency of the package

Scenario 2: dual linking, users have app list, apps have user list

Problems:
  + Maintenance, this may create inconsistent state in database -> transantion, mongodb does not support transaction -> write yourself http://docs.mongodb.org/manual/tutorial/perform-two-phase-commits/
  + Scalability, changes will resend the hole array, mongo limits the size of array fields to 16MB(can store around a milliton id)

<h2>Tree Data Structures</h2>
need to review

<h2>A better way to count documents</h2>

<h2>this.unblock()</h2>

+ If a method will cause side effects and subsequent methods will depend on those side effects, do not invoke this.unblock

+ To use unblock inside publication: https://atmospherejs.com/meteorhacks/unblock

+ this.unblock() is useful in situations where retrieving the result of our Method being called is necessary

+ Meteor.defer() is best suited for tasks that need to be run behind the scenes. For example, a common use case for using Meteor.defer() is when sending Email. If we sent email inside of a Method and did some other things, too, it’s likely that sending our email would take a few seconds. Because sending an email is a “background” task (the email being sent doesn’t return a result we need to interact with), we can defer it until later. We still want it to be called, but it’s okay if it’s called after everything else

+ -> Use Meteor.defer() for something:
 needs to be done
 you do not care when it is executed
 it does not have return value, does not affect your system(database, other methods' prerequisites, results)

<h2>Fullcalender</h2>

Package:
  meteor add rzymek:fullcalendar

Helper:
  {{> fullcalendar calendarOptions}}

Options:

  Template.calendar.helpers
    calendarOptions: ->
      return {
        firstDay: 1
        theme: true
        events: [
          title: ''
          start: new Date y, m, 1
          backgroundColor: '#3c8dbc'
          borderColor: '#3c8dbc'
          allDay: true
        ,
          title: 'event 2'
          start: new Date y, m, 10
          backgroundColor: '#3c8dbc'
          borderColor: '#3c8dbc'
          allDay: true
        ]
      }

<h2>Migrate db to server</h2>
Assume that you already have a db server somewhere(I did not do this so let assume)

My strategy is this: manage to have db on local, then push to host. There may be a way to move from host to host directly, but this is good enough for newbie(that is me)

Prerequisites:

Commands:

  sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10

  echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.0.list

  sudo apt-get update

  sudo apt-get install -y mongodb-org=3.0.6 mongodb-org-server=3.0.6 mongodb-org-shell=3.0.6 mongodb-org-mongos=3.0.6 mongodb-org-tools=3.0.6

Fact: actually we just use mongodb-org-tools to accomplish this task :v, above commands is using in ubuntu 14.04 so just them with care if you use other OS

Show time:

  mongodump -h SOURCE_SERVER_DOMAIN --port SOURCE_DB_PORT --db SOURCE_DB -u USERNAME -p PASSWORD -d DUMPFOLDER

  mongorestore -h DES_SERVER_DOMAIN --port DES_DB_PORT --db SOURCE_DB -u USERNAME -p PASSWORD -d DUMPFOLDER

<h2>Mongo Shell</h2>
Today was an amazing day for me. In fact, I did not achieved much the main point is how I handled all of my tasks to day. Even though they are not so difficult, a newbie like me should have suffered more or less before getting through. But I made them so easily akaka, migrating db, writing mongo shell script to manipulate the db. Just this morning they are all strange things to me, now they are just knowdelge which every people should know keke. Ok that is for selfie, let write down what I learned today

  mongo HOST:PORT/DB -u USERNAME -p PASSWORD

  db
  show collections
  print()
  typeof
  cls

<h2>Google Static Map API</h2>

Create app at:
  https://console.developers.google.com

Link format:
  https://maps.googleapis.com/maps/api/staticmap?center=37.405,120.08&zoom=9&size=640x400&markers=37.405,120.08&key=API_KEY

<h2>Aggregation with Meteor</h2>

Only server-side

Package:
  meteor add meteorhacks:aggregate

Usage:
  collection.aggregate([...]);


<h2>PDFMake</h2>

{
  table: {
    widths: [1, 1, 1, 1]
    body: [
      [{col 1}, {col 2}, {col 3}, {col 4}],     //row 1
      [{}, {}, {}, {}],                         //row 2
      [{}, {}, {}, {}]                          //row 3
    ]
  }
}

{
  layout: {
    hLineColor: 'whilte',
    vLineColor: 'white'
  },
  table: {

  }
}

{
  text: '',
  bold: true,
  fillColor: 'green'
}

dd = {
  content: [{
    ...
  },{...}],

  footer: function(page, pages){
    return {
      columns: [
        '', {
          alignment: 'right',
          text: [{
              text: page.toString(),
              italics: true,
              color: 'gray'
            },
            // ' of ', {
            //   text: pages.toString(),
            //   italics: true
            // }
          ]
        }
      ],
      margin: [5, 0]
    };
  }
}

1 more column, 9 more pixels

<h2>Underscore</h2>

_.where(list, {key: value})

_.pluck(list, 'key')

_.map(list, function(item){ return item;})

_.without(list, value1, value2)

_.uniq(list)

_.object([key1, key2, key3], [value1, value2, value3])

_.chain(obj)

_.value()

<h2>useraccounts:iron-routing</h2>

<h2>Search with MongoDB</h2>

Regular expression:
  + realtime searching
  + dataset size if not more then fex thousand documents
  + partial word matching
  + save disk space

Full Test Search:
  + dataset is big, need high-performance
  + can afford more disk space
  + need language-specifix search requirements
  + do not need realtime search

  + only use full text search inside method, because oplog does not support full text search

  + Usage:

    db.entries.ensureIndex({name: "text"});

    Entries.find({$text: {$search: searchText}});


<h2>Way to modify packages</h2>
  + clone to packages folder
  + meteor add package-name
  + modify as you want

<h2>Scaling</h2>
  + Vertical scaling: improve a single server
  + Horizontal scaling: increase the number of servers

  + Scaling rule:
    - Oplog is a must
    - Run MongoDB as a replica set
    - Mongo Sharding (not available at the moment)
    - When choose servers opt for: strongest single CPU(power), same kind of instances(RAM, CPU, data center) because Meteor is single thread

<h2>Prosemirror Editor</h2>
  npm install -g babel
  npm install -g browserify

  npm install git+https://github.com/ProseMirror/prosemirror.git
  cd node_modules/prosemirror
  npm run dist

  var ProseMirror = require("prosemirror/dist/edit").ProseMirror
  require("prosemirror/dist/menu/menubar") // Load menubar module

  var editor = new ProseMirror({
    place: document.body,
    menuBar: true
  })

  browserify --outfile edit-bundle.js edit.js

<h2>Meteor External script</h2>
  Load inside render events
  $().after() a hold script element

<h2>Tracker</h2>
Use .equals in preference to .get

<h2></h2>
