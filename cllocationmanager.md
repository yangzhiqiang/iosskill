#CLLocationManager

##概要

##获取位置授权

##获取用户现在的位置

##区域监测

##确定与Beacon的距离
App可以使用`beacon ranging service`来确定距离支持 ibeacon 技术的设备的大概的距离，`Beacon ranging`也可以用来监听通过BLE广播自己的设备。在App启动此服务后，你可以接收到每一个你监测的设备的相关信息，是否在范围内，是附近还是远离，每次beacon的状态发生变化时，都会提供一个信息更新。

为了启动beaocn监测服务，创建一个`CLBeaconRegion`对象，并设定你关心的beacon的特征，并用此对象调用location mananger对象的 `startRangingBeaconsInRegion:` 方法。在 location manager 找到相应的 beacon 时，会调用 delegate 的 `locationManager:didRangeBeacons:inRegion:` 。 在一个有很多 beacon 的区域，并且用户不停移动时，此方法会被频繁的调用。为了节省电量，不要在一直监测所有的 beacon，推荐的最佳实践是，首先使用 beacon region 监测服务监测相应的 beacon，在发现相应的 beacon 后再启动 beacon 的监测，当找到需要的 beacon 或者离开相应的 beacon 区域时，停止监测。

##配置 Heading 相关的服务
在 iOS 中你可以通过 `Heading Service` 获取指南针相关的信息。实现导航或者方向信息功能App，可以通过此服务获取相应的方向信息。

为了获取此信息，需要在 delegate 中实现相应的函数，并调用 location manager 的 `startUpdatingHeading` 方法来启动。如果位置更新服务是有效的，系统会返回真正的方向信息和指南针的信息；如果位置更新服务是无效的，则只会返回指南针的方向信息。

在App处于前台时，系统会发送方向信息给App，当App处于后台时，只有开启了后台位置更新模式，并启动了位置更新服务时，系统才会发送方向信息给App。

##获取访问过的地方
在 iOS 中 `visits service` 为那些只是对用户到过特定地方感兴趣的App，提供了一个显著位置更新服务的替代品。例如，当用户到达某一个位置，停留一定时间后，在用户到达此位置后，系统会发送一个时间给App，当用户离开此位置后，会发送另一个事件给App。此服务为那些使用了显著位置更新，并且想找一个更低的电量消耗的App提供的。你不能使用此服务来实现一个依赖常规位置更新的导航App。

为了启动这个服务，你需要调用 location manager 的 `startMonitoringVisits` 方法。在生成相应的事件后，系统发送事件给 delegate 的 `locationManager:didVisit:` 方法。系统只发送启动服务之后的事件，启动之前的事件，不会发送给App。如果系统终止了App执行，在到达感兴趣的地点时，系统会重新唤醒相应的App，并接收相应的事件。

##在后台使用位置更新服务
许多App在前台可以使用的位置服务，在后台也是可以使用的，有些甚至可以重新唤醒App来处理事件。为了在后台运行多数位置服务，需要在项目的 Capabilities 页面的后台模式中，打开 `location updates` 即后台位置更新模式。对于运行你App的那些服务，你需要向用户申请 `Always` 权限。

* 标准位置更新服务，正常情况下，需要App运行在前台才会发送，运行在后台的时候，需要打开后台位置更新模式，此服务并不能重新运行你的App
* 显著位置更新服务，在前台和后台，App都可以接收到此事件。对于终止的App，此事件会重新运行你的App。此服务需要向用户申请 `Always` 权限。
* 区域监测服务，在前台和后台都可以接收到此事件，对于终止的App，此事件会重新运行你的App。此服务需要向用户申请 `Always` 权限。
* Beacon监测服务，正常情况下，需要在前台接收此事件。在后台时，只有App打开了 `location-updates ` 后台模式，并且标准位置更新服务运行的情况下，才会发送此事件到App。如果region的  notifyEntryStateOnDisplay 设定为 YES ，则唤醒设备时，App会在后台进行几秒钟的 beacon 扫描。此服务不会重新唤醒App，但是你可以通过 beacon 区域监测唤醒App。
* Heading 服务，正常情况下，App需要在前台才能接收到相应的事件。在后台时，只有App打开了 `location-updates ` 后台模式，并且标准位置更新服务运行的情况下，才会发送此事件到App。此事件并不能重新唤醒App。
* 正常情况下，App需要在前台才能接收到相应的事件。在后台时，只有App打开了 `location-updates ` 后台模式，并且标准位置更新服务运行的情况下，才会发送此事件到App。对于终止的App，此事件会重新运行你的App。此服务需要向用户申请 `Always` 权限。

打开 `location-updates ` 后台模式，以确保你的App可以在后台持续获取位置信息。在App进入后台时，系统会添加位置指示器到状态条，来让用户知道，有App在使用位置更新服务。系统仍有可能在任何时候停止这些App的运行，以获取资源和内存。

> iOS 8 及以后的系统，关闭后台App刷新，并不能阻止位置更新事件的在后台的接收，在以前的版本中，关闭后台App刷新，会阻止App接收位置更新事件。

对于会重新唤醒App的那些服务，系统会在重新唤醒App时，添加 `UIApplicationLaunchOptionsLocationKey` 到 options 词典，如果此Key出现，则你需要重新启动你的位置服务，在词典中，并不包含位置事件信息。你需要重新配置 delegate ，重新启动服务，以接收Pending的事件。在iOS 7.1 及以后，这些服务甚至在用户主动杀掉 App 后，还能重新启动 App；以前的版本中，强制杀掉App，可以阻止App重新唤醒。








