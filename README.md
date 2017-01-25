# About prescription

Prescription in Greenify is a precisely targeted rule to block unwelcome behaviors in any Android apps. It is published in open source by any community member on GitHub, and could then be imported into Greenify by any user. All experienced super users (especially developers) are welcome to publish and distribute composed prescriptions for the good of Android app community, to form a influence force and encourage the app developer to build for better device experience. This is the same goal of Greenify.

The prescription feature requires Greenify working in either root mode, or non-root mode but only on Android 4.4 to 5.x.

# What could prescription do?

Prescription may block various types of unwelcome behaviors, and it's still evolving with more capabilities coming in the near future.

* Broadcast behavior
* Service binding / starting behavior
* Activity launching behavior

For safety reason, all prescriptions are **only applied to cross-user-app behaviors at present, excluding those originated from Android system and privilged system apps**. This limit will be lifted in the near future with support for finer directional control.

# How to write a prescription

Prescript is written in XML with versioned schema (defined by `xmlns`, see samples below) for compatibility. Newer schema version is generally supported by higher version of Greenify app.

Currently, the following two types of prescription are supported.

## Prescript - single prescription with rule content.

Compsing a prescript requires basic Android development knowledge and deep analysis in low-level app behaviors, as it is based on the "intent" mechanism, which serves as the most important protocol of behavior and communication between (and also within) Android apps.

Example: <https://github.com/greenify/rx-mipush>
```xml
<prescription xmlns="http://greenify.github.io/schemas/prescription/v1" type="service">
  <intent-filter>
    <action name="com.xiaomi.mipush.sdk.WAKEUP" />
  </intent-filter>
</prescription>
```

This is a simple prescript with just one intent-filter to identify the behavior which it blocks. The attribute `type="service"` indicate it applies to "service". (other supported types are "broadcast" and "activity")

A prescript may contain more than one intent-filter. If any of them matches, the behavior is blocked. Complex prescript may also restrict the intent matching to specific app or specific component within app, defined by `package` or `class` attribute of `<prescription>` tag. (see <https://github.com/greenify/rx-baidu-sso/>) If both attributes are supplied, then intent filters may be omitted all together to block a component completely.

## Prescription Collection - a set of referenced prescriptions

Prescription collection may include prescripts and other prescription collections, all referenced by relative repository path.

Example: <https://github.com/oasisfeng/rxs-push-services>

```xml
<prescriptions xmlns="http://greenify.github.io/schemas/prescription/v1">
  <prescription link="/greenify/rx-mipush" />
  <prescription link="/greenify/rx-baidu-push" />
</prescriptions>
```

# How to publish and distribute your prescriptions

All prescripts must be published on GitHub with repository name that starts with "rx-" while all prescription collections must be published with repository name that starts with "rxs-".

The `README` is optional but highly recommended to complement the short description with more detailed explanation about your prescription, especially the behaviors it blocks and the impacts on the user experiences.

To distrubte your prescriptions, you need to use the `https://greenify.github.io/<user>/<repo>` URL instead of the GitHub repository URL, for Greenify app to recognize and import your prescription. For example, a prescription hosted at <https://github.com/oasisfeng/rxs-push-services> should be distributed as **<https://greenify.github.io/oasisfeng/rxs-push-services>**. When the latter link is clicked in web browser on the Android device with Greenify installed, Greenify will be launched to import this prescription.

# Play with the community

GitHub has a great design on community collaboration and development. You can create a repository and compose a prescription in minutes, all on the website, and make modification at any time with mobile browser on the go.

"Fork" is the spirit of GitHub and we embrace it too. You are welcome to fork prescription written by others and add your tweaks on top. If you wish, pull request is also encouraged to support each other in the community. In the future version of Greenify, we will add more recognizable interaction around the prescription forking, like switching among the forks.

If you have any questions to ask, or ideas to share, please feel free to post in our [G+ community](https://plus.google.com/communities/103850238949791125024) and [XDA forum](https://forum.xda-developers.com/apps/greenify).
