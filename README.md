# ios-interview
The interview question for Rollout.io's IOS developer position

This mission, should you choose to accept it, is to build an application that fetches __string condition__ object from REST api and display it in a Table view. 
After I select the condition, the second screen appears and allows me to test my __string condition__

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
- The screen should show 2 text fields and a result of the condition 
- The condition is loaded via `http://server/conditions/:id`:
```javascript 
{ 
  name:”condition name”, 
  _id:"unique id”, 
  condition: <ConditionData>, 
} 
```
- The input fields are 2 text fields (The condition is only for NSString - to reduce complexity) 

__Notes__:
- Don’t worry about UI at all, it doesn’t have to look good
- The condition parser design is important
 - It should easily support adding new operators
- It is very important to understand the schemas here, and how condition is constructed, check out the examples 


__Schemas__:
It is important to understand the ConditionData Schema

ConditionData schema:
```javascript
{ 
  op: “eq” | “and” | “or” | “ne” | “containsString” , // operators that are allow
  args: [ <ConditionData> | <Value> | <Argument > ] // The arguments of the operators, it can be a value and argument or another condition (for example when the op is and)
}
```
      

Value Schema:
```javascript
{
  type: ”string”,
  date: String // e.g. “just some text"
}
```

Argument Schema:
```javascript
{
  type: ”argument”,
  argumentNumber: 1|2 // the actual argument 
}
```

__Example 1__
 - The Condition:
	
	```[arg1 isEqual:@"rollout.io"]```
 - In pesudo code can look like this:

	 ```equals(arg1, “rollout.io”)```
- The model will look like this:
```javascript
{ 
  name:”first example - simple eq operator”, 
 _id:”1”, 
  condition: {
    op: “eq”, 
    args:  [{ type: “argument”, argumentNumber:1}, {type: “string”, value: “rollout.io”}]
  }
}
``` 
 
__Example 2__
 - The Condition:
	
	```[arg1 isEquals: @"rollout.io"] && [arg1 containsString: @"ro"]```
 - In pesudo code can look like this:
	
	```and(equals(arg1, @"rollout.io"), constainsString(arg1, @"ro"))```
 
- The fetched model will look like this:
```javascript
{ 
	name:”second example - and operator”, 
	_id:”1”, 
	condition: {
		op: “and”, 
		args: [{
				op: “ne”,
				args: [{ type: “argument”, argumentNumber:1}, {type: “string”, value: “rollout.io”}]
			},{
				op: “containsString”,
				args: [{ type: “argument”, argumentNumber:1}, {type: “string”, value: “ro”}]
			}
		],
	}
}
``` 
 
__Example 3__
 - The Condition 

	```[arg1 isEqaul: @“rollout.io”] &&  [arg2 isEqual: @“control your production”]``` 
 - Or in pesudo code can look like this:
	
	```and(equals(arg1, “rollout.io”), equals(arg2,   @“control your production”)``` 
 
- The fetched model will look like this:
```javascript
{ 
  name:”third example - 2 arguments”, 
  _id:”1”, 
  condition: {
    op: “and”, 
    args: [{
      op: “eq”,
      args: [{ type: “argument”, argumentNumber:1}, {type: “string”, value: “rollout.io”}]
    },{
      op: “eq”,
      args: [{ type: “argument”, argumentNumber:2}, {type: “string”, value: “control your production”}]
    }],
  }
}
```
 
 	
