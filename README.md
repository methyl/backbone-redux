backbone-redux
=========================

> ###Disclaimer   
This package is extracted from [Redbooth](https://redbooth.com) to be opensourced and announced on FutureJS 2015.  
So, the docs are not yet ready and API is still rough. But we have a huge intention to eventually make everything    right.

Easy way to keep your backbone collections and redux store in sync.

Creates reducers and listeners for your backbone collections and fires action creators on every collection change.

### Why?

* You can start migrating your apps from backbone to react+redux in no time (big blogpost about our experience is coming)
* No need to worry about migrated/legacy parts of your app being out of sync, because both are using single source of truth
* No boilerplate
* You can hide all new concepts like `reducers`, `stores`, `action creators`, `actions` and `purity` from other devs to avoid brain overloading.
* You have REST-adapter to your server out-of-the-box. Most React projects end up implementing an ad hoc, bug-ridden implementation of Backbone.Collection not only once, but for each store.
* You have separation between server-data and UI-data. The later is flat, so working with it is a plesure in React.

### How to use?
#### Auto way
> ##### Warning
This way assumes that you don't have any other reducers in your app, becauss they will be replaced with backbone ones, so if this is not you case, please read the whole Readme.

```javascript
import { createStore, compose } from 'redux';
import { devTools } from 'redux-devtools';
import { syncCollection } from 'backbone-redux';

//  Create you redux-store, include all middlewares you want.
const finalCreateStore = compose(devTools())(createStore);
const store = finalCreateStore(() => {}); // Store with just empty dumb reducer

// Now just call auto-syncer from backbone-redux
// Assuming you have collections.todos Backbone collection globally available
syncCollection({todos: collections.todos}, store);
```

You are done. Now any change to backbone collection `app.todos` will be reflected in redux store.
Store schema will look like this:

```javascript
{
  todos: {
    entities: [{id: 1, ...}, {id: 2, ...}],
    byId: {
      1: {id: 1, ...},
      2: {id: 2, ...}
    }
  }
}
```

Every object in entities array is not a Backbone model, but a result of calling `toJSON` on it + field called `__optimistic_id` which is equal to model's `cid`;

So, what is happening when you change `collections.todos`?

```
something (your legacy/new UI or anything really) changes collection.todo
  -> collection.todos emits event
    -> collectionSyncer catches it 
      -> ActionCreator emits an action
        -> Reducer creates a new state based on this action
          -> New State is stored and listeners are notified
            -> React doing its magic
```

#### Manual Artesanal Way
TODO

### Storage format
TODO
