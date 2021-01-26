---
title: Properties Configuration
sidebar: qaf_latest-sidebar
permalink: latest/properties_configuration.html
folder: latest
tags: [getting_started]
---

QAF has configuration manager that loads configuration provided using xml or properties file. It expects `application.properties` available under `resources` directory under project root. If `application.properties` not provided under default location, you can specify location using system property named `application.properties.file`.  You can provide [properties used by the framework](properties_list.html) or your test code.

To load other resource, `env.resource` prope

## System properties

Any property with `system` prefix will be added into system properties. For example:

```
system.webdriver.gecko.driver=/drivers/geckodriver
```

this will set system property `webdriver.gecko.driver`.

## Parameter Interpolation
Parameters (like ${token}) in property value(s) are automatically resolved when you retrieve property. Here is an example (properties file in this example, but xml configuration file work the same way)

```
env.name=sit
env.resources=resources/common;resources/${env.name}
```
The general syntax of a parameter is ${prefix:name}. The prefix tells Configuration manager that the parameter is to be evaluated in a certain context. The context is the current configuration instance if the prefix is missing. The following other prefix names are supported by default: 

| Prefix | Description | 
|-------|---------|
| rnd |	generates random based on provided format. Format uses `a` for alphabet and `9` for digit in resulting value 
| expr | evaluates expression

<b>Parameter Examples with prefix:</b>

```
${rnd:aaa-aaa-aaa}
${rnd:99999}
${expr:java.time.Instant.now()}
${expr:java.lang.System.currentTimeMillis()}
${expr:java.util.UUID.randomUUID()}
${expr:com.qmetry.qaf.automation.util.DateUtil.getDate(0, 'MM/dd/yyyy')}

```

When you retrieve property with parameter, If a parameter cannot be resolved, e.g. because the property with name not available or an unknown prefix is used, it won't be replaced, but is returned as is including the dollar sign and the curly braces. 

### parameter within parameter
In case you want to place parameter inside parameter you can use <% %>. For example:

```
a=c
a.b=${a}b 
c.b=cb
z.b=zb
target=${<%a%>.b} #in this case where a=c it should be ${c.b} results in cb

start.date=${expr:com.qmetry.qaf.automation.util.DateUtil.getDate(1, 'MM/dd/yyyy')} #tomorrow in MM/dd/yyyy format
start.date=${expr:com.qmetry.qaf.automation.util.DateUtil.getDate(<%rnd:9%>, 'MM/dd/yyyy')} #random date from 10 days from today in MM/dd/yyyy format
```
## Accessing properties

### In Java
To access any properties in code you can use `getBundle()` method of `ConfigurationManager`.

```java
import static com.qmetry.qaf.automation.core.ConfigurationManager.getBundle();

...

String myString = getBundle().getString("my.stringprop");
int myInt = getBundle().getInt("my.intprop");

```

### In BDD

To access any properties in BDD file, you can provide parameter in step call or meta-data. For example:

```
Given user login with '${admin.user.name}' and '${admin.user.pwd}'
```

