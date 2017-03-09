English|[中文](/README.chs.md)|[Italiano](/README.ita.md)

# Informazioni sulla prescrizione

La prescrizione in Greenify ha come obbiettivo il blocco dei comportamenti sgraditi da parte delle applicazioni Android. E' pubblicata in open source da parte di ogni membro della community su GitHub e può essere importata in Greenify da qualsiasi utente. Tutti gli utenti con una esperienza riguardante il super utente (sopratutto gli sviluppatori) sono benvenuti per pubblicare e distribuire prescrizioni composte per il bene della community delle applicazioni Android, per formare una forza d'influenza e coraggio nei confronti dello sviluppatore dell'applicazione per creare una migliore esperienza sul proprio dispositivo. Questo è lo stesso obbiettivo di Greenify.

La caratteristica della prescrizione necessita Greenify funzionante nella modalità root, o non-root ma solo su Android 4.4 fino a 5.x.

# Cosa può fare la prescrizione

Prescription may block various types of unwelcome behaviors in regular Android app, and it's still evolving with more capabilities coming in the near future.

* Behavior triggered by Android broadcast mechanism
* Service starting and binding (invocation) behavior
* Activity (UI) launching behavior

Some real world use-cases:

* Unnecessary power and memory consumption caused by background services or periodic behaviors, without settings in app to disable.
* Random lagging caused by lots of background processes unnecessarily launched in a short time, especially in some poorly written 3rd-party SDKs integrated by popular apps.
* Unwelcome promotions and ads, without proper settings in app to disable.
* Unwelcome or malfunctional built-in software components in the device.
* ……

# Come scrivere una prescrizione

Prescript is written in XML with versioned schema (defined by `xmlns`, see samples below) for compatibility. Newer schema version is generally supported by higher version of Greenify app.

Currently, the following two types of prescription are supported.

## Prescript - single prescription with rule content.

Compsing a prescript requires basic Android development knowledge and deep analysis in low-level app behaviors, as it is based on the `intent` mechanism, which serves as the most fundamental protocol of behavior and communication between (and also within) Android apps.

Syntax:

```xml
<prescription xmlns="http://greenify.github.io/schemas/prescription/v2"
  type="service|broadcast|activity"
  sender="other-app|other|any"
  [package="<app package name>"]
  [class="<component class name>"]>
  <intent-filter>
    ...
  </intent-filter>
</prescription>
```

or (since Greenify v3.2.0)

```xml
<prescriptions xmlns="http://greenify.github.io/schemas/prescription/v2">
  <prescription ... >
    ...
  </prescription>
  <prescription ... >
    ...
  </prescription>
  ...
</prescriptions>
```

A prescript may contain more more than one `<intent-filter>` (See [Android developer document](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) for detailed syntax). If any of them matches, the behavior is blocked. Complex prescript may also restrict the intent matching to specific app or specific component within app, defined by `package` or `class` attribute of `<prescription>` tag. (see <https://github.com/greenify/rx-baidu-sso/>) If both attributes are supplied, then intent filters may be omitted all together to block a component unconditionally.

Starting from Greenify v3.2.0 (schema version 2), prescript may also contain inline sub-prescripts. And a new attribute `sender` is introduced to limit the origination of behavior, with 3 supported types:

* `other-app` - originated from other app (default)
* `other` - originated from outside of target app itself. (including origination from system, in addition to `other-app`)
* `any` - originated anywhere. (including app-internal behaviors, in addition to `other`)

Example: <https://github.com/greenify/rx-mipush>

```xml
<prescription xmlns="http://greenify.github.io/schemas/prescription/v1" type="service">
  <intent-filter>
    <action name="com.xiaomi.mipush.sdk.WAKEUP" />
  </intent-filter>
</prescription>
```

This is a simple prescript with just one `intent-filter` to identify the behavior which it blocks. The attribute `type="service"` indicate it applies to "service".


## Prescription Collection - a set of referenced prescriptions

Prescription collection may include prescripts and other prescription collections, all referenced by relative repository path.

Example: <https://github.com/oasisfeng/rxs-push-services>

```xml
<prescriptions xmlns="http://greenify.github.io/schemas/prescription/v1">
  <prescription link="/greenify/rx-mipush" />
  <prescription link="/greenify/rx-baidu-push" />
</prescriptions>
```

# Come pubblicare e distribuire le tue prescrizioni

All prescripts must be published on GitHub with repository name that starts with "rx-" while all prescription collections must be published with repository name that starts with "rxs-".

The `README` is optional but highly recommended to complement the short description with more detailed explanation about your prescription, especially the behaviors it blocks and the impacts on the user experiences.

To distrubte your prescriptions, you need to use the `https://greenify.github.io/<user>/<repo>` URL instead of the GitHub repository URL, for Greenify app to recognize and import your prescription. For example, a prescription hosted at <https://github.com/oasisfeng/rxs-push-services> should be distributed as **<https://greenify.github.io/oasisfeng/rxs-push-services>** (the only difference is domain name). When the latter link is clicked in web browser on the Android device with Greenify installed, Greenify will be launched to import this prescription.

# Esplora e gioca con la community

GitHub has a great design on community collaboration and development. You can create a repository and compose a prescription in minutes, all on the website, and make modification at any time with mobile browser on the go.

"Fork" is the spirit of GitHub and we embrace it too. You are welcome to fork prescription written by others and add your tweaks on top. If you wish, Pull Request is also encouraged to support each other in the community. In the future version of Greenify, we will add more recognizable interaction around the prescription forking, like switching among the forks.

If you have any questions to ask, or ideas to share, please feel free to post in our [G+ community](https://plus.google.com/communities/103850238949791125024) and [XDA forum](https://forum.xda-developers.com/apps/greenify).
