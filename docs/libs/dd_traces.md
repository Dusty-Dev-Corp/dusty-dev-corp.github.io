# About Traces

## Last update
For the last updated script, see this link : [https://dustydev.tebex.io/category/@TODO](https://dustydev.tebex.io/category/@TODO)

## Introduction
Traces is a script to help developpers about tracking user or server behavior. You can just use it like the `print` function, but only print it in console when needed.

## How to use it ?

You have four main functions usable client and server side and one command.

First of all, you need to add it to the `fxmanifest.lua` file of your resource.

```lua
-- Call it like this if you use it only client side
client_scripts {
    '@dd_trace/lib/trace.lua'
}

-- Call it like this if you use it only server side
server_scripts {
    '@dd_trace/lib/trace.lua'
}

-- Call it like this if you use it both side (client and server)
shared_script {
    '@dd_trace/lib/trace.lua'
}
```

### <Badge type="warning" text="function" />  Trace.\<logLevel>(args)
You have 4 levels of information :
- error
- warning
- info
- debug 

#### Example:
```lua
local fn = function (with, my, args)
    Trace.error('text', {table: 'content'}, 15, false, with, my, args)
    Trace.warning('text', {table: 'content'}, 15, false, with, my, args)
    Trace.info('text', {table: 'content'}, 15, false, with, my, args)
    Trace.debug('text', {table: 'content'}, 15, false, with, my, args)
end
```

Each one does basically the same thing, the difference will be in the export section. Each log is stored during 15 minutes unless you change the behavior with the `Trace.setTimer(value)` function described below.

### <Badge type="warning" text="function" /> Trace.setTimer(value)

This function allow you to change the default timer before the cleaning process of traces.

#### Example:
```lua
local fn = function (with, my, args)
    local newValue = 5 
    Trace.setTimer(newValue)
    Trace.warning('This is a warning') -- The following traces will be stored for the next 5 minutes
end
```
### <Badge type="warning" text="function" /> Trace.forceClean()

This function will clean the stored traces. Once it's called, you can not retrieve previous traces.

#### Example
```lua
local fn = function(with, my, args)
    Trace.warning('This is a warning')
    Trace.forceClean()
    -- The previous warning will not be stored anymore
end
```

### <Badge type="warning" text="function" /> Trace.export(scriptName, forcePrint)
With this function, you can retrieve the traces for a script and / or print traces. The default value for the first argument is the current script name and the default value for the second argument is `true`.
#### Example
```lua
-- First Example : No use of output, just print traces
local fn = function(with, my, args)
    Trace.warning('This is a warning')
    Trace.export(GetCurrentResourceName(), true)

    -- [[ Alternative export ]]
    -- Trace.export()
end

-- Second Example : No print, just use the output
local fn = function(with, my, args)
    Trace.warning('This is a warning')
    local traceExports = Trace.export(GetCurrentResourceName(), false)
    print(json.encode(traceExports, {indent = true}))
end
```

If you need to keep the export in other variable to display it differently, you can use `Trace.export(scriptName, forcePrint)` in your code.

### <Badge type="tip" text="command" /> `/ddd <scriptName> <serverSide>`
This command is an alias of the `Trace.export()` function. You can configure who can use it directly in the `dd_traces/server/commands.lua`. The usage of this function will print traces in the console. It can be client or server side, depends 