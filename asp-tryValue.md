---
title: "Try Value"
description: "MethodName() starting with Try - part"
date: 2026-02-09T09:08:08+08:00
---

## Get Value by Key
- GetValueOrDefault
> .net core
- TryGetValue
> fx

### [GetValueOrDefault](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.collectionextensions.getvalueordefault)
```
Dictionary<string, string> m_kbd = new Dictionary<string, string>();
result += m_kbd.GetValueOrDefault(kbd.Key);
``` 

### [TryGetValue](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2.trygetvalue)
```
Dictionary<string, string> m_kbd = new Dictionary<string, string>();
result += m_kbd.TryGetValue(kbd.Key, out var value)? value : null;
```

## Get an integer from a string
- TryParse

### [TryParse](https://learn.microsoft.com/en-us/dotnet/api/system.int32.tryparse)
```
string itostr(string val)
{
    string result = "";
    int iVal = 0;

    if (Int32.TryParse(val, out iVal))
    {
        val = "_" + val;
    }

    result = val;

    return result;
}
```

## Ref
- [The history of C#](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-version-history)
- [GetValueOrDefault available for IReadOnlyDictionary but not IDictionary](https://github.com/dotnet/runtime/issues/23651#issuecomment-332634353)
- [C# System.Collections.Generic cannot use GetValueOrDefault method](https://stackoverflow.com/a/67204390)
