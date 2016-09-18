# rxjs-zone-operators

Note - The code for enterZone & leaveZone operators has been copied from 
[https://github.com/ngrx/core](@ngrx/core) project

```bash
npm install rxjs-zone-operators --save
```

## Reactive extensions for Zones

Learn what are zones - <https://github.com/angular/zone.js>

Learn about usage of zones in Angular 2 - <http://blog.thoughtram.io/angular/2016/02/01/zones-in-angular-2.html>

So what is the problem you may ask -

If you are here it means that you have adopted the Reactive Extensions for JavaScript in your 
Angular 2 project and are puzzled that some times change detection is not happening.

Let me give you a real example where there is a problem -

- You are building an ionic 2 application
- Since it is a mobile application you most likely are going to use cordova plugins
- Since Ionic 2 & Angular 2 makes use of Typescript you would want to have typescript apis for the various cordova plugins
- [ionic-native](https://github.com/driftyco/ionic-native) is the project that addresses this problem
- ionic-native not only provides the binding but it also does smart wrapping of apis exposed by cordova plugins
- Essentially the callback functions of plugin apis are either wrapped into Promises or Observables

The problem is that when the callbacks are fired by the cordova plugin they does not execute in the Angular 2 excution context (i.e. zone).

Most of the time you would not notice the problem (but it is there !!) because your user may interact with the user interface and by doing may get back into the angular zone.

Here is how you would solve this for apis that return Promise (when ionic-native wraps the callback into Promise)

```typescript
mycordovaAPI().then(() => {
  // here you are outside angular zone
  this.ngZone.run(() => {
    // you now entered into angular zone
  });
})
```

Now lot of cordova plugin apis are wrapped into Observables as well. The ones that are to be fired multiple times should be wrapped as Observables.

and here comes the challenge - if we do something similar to what we have done with our Promise example then we would end up breaking the operator chain (or as some one proficient in reactive extensions would say ... you are now leaving the monad !)

This is where enterZone shines as all you need to do is following 

```typescript
myCordovaAAPI().enterZone(this.ngZone).<operator_of_your_choice>().
```

Hope this makes sense.

See the real world usage of these operators in an ionic-native application [https://github.com/ksachdeva/ble-inspector/blob/master/app/services/ble-central.ts](https://github.com/ksachdeva/ble-inspector/blob/master/app/services/ble-central.ts)


















