---
layout: post
title: Permission checking with Meteor
categories: [javascript, framework]
tags: [tech, meteor, server]
published: true
fullview: true
---

Warning this post is written with my poor English

# Introduction

Recently I work on a very complicated-logic application which requires to have two roles system working together. One is the usual system's roles (e.g. admin, web master, ...), the other is the team's role system (e.g. team's member, team's manager, ...). At some point, the app requires to check user's role depend on both system. The result of that is many very long and duplicated code.

Although I have faced this problem a few times ago, it has never bothered me enough to make an attempt to refactor the code. But it does this time, the code for permission checking now is too long, complicated, and makes me sick to copy/paste them every times.

# Use smaller functions with [LoggedInMixin](https://github.com/nabiltntn/loggedin-mixin/), [MethodHooks](https://github.com/leonidez/method-hooks/) extension

The method I use to refactor is pretty simple, and I think everyone is familiar with it, that is to make the function smaller by separate the code to small function. Take this methods as an example:

    export const bloggingSunday = new ValidatedMethod({
      // other property ...
      run(data) {

        const userId = Meteor.userId();

        if (!userId) {
          throw new Meteor.Error('not-authorized');
        }

        if (Roles.isUserInRoles(userId, 'admin')) {
          throw new Meteor.Error('not-authorized');
        }

        const team = Team.findOne(data.teamId);

        if (!team || !team.isUserInTeam(userId) || !team.isUserTeamManager(userId) ) {
          throw new Meteor.Error('not-authorized');
        }

        // logic code
      },
    });

As we can see the code for checking permission in the method above is pretty complex, though I already use some helpers to make the code simpler. This is just an example, in the real app I have to write much complicated logic for checking. So that to save myself a lot of time and headaches I separate all the permission checking code to small function and also utilize some of the ValidatedMethod's extension packages.


    // checkIsUserInTeam.js
    export const checkIsUserInTeam = function(data) {
      const team = Team.findOne(data.teamId);

      if (!team || !team.isUserInTeam(userId)) {
        throw new Meteor.Error('not-authorized');
      }
    };

    // checkIsUserTeamManager.js
    export const checkIsUserTeamManager = function(data) {
      const team = Team.findOne(data.teamId);

      if (!team || !team.isUserTeamManager(userId) ) {
        throw new Meteor.Error('not-authorized');
      }
    };

    // import dependency ...
    export const bloggingSunday = new ValidatedMethod({
      // other property ...
      mixins: [LoggedInMixin, MethodHooks],
      beforeHooks: [checkIsUserInTeam, checkIsUserTeamManager],
      checkLoggedInError: {
        error: 'not-authorized',
        reason: 'Do not have permission',
      },
      checkRoles: {
        roles: ['admin'],
        rolesError: {
          error: 'not-allowed',
          reason: 'Do not have permission',
        }
      },
      run(data) {
        // logic code ...
      },
    });

In the refactored version above, I do two main things: first I create two small functions for checking role of user in the team's scope, second I use LoggedInMixin and MethodHooks extension package of ValidatedMethod to do the real check.

LoggedInMixin is used to check if user is logged in and is admin of the system. MethodHooks add a beforeHooks property to the ValidatedMethod which helps us to run certain functions before the real function runs, so that checkIsUserInTeam and checkIsUserTeamManager run before the main function and help us take care of the checking.

Now whenever I need to check if user is member of a team or is manager of team in a method, I can simply use checkIsUserInTeam and checkIsUserTeamManager function. Not only that these function can also be used inside publication function, though you will have to them in the top of those publications because there is no before hook for publication (or maybe because of my poor knowledge :D).

# Further discussion

You may notice I do not have code for checking user's roles in the system's scope and user's login status, because I utilize the LoggedInMixin package to do that. If I have to check for these condition inside publications I will have to write these functions. So maybe getting rid of LoggedInMixin and only use MethodHooks is a better way. But for me right now, it does not really matter and with LoggedInMixin it is also clearer because just by looking at the method I know exactly what roles user need to have to be qualified without going around and look for the checking function.

With speed is one of the most important thing to care for in my daily workflow, I think using both methods, e.g. use LoggedInMixin for methods and checking functions for publications, will be the best choice.
