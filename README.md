# ios-interview
The interview question for Rollout.io's IOS developer position

This mission, should you choose to accept it, is to build an application that fetches condition object from REST api and display it in a Table view. 
After I select the condition, the second screen appears and allows me to test my condition 

__Main Screen__: 
- A tableView with the content object from server/conditions
- The screen should show only condition names
- Once a user click on a condition the next screen should appear 
- get `http://server/conditions`  will return : 
```javascript 
[ 
  {
    name:”condition name”,
    _id:”unique id”
  }[, {}...]
]
```

__Second  Screen__:
- The screen should show a text field and a result of the condition 
- The condition is loaded via `http://server/conditions/:id`:
```javascript 
{ 
  name:”condition name”, 
  _id:"unique id”, 
  condition: <ConditionData>, 
  arguments: <number> 
} 
```
- The input fields can only be text fields (to reduce complexity) 
- The amount of arguments is maximum 3 (to reduce complexity in UI)

__Notes__:
- Don’t worry about UI at all, it doesn’t have to look good
- The condition parser design is important
 - It should easily support adding new operators

__Schemas__:

ConditionData schema:
```javascript
{ 
  op: “eq” | “and” | “or” | “ne” | “startsWith” ,
  args: [ <ConditionData | Value > ] 
}
```
      

Value Schema:
```javascript
{
  type: “arg”|”string”,
  date: “data"
 }
```


For example for this condition: 	`arg1 equals  “rollout.io”`

The fetched model will look like this:
```javascript
{ 
  name:”first example - simple eq operator”, 
 _id:”1”, 
  condition: {
    op: “eq”, 
    args:  [{ type: “arg”, value:1}, {type: “string”, value: “rollout.io”}]
  }
  arguments: 1 
}
``` 
 
For example for this condition 
	`arg1 not-equal  “rollout.io” &&  arg1 startsWith “ro”`
The fetched model will look like this:
```javascript
{ 
	name:”second example - and operator”, 
	_id:”1”, 
	condition: {
		op: “and”, 
		args: [{
				op: “ne”,
				args: [{ type: “arg”, value:1}, {type: “string”, value: “ rollout.io”}]
			},{
				op: “startsWith”,
				args: [{ type: “arg”, value:1}, {type: “string”, value: “ro”}]
			}
		],
	}
	arguments: 1 
}
``` 
 
For example for this condition 
arg1 equals “rollout.io” &&  arg2 equals “control your production” 
The fetched model will look like this:
```javascript
{ 
  name:”third example - 2 arguments”, 
  _id:”1”, 
  condition: {
    op: “and”, 
    args: [{
      op: “eq”,
      args: [{ type: “arg”, value:1}, {type: “string”, value: “rollout.io”}]
    },{
      op: “eq”,
      args: [{ type: “arg”, value:2}, {type: “string”, value: “control your production”}]
    }],
  }
  arguments: 2
}
```
 
 	
