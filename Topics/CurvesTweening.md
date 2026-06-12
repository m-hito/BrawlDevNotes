- Refrence: https://create.roblox.com/docs/en-us/reference/engine/classes/FloatCurve

### FloatCurve: 
`A sorted list of time-value pairs that define a curve. Used to animate a single numerical value.`

| Syntaxes                                        | What it does                                                    |
| ----------------------------------------------- | --------------------------------------------------------------- |
| Instance.new("FloatCurve")                      | creates a FloatCurve instance to setKeys into                   |
| FloatCurveKey.new(time, value)                  | FloatCurveKey makes a new key takes in time and value           |
| floatCurve:setKeys(table: FloatCurveKey values) | takes in table of floatKeys to set it to the instane FloatCurve |


```lua
local floatCurve = Instance.new("FloatCurve")
-- set a table
local floatCurveKeys = {
	FloatCurveKey.new(10, 10),
	FloatCurveKey.new(45, 50),
	FloatCurveKey.new(60, 30),
	FloatCurveKey.new(80, 40)
}
floatCurve:setKeys(floatCurveKeys)
```
