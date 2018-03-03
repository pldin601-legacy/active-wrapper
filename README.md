# ActiveWrappers

## Description
Collection of lightweight and powerful JavaScript object wrappers with event handling support.
Consist of three wrappers: **ActiveObject**, **ActiveArray** and **ActiveVariable**.

## Usage
First of all include the script into your HTML markup:

```html
<script src="js/activewrappers.min.js"></script>
```

### List of all available methods

```js
var activeVariable = new ActiveVariable(/*initial value*/);
activeVariable.onSet(function (newValue, oldValue) { ... });
activeVariable.set(/*new value*/);
activeVariable.get();

var activeObject = new ActiveObject(/*initial value*/);
activeVariable.onSet(function (property, newValue, oldValue) { ... });
activeVariable.onRemove(function (property) { ... });
activeVariable.onClear(function () { ... });
activeVariable.onGlobal(function (eventType, arrayOfArgs) { ... } /*, eventTypeFilter*/);
activeVariable.set(/*property*/, /*value*/);
activeVariable.remove(/*property*/);
activeVariable.clear();
activeVariable.get(/*property*/);
activeVariable.getKeys();
activeVariable.getObject();
activeVariable.each(function (objectEachProperty) { ... });
activeVariable.count();

var activeArray = new ActiveArray(/*initial value*/);
activeVariable.onAdd(function (newItem) { ... });
activeVariable.onRemove(function (index) { ... });
activeVariable.onUpdate(function (index, newValue, oldValue) { ... });
activeVariable.onClear(function () { ... });
activeVariable.onMove(function (oldIndex, newIndex) { ... });
activeVariable.onSwap(function (firstIndex, secondIndex) { ... });
activeVariable.onGlobal(function (eventType, arrayOfArgs) { ... } /*, eventTypeFilter*/);
activeVariable.add(/*value*/);
activeVariable.addAll(/*array of values*/);
activeVariable.addEvery(/*array of values*/);
activeVariable.update(/*index*/, /*value*/);
activeVariable.remove(/*index*/);
activeVariable.swap(/*index*/, /*index*/);
activeVariable.move(/*index*/, /*index*/);
activeVariable.clear();
activeVariable.get(/*index*/);
activeVariable.each(function (arrayEachElement) { ... });
activeVariable.count();
```

## ActiveVariable
ActiveVariable supports single kind of event - **SET** which occurs when you change internal object.

### Example of usage
```js
// Creates new ActiveVariable object with initial value '1'
var myVariable = new ActiveVariable(1);

// Registers object listener
myVariable.onSet(function(newValue, oldValue) {
    console.log("EVENT: Value changed: '" + oldValue + "' -> '" + newValue + "'");
});

console.log("Value: " + myVariable.get());
// > Value: 1

myVariable.set(10);
// > EVENT: Value changed: '1' -> '10'

console.log("Value: " + myVariable.get());
// > Value: 10
```


## ActiveObject
ActiveObject is extended object wrapper which supports next kinds of events: **SET**, **REMOVE**, **CLEAR** and **GLOBAL**

### Example of usage
```js
// Create new test object with some fields
var testObject = {
    name: "John",
    age: 23,
    email: "bob@mail.com"
};

// Wrap test object with ActiveObject
var activeObject = new ActiveObject(testObject);

activeObject.onSet(function (property, newValue, oldValue) {
    console.log("EVENT: Property '"+property+"' value changed: '"+oldValue+"' -> '"+newValue+"'");
});

activeObject.onRemove(function (property) {
    console.log("EVENT: Property '"+property+"' removed");
});

activeObject.onClear(function () {
    console.log("EVENT: Object cleared");
});

activeObject.onGlobal(function (eventId, args) {
    console.log("GLOBAL EVENT: event_id:" + eventId + " arguments: ", args);
});

console.log("Name:", activeObject.get("name"));
// > Name: John

console.log("Object:", activeObject.getObject());
// > Object: Object {name: "John", age: 23, email: "bob@mail.com"}

console.log("Keys:", activeObject.getKeys());
// > Keys: ["name", "age", "email"]

activeObject.set("age", 32);
// > EVENT: Property 'age' value changed: '23' -> '32'
// > GLOBAL EVENT: event_id:64 arguments:  ["age", 32, 23]

activeObject.set("name", "John Smith");
// > EVENT: Property 'name' value changed: 'John' -> 'John Smith'
// > GLOBAL EVENT: event_id:64 arguments:  ["name", "John Smith", "John"]

activeObject.remove("email");
// > EVENT: Property 'email' removed
// > GLOBAL EVENT: event_id:2 arguments:  ["email"]

console.log("Object:", activeObject.getObject());
// > Object: Object {name: "John Smith", age: 32}
activeObject.clear();
// > EVENT: Object cleared
// > GLOBAL EVENT: event_id:8 arguments: undefined

console.log("Object:", activeObject.getObject());
// > Object: Object {}
```

**.onGlobal** is a special event listener which listens all **ActiveObject** events,
or event specified by eventType filter.

Example:

```js
activeObject.onGlobal(function (eventId, args) {

    /* Your code */

}, EventHolderHelper.eventType.SET | EventHolderHelper.eventType.REMOVE);
```

It means that your code will be executed when either **SET** or **REMOVE** event will occurred.

### List of eventType constants

```
EventHolderHelper.eventType.ADD
EventHolderHelper.eventType.REMOVE
EventHolderHelper.eventType.UPDATE
EventHolderHelper.eventType.CLEAR
EventHolderHelper.eventType.SWAP
EventHolderHelper.eventType.MOVE
EventHolderHelper.eventType.SET
EventHolderHelper.eventType.ANY
```

To specify a more than one event use binary **OR** operator:

```
EventHolderHelper.eventType.SET | EventHolderHelper.eventType.REMOVE
```

## ActiveArray
ActiveArray is a wrapper for array of objects. It supports next event handling:
**ADD**, **UPDATE**, **REMOVE**, **CLEAR**, **SWAP**, **MOVE** and **GLOBAL**

### Example of usage
```js
var activeArray = new ActiveArray();

activeArray.onAdd(function (item) { console.log("Added:", item); });
activeArray.onRemove(function (index) { console.log("Item with index "+index+" removed"); });
activeArray.onClear(function () { console.log("Array cleared"); } );
activeArray.onUpdate(function (index, newValue, oldValue) {
    console.log("Element with index "+index+" changed: "+oldValue+" -> "+newValue);
});
activeArray.onMove(function (fromIndex, toIndex) {
    console.log("Element with index "+fromIndex+" moved to index "+toIndex);
});
activeArray.onSwap(function (firstIndex, secondIndex) {
    console.log("Elements with indexes "+firstIndex+" and "+secondIndex+" swapped");
});

activeArray.add("Hello");
// > Added: Hello

activeArray.add("World");
// > Added: World

activeArray.add(2014);
// > Added: 2014

activeArray.add("abc");
// > Added: abc

activeArray.addAll([1, 2, 3]);
// > Added: [1, 2, 3]

activeArray.addEvery([11, 12, 13]);
// > Added: 11
// > Added: 12
// > Added: 13

activeArray.move(2, 0);
// > Element with index 2 moved to index 0

activeArray.swap(1, 2);
// > Elements with indexes 1 and 2 swapped

activeArray.update(3, "ABC");
// > Element with index ABC changed: undefined -> abc

activeArray.remove(3);
// > Item with index 3 removed

activeArray.remove(0);
// > Item with index 0 removed

activeArray.clear();
// > Array cleared
```
