# Traffic Information 🚦

## 🎉 Introduction

Using a pre-installed camera system, this traffic information project in Binh Dinh helps businesses and governments manage the security and traffic situations in the area.

## ✨ Features

- User registration, login, and logout with Auth0
- User authentication: 
+ Decentralize and safeguard `route` in accordance with permitted permissions. 
+ Redirects to `/unauthorized` if authorization is not granted, according to the check.
+ A notification informing the user that their account has been locked will be displayed if the user is locked.
- Map: a general overview of traffic cameras, traffic alerts, road event, parking lots, traffic separation, and static data. 
- Manage:
+ `Traffic alerts:` create alerts, show new alerts
+ `Traffic cameras:` a list of cameras organized by location cameras and camera groups can be added, removed, or edited using the `admin` or `superadmin` rights, CSV, Excel, and JSON data can be exported with the `admin` or `superadmin` permissions.
+ `Road event:` display a list of active construction projects, add, remove, or update construction data with `admin` or `superadmin` permissions, and export data to csv, excel, or json with the same rights
+ `Parking:` display a list of parking lots, add, remove, or update parking information with `admin` or `superadmin` rights, and export data in csv, excel, or json with the same permissions.
+ `Traffic event:` display the traffic flow list, add, remove, or change the traffic flow data with the `admin` or `superadmin` permissions, and export the data to csv, excel, or json with those permissions.
+ `Static information:` Under `admin` or `superadmin` permission, you can add, remove, or change class data static information. You can also export data in the following formats: csv, excel, and json.
- Administration:
+ ` Settings:` Under `admin` or `superadmin` permissions, configure camera status, traffic conditions, and traffic events.
+ ` Users:` display the system's user list, account statistics, and editable user permissions for `guest`,`user`, and `manager`. User `lock` and `unlock`
+ ` Groups:` display user group list, create user groups, and configure permissions to allow: `access`, `permission`, `editing`, `management` of system resources.
- Statistic:
+ `Warning statistics:` show a graph of alerts broken down by `day`, `weekly` and `monthly`.

## 💻 Using technologies
- [Angular](https://angular.io/)
- [NodeJS](https://nodejs.org/en/)
- [MongoDB](https://www.mongodb.com/)
- [Auth0](https://auth0.com/)
- another technologies.

## 👤 Client side

Project made using [Angular CLI](https://github.com/angular/angular-cli)  v13.2.5.
You must use the `npm install` command to install the modules before using the `npm start` command to launch the user-side project.
### ⚙️ Auth0 Settings
- The Auth0 module can be installed with the command.: `npm install @auth0/auth0-angular`
- Open file `app.module.ts` then set up as follows:

```
 import { BrowserModule } from '@angular/platform-browser';
 import { NgModule } from '@angular/core';
 import { AuthModule } from '@auth0/auth0-angular';
 import { AppComponent } from './app.component';

 @NgModule({
   declarations: [AppComponent],
   imports: [
     BrowserModule,
     AuthModule.forRoot({
       domain: 'YOUR_DOMAIN',
       clientId: 'YOUR_CLIENT_ID'
     }),
   ],
   bootstrap: [AppComponent],
 })
 export class AppModule {}
 ```
 
- The following steps must be taken in order to configure Auth0 user permissions.:
+ Access to Auth0: Choose Auth Pipeline ==> Rule ==> Create ==> Empty Rule ==> Create Name and paste the following code into the Script:

``` 
function (user, context, callback) {
  user.app_metadata = user.app_metadata || {};
  // You can add a Role based on what you want
  // In this case I check domain
  var addRolesToUser = function(user, cb) { 
    if (user.app_metadata.roles) {
      return cb(null, user.app_metadata.roles);
    } else {
      return cb(null, ['guest']);
    }
  };

  addRolesToUser(user, function(err, roles) {
    if (err) {
      callback(err);
    } else {
      user.app_metadata.roles = roles;
      auth0.users.updateAppMetadata(user.user_id, user.app_metadata)
        .then(function(){
        context.idToken.roles = user.app_metadata.roles; 
        user.roles = roles;
          callback(null, user, context);
        })
        .catch(function(err){
          callback(err);
        }); 
    }
  });
}
```

Then click Save change.

- The same procedures as described above for configuring user permissions using the Auth Pipeline are used to setup Auth0 to return `appmetadata` in the `access token`:
+ paste the following code into the Script:

```
function (user, context, callback) {
  const namespace = 'https://hoang0650.com';
  const assignedRoles = (context.authorization || {}).roles;
  let idTokenClaims = context.idToken || {};
  let accessTokenClaims = context.accessToken || {};
  idTokenClaims[`${namespace}/roles`] = assignedRoles;
  accessTokenClaims[`${namespace}/roles`] = assignedRoles;
  context.idToken = idTokenClaims;
  context.accessToken = accessTokenClaims;
  if (context.idToken && user.user_metadata) {
     context.idToken[namespace + '/user_metadata'] = user.user_metadata;
  }
   if (context.idToken && user.app_metadata) {
     context.idToken[namespace + '/app_metadata'] = user.app_metadata;
  }

 return callback(null, user, context);
}
```

Then click Save change.

- Setting up Auth0 such that it returns name and appmetadata in the `access token`
+ paste the following code into the Script:

```
function (user, context, callback) {
  var namespace = "https://hoang0650.com";
  user.app_metadata = user.app_metadata || {};
  user.permissions = user.permissions || {};
  //var appmeta = (context.authorization || {}).app_metadata;
  user.name = user.name || {};
  context.accessToken[`${namespace}/app_metadata`] = user.app_metadata; 
  context.accessToken[`${namespace}/name`] = user.name;
  context.accessToken[`${namespace}/permission`] = user.permissions;
  callback(null, user, context);
}
```

Then click Save change.

## 🖥 Server side

- Project made using [NodeJS](https://nodejs.org/en/)  v14.15.4.
- You must use the `npm install` command to install the modules before using the `npm start` command to launch the user-side project.

## ✅ Testing
- For `guest` account: `email` guest@gmail.com `password` Guest123@
- For `user` account: `email` user@gmail.com `password` User123@
- For `admin` account: `email` admin@gmail.com `password` Admin123@
- For `superadmin` account: `email` superadmin@gmail.com `password` Superadmin123@  
