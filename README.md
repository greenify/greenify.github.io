# Greenify Community Features

## About prescription

Prescription in Greenify is a precisely targeted rule to block unwelcome behaviors in any Android apps. It is published in open source by any community member on GitHub, and could then be imported into Greenify by any user.

The prescription feature requires Greenify working in either root mode on Android 4.4 to 7.x, or non-root mode on Android 4.4 to 5.x.

## What could prescription do?

Prescription may block various types of unwelcome behaviors, and it's still evolving with more capabilities coming in the near future.

* Broadcast behavior
* Service binding / starting behavior
* Activity launching behavior

For safety reason, all prescriptions are **only applied to cross-user-app behaviors at present, excluding those originated from Android system and privilged system apps**. This limit will be lifted in the near future with support for finer directional control.

## How to write a prescription

Prescript is written in XML with versioned schema (defined by `xmlns`, see samples below) for compatibility. Newer schema version is generally supported by higher version of Greenify app.

Currently, the following two types of prescription are supported.

### Prescript - single prescription with rule content.

The XML content of prescript requires basic Android development knowledge to compose, since it involves the "intent" mechanism, which serves as the most important protocol of behavior and communication within and between Android apps.

Example: https://github.com/greenify/rx-mipush

```xml
<prescription xmlns="http://greenify.github.io/schemas/prescription/v1" type="service">
  <intent-filter>
    <action name="com.xiaomi.mipush.sdk.WAKEUP" />
  </intent-filter>
</prescription>
```

This is a simple prescript with just one intent-filter to identify the behavior which it blocks. The attribute `type="service"` indicate it applies to "service". Other supported types are "broadcast" and "activity".

A prescript may contain more than one intent-filter. If any of them matches, the behavior is blocked.

Complex prescript may also restrict the intent within specific app or component within app, defined by `package` or `class` attribute. If both attributes are supplied, then intent filters may be omitted all together. (see https://github.com/greenify/rx-baidu-sso/)

### Prescription Collection - a set of referenced prescriptions

[TODO]

## How to publish your prescriptions

All prescripts must be published with repository name that starts with "rx-" while all prescription collections must be published with repository name that starts with "rxs-".

[TODO]
