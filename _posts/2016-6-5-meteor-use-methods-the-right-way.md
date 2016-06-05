---
layout: post
title: Meteor methods in the right way
categories: [javascript, framework]
tags: [tech, meteor, server]
published: true
fullview: true
---

Warning this post is written with my poor English

# Introduction

Have been working with Meteor for almost a year and I thought I was doing everything right or almost right in Meteor. But just around 3 days ago, I realized I was doing something wrong all the time, really wrong. Though it shocked me at first, I still feel lucky that I found it.
And my failure was about how handle Meteor methods, I am going to write about it in this post.

# My failure

To demonstrate the problem I will use a context which is very likely to happen. Let say we have:

- A **User** collection
- A **Team** collection of users. A team may have many users, and a user may belong to more than one team. So their relation is [N-N](https://en.wikipedia.org/wiki/Many-to-many_(data_model))
- To solve the N-N relation of user and team there is a junction collection named **UserInTeam**

And below are some use-cases of the system, let write some methods to fulfill them:

#### Use case 1: create a new user and a new team than add that new user to that new team

    Meteor.methods({
      useCase1({userName, teamName}) {
        // check params here ...
        const userId = User.insert({
          name: userName,
        });

        const teamId = Team.insert({
          name: teamName,
        });

        return UserInTeam.insert({
          userId,
          teamId,
        });
      }
    });

#### Use case 2: create a new team and add an existing user to that new team

    Meteor.methods({
      useCase2({userId, teamName}) {
        // check params here ...

        const teamId = Team.insert({
          name: teamName,
        });

        return UserInTeam.insert({
          userId,
          teamId,
        });
      }
    });

#### Use case 3: create a new user and add that new user to an existing team

    Meteor.methods({
      useCase3({userName, teamId}) {
        // check params here ...
        const userId = User.insert({
          name: userName,
        });

        return UserInTeam.insert({
          userId,
          teamId,
        });
      }
    });

Do you see problem within three methods above? that is one of the most evil problem in programming called *Duplicate code*. This problem came as a huge surprise for me, because it is very obvious to see but somehow I missed it all the time.

Basically the above functions insert data to three different collections, and to do that they repeat some same operations at least two time. In programming we should never do this unless you are newly introduced to programming.

# ValidateMethod

I just use a very basic examples above to get you idea, in reality it will be more complicated. Say before mutating the database, we will have to check for permission only logged-in users who are admin can execute the above methods.

Now the problem is even worse, we will have at least two more if statement inside each functions to check for permission. It would look like this:

    Meteor.methods({
      useCase3({userName, teamId}) {
        // check params here ...

        const userId = Meteor.userId();

        if (!userId) {
          throw new Meteor.Error('not-authorized', 'Do not have permission')
        }

        if (!Roles.userIsInRole(userId, 'admin')) {
          throw new Meteor.Error('not-authorized', 'Do not have permission')
        }

        const userId = User.insert({
          name: userName,
        });

        return UserInTeam.insert({
          userId,
          teamId,
        });
      }
    });

And repeat three times. It seems like that only me but this tedious permission check also bothers other people, include people in the MDG. So in the new guide for Meteor, they mention this method: **ValidateMethod** comes within the package **[mdg:validated-method](https://github.com/meteor/validated-method)**.

What this method does is to provide us a way to check param input with SimpleSchema, but more importantly it also allows the use of mixins and provides an interface for defining and using mixins easily.

Depend on that easiness, many people have came in and contributed many useful mixins included: logged-in user and role checking. You should go to check yourself for more detail. And with the problem we are having, now we can solve like this:

    export const useCase3 = new ValidateMethod({
      name: 'useCase3',
      mixins: [LoggedInMixin],

      // check for logged-in user
      checkLoggedInError: {
        error: 'notLogged',
        reason: 'Do not have permission',
      },

      // check for admin role
      checkRoles: {
        roles: ['admin'],
        rolesError: {
          error: 'not-allowed',
          reason: 'Do not have permission',
        }
      },

      // check input params
      validate: new SimpleSchema({
        userName: {
          type: String,
        },
        teamId: {
          type: String,
        },
      }).validator(),

      run({userName, teamId}) {
        // mutating db operations...
      }
    });

# Take in the old best practice

So we have solved part of the problem by ValidateMethod. It saves us from writing a lot of duplicate permission checking code. Let's get back to the originally problem: the duplicate logic code.

After being aware of this problem I started to think about it, and found out that Meteor methods on server are just like event handlers on client. They all wait for some events (user behaviors on client, call operations on server ) and execute some logic code.

This similarity means that we can apply the best practice of event handlers to Meteor methods, and that is decoupling the logic code from the event listener function. Take that into account, our example should be refactored by decoupling all the mutating operation into their own functions:

    // insertUser.js
    export const insertUser(userName) {
      return User.insert({
        userName,
      });
    }

    // insertTeam.js
    export const insertTeam(teamName) {
      return Team.insert({
        teamName,
      });
    }

    // insertUserInTeam.js
    export const insertUserInTeam(userId, teamId) {
      return UserInAccount.insert({
        userId,
        teamId,
      });
    }

And use them like this:

    // useCase3.js
    import { insertUser } from 'insertUser.js';
    import { insertUserInTeam } from 'insertUserInTeam.js';

    export const useCase3 = new ValidateMethod({
      name: 'useCase3',
      // permission check with mixins
      run({userName, teamId}) {

        const userId = insertUser(userName);

        return insertUserInTeam(userId, teamId);
      }
    });


    // useCase2.js
    import { insertTeam } from 'insertTeam.js';
    import { insertUserInTeam } from 'insertUserInTeam.js';

    export const useCase2 = new ValidateMethod({
      name: 'useCase2',
      // permission check with mixins
      run({userId, teamName}) {

        const teamId = insertTeam(teamName);

        return insertUserInTeam(userId, teamId);
      }
    });

# Conclusion

With some very simple steps I am now free from my failure, from now on I will be able to write better methods with higher quality and I hope you do too. To sum it up I say this: **use ValidateMethod and treat methods like event handlers**.
