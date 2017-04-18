# 绿色应用认证计划

为推广和促进[《绿色应用公约》](/dev/green-apps-convention.md)在国内Android应用生态中的普遍接纳，推动更多有责任感的开发团队积极加入改善Android设备体验的行列，[绿色守护（Greenify）](http://www.coolapk.com/apk/com.oasisfeng.greenify)联合国内主流的科技媒体、应用社区和传播渠道共同推出绿色应用认证计划，加大在普通用户群体中对设备体验的科普教育，并对遵守公约捍卫设备体验的优秀应用进行宣传推广，促进从用户、应用开发团队到设备厂商对公约所提倡的设备体验原则的普遍认同，弥合整个生态中日渐分裂的应用后台行为和限制约束。

# 认证要求

针对国内的复杂国情（尤其是推送渠道的碎片化问题），以维护应用的合理利益为出发点，在《绿色应用公约》的基础上，补充以下辅助内容：

1. 公约中的第1点要求仅约束应用在`AndroidManifest.xml`中声明的`targetSdkVersion`属性，不限制`minSdkVersion`属性和开发中使用的SDK版本。开发团队须自己确保应用符合[Android系统本身对`targetSdkVersion`的约束要求](https://developer.android.google.cn/reference/android/os/Build.VERSION_CODES.html#N)。

2. 针对公约中的第2点要求，可能有部分三方SDK要求必须在`AndroidManifest.xml`中声明`READ_PHONE_STATE`权限，否则拒绝工作。 **解决方法是保留权限声明，但不在运行期请求此项权限。**

   经过与相关SDK开发团队的沟通，统计和Push类SDK其实并不依赖这一权限，因为一方面不含基带的Android设备（如大部分平板电脑）都没有IMEI，另一方面有部分国内厂商的Android系统中已主动屏蔽了对真实IMEI的读取，所以三方SDK的实现中都不会强依赖IMEI。（实际上读取IMEI大多也是服务于用户身份的跨应用关联，属于SDK提供团队的利益诉求）

3. 针对公约中的第3点要求，如果应用中集成了国内的第三方SDK，则需要评估其是否存在『交叉唤醒』行为。 **建议接入轻量的开源辅助库[Project Condom](https://github.com/oasisfeng/condom)，识别和消除第三方SDK造成的交叉唤醒。** （目前市面上大部分三方推送SDK广泛存在交叉唤醒行为）

4. 针对公约中的第4点要求，请通过`dumpsys alarm`和`dumpsys jobscheduler <package name>`完成自查。

5. 针对公约中的第5点要求，认证计划建议但不强求应用在设置中为用户提供『后台纯净』的选项。最低要求是必须为绿色守护（Greenify）的用户提供这一选项。

   由于绿色守护的休眠机制原本就会屏蔽应用的所有后台行为，加入休眠清单的应用，实际上已经丧失了后台执行的能力（包括通知），以及相应可产生的UV。只要应用确保了后台纯净，则绿色守护默认将不再主动休眠此应用。应用通过提供这个选项，可与绿色守护的用户之间达成和解，从而赢得用户的信任，换取受控的后台执行能力（如`Alarm`、`JobScheduler`）。

   如果应用不在自身的设置界面中提供上述选项，则须提供一个如下所示的`Activity`：
   ```
   <activity … android:theme="@android:style/Theme.Dialog (or @style/Theme.AppCompat.Dialog)" android:excludeFromRecents="true">
     <intent-filter>
       <action android:name="com.oasisfeng.greenify.intent.action.REQUEST_LIMITED_BACKGROUND" />
     </intent-filter>
   </activity>
   ```
   如果已在应用自身的设置内提供『后台纯净』的选项，请为应用内对应的设置界面提供一个响应上述`action`的`Activity-alias`：
   ```
   <activity-alias … android:excludeFromRecents="true">
     <intent-filter>
       <action android:name="com.oasisfeng.greenify.intent.action.REQUEST_LIMITED_BACKGROUND" />
     </intent-filter>
   </activity>
   ```
   绿色守护会在用户将应用加入休眠清单后提示用户该应用提供『后台纯净』选项，通过`startActivityForResult()`启动上述`Activity`。 **此`Activity`应向用户展示一个单步骤的确认对话框，提示用户开启『后台纯净』选项可能带来的功能影响（如推送的实时性下降）。用户确认开启『后台纯净』选项后，应用需要确保完成以下几点：**

   * 通过`setActivityResult()`向绿色守护返回用户的确认结果。（开启为`RESULT_OK`，放弃为`RESULT_CANCEL`）
   * 通过`PackageManager`禁用前述`Activity` (或`alias`），此禁用状态作为绿色守护后续识别『后台纯净』模式已开启的标识。如果用户此后在应用的设置中关闭了『后台纯净』模式，则须通过`PackageManager`重新启用前述`Activity`（或`alias`）。
   * 不启动任何将在后台保持持续运行的服务（`Service`），除非应用处于『前台』状态。
   * 不再初始化会启动后台持续运行服务的三方SDK。

# 认证有效性

对应用的认证针对应用具体的版本，上述认证要求必须在任何渠道发布的应用包中得到保证。现阶段认证将以人工审核的形式进行，请在通过自验后提交即将（或已发布）的应用包给认证方。在通过认证后，应用的后续版本须继续确保符合认证要求，否则将失去认证资质。

此认证计划的要求将会顺应整体生态的发展不断优化调整，通过认证的应用须保持就认证要求的顺畅沟通渠道（至少一名直接负责实施的技术接口人），如果无法跟进认证要求的关键性调整，将保留取消认证的权力。
