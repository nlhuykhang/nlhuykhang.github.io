---
layout: post
title: Pigeonhole multi-version support
categories: [web, log]
tags: [web, log]
published: false
fullview: true
---


### Introduction

This proposal introduces a way to implement multi-version support for Pigeonhole system. The three components which will have this function are Dashboard, API, and Webapp. In order to have this support, we will make backend components (Dashboard backend, API) backward compatible, keep old frontend components (Dashboard frontend, Webapp frontend) as default version, and provide users a function to activate new frontend components. This feature will allow system admin to provide test versions of the system to selected users, let them try it out, and gather feedbacks.

### Implementation

This support comes on a Pigeonhole basis. Pigeonhole and all data or settings under it will have multi-version support, other things such as Dashboard User, Team, Contract are not included. The implementation will require:

- A new column in the Pigeonhole table specifies which frontend version a particular Pigeonhole should use. An empty value denotes using the default (old) version
- A mechanism to let backend components (Dashboard backend, Webapp backend) detect and serve the correct frontend version to users
- Making backend components (Dashboard backend) backward compatible with GET/POST request from default (old) version
- Updating the current deployment process to acknowledge and support the system multi-version feature

#### API

API is the simplest part because it is always backward compatible from the start. Therefore, there is no works needed to be done for it.

#### Webapp

The Webapp backend will need to detect which version a Pigeonhole is using by checking its `version` column. Ideally, this column value should be the hash commit of the version. Next, the Webapp uses this hash to create links to source files of this version and serve to users.

For example: `index.php`

```
<?php
    $defaultValue = "some_hash_value_which_is_set_during_deployment";
    $version = getPigeonholeFromApi(/*...*/)["version"];
 ?><!DOCTYPE html>
<html lang="en" dir="ltr">
    <head>
        <?php
            if (!empty($version)) {
                echo "<script src='https://static.pigeonhole.at/".$version."/bundles/jsbundle-main.js'></script>";
            } else {
                echo "<script src='https://static.pigeonhole.at/".$defaultValue."/bundles/jsbundle-main.js'></script>";
            }

         ?>
    </head>
    <body>
         <!-- ... -->
    </body>
</html>
```

#### Dashboard

Dashboard is the complicated part because it has both frontend and backend bundled together. We have been treating the Dashboard as a monolith application, this needs to change in order to implement multi-version support. Dashboard's backend and frontend need to be perceived as two separate applications even though they are housed in the same repository. The backend will always be the newest version and backward compatible with the old version, whereas the frontend's version depends on the particular Pigeonhole settings. If a Pigeonhole has no version settings, that means it is using the default version which is the old one.

For example, let's say we current have two version `old-version` and `new-version` so backend will run `new-version` and frontend will be `old-version`. We can achieve that by:

`version.yml`
```
parameters:
    # this value is set during deployment
    assets.version: "old-version"
```

`base.html.twig`
```
{% set version = null %}

{% for item in initialModels %}
    {% if item.name == "pigeonhole" and item.attributes.version is defined %}
        {% set version = item.attributes.version %}
    {% endif %}
{% endfor %}

{% if not version %}
    <script src="{{ asset('bundles/vendor.js') }}"></script>
{% else %}
    <script src='{{ "https://static.pigeonholelive.com/dashboard-two-point-oh/#{version}/bundles/vendor.js" }}'></script>
{% endif %}
```

A way to let user setup Pigeonhole's version is also need to be developed. It could be an option inside Pigeonhole settings page or manager portal.

#### CI/CD

- Option to deploy the dashboard as default or beta version, beta deploy means backend runs new version, frontend still runs all version
- A way to know which version is the current default one and separate backend and frontend version. Ideally some version files on s3

### Discussion

To make an application to always backward compatible is complicated, there will be situation when it is very hard or impossible. This section introduces common situations and how to handle them.

##### Situation 1: new feature add new columns to database
##### Situation 2: new feature remove current columns from database

### Questions

How to deploy a new version when there is currently a beta version?