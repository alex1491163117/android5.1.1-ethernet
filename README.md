#android5.1.1 add ethernet

[原文地址](https://blog.csdn.net/hclydao/article/details/50972932)

android5.0以上的系统自带了ethernet service，默认开机就会启动，同时使用ip获取方式是动态分配，这里记录下android5.1增加ethernet设置界面设置ip获取方式及开关.

首先是界面方面要修改Settings增加ethernet设置界面

修改文件packages/apps/Settings/res/xml/dashboard_categories.xml在蓝牙后面加上如下代码
```java
		<!-- ethernet add by hclydao-->
		<dashboard-tile
		    android:id="@+id/ethernet_settings"
		    android:icon="@drawable/ic_settings_dock"
		    android:fragment="com.android.settings.ethernet.EthernetSettings"
		    android:title="@string/ethernet_settings" />
```
其中的EthernetSettings后面进行说明，然后增加string修改文件packages/apps/Settings/res/values/strings.xml

增加如下内容
```java
    <!-- Eth settings title add by hclydao-->
    <string name="ethernet_settings">Ethernet</string>
    <!-- Ethernet configuration dialog add by hcldyao-->
    <string name="eth_config_title">Configure Ethernet device</string>
    <string name="eth_setting">Ethernet</string>
    <string name="eth_dev_list">Ethernet Devices:</string>
    <string name="eth_con_type">Connection Type</string>
    <string name="eth_con_type_dhcp">DHCP</string>
    <string name="eth_con_type_manual">Static IP</string>
    <string name="eth_dns">DNS address</string>
    <string name="eth_gw">Gateway address</string>
    <string name="eth_ipaddr">IP address</string>
    <string name="eth_quick_toggle_title">Ethernet</string>
    <string name="eth_quick_toggle_summary">Turn on Ethernet</string>
    <string name="eth_conf_perf_title">Ethernet configuration</string>
    <string name="eth_conf_summary">Configure Ethernet devices</string>
    <string name="eth_mask">Netmask</string>
    <string name="eth_toggle_summary_off">Turn off Ethernet</string>
    <string name="eth_toggle_summary_on">Turn on Ethernet</string>
    <string name="eth_settings_error">Failed to set: Please enter the valid characters 0~255</string>
	<string name="eth_settings_empty">can\'t be empty</string>
    <!-- Label for the network prefix of the network [CHAR LIMIT=25]-->
    <string name="eth_network_prefix_length">Network prefix length</string>
```
这是我修改完成后所要加的所有的string

接着修改文件packages/apps/Settings/src/com/android/settings/SettingsActivity.java增加
```java
import com.android.settings.ethernet.EthernetSettings;//add by hclydao
```
然后在R.id.bluetooth_settings,后增加
```java
R.id.ethernet_settings,//add by hclydao
```
在BluetoothSettings.class.getName(),后增加
```java
EthernetSettings.class.getName(),//add by hclydao
```
接着修改文件packages/apps/Settings/src/com/android/settings/Settings.java

在
```java
public static class WirelessSettingsActivity extends SettingsActivity { /* empty */ }
```
后增加
```java
public static class EthernetSettingsActivity extends SettingsActivity { /* empty */ } //add by hclydao
```
这里面应该是声明与继承关系

接着修改
```java
packages/apps/Settings/AndroidManifest.xml
```
增加
```java

        <activity android:name="Settings$EthernetSettingsActivity"
                android:label="@string/ethernet_settings"
                android:taskAffinity="">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <action android:name="com.android.settings.ETHERNET_SETTINGS" />
                <action android:name="android.settings.ETHERNET_SETTINGS" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.VOICE_LAUNCH" />
                <category android:name="com.android.settings.SHORTCUT" />
            </intent-filter>
            <meta-data android:name="com.android.settings.FRAGMENT_CLASS"
                android:value="com.android.settings.ethernet.EthernetSettings" />
            <meta-data android:name="com.android.settings.TOP_LEVEL_HEADER_ID"
                android:resource="@id/ethernet_settings" />
            <meta-data android:name="com.android.settings.PRIMARY_PROFILE_CONTROLLED"
                android:value="true" />
        </activity>
```
这三个文件按照wifi的代码改就行了。

然后增加点击进去后的布局文件,增加
```java
packages/apps/Settings/res/xml/ethernet_settings.xml
```
内容如下
```java
<?xml version="1.0" encoding="utf-8"?>
<!-- Copyright (C) 2010 The Android Open Source Project

     Licensed under the Apache License, Version 2.0 (the "License");
     you may not use this file except in compliance with the License.
     You may obtain a copy of the License at

          http://www.apache.org/licenses/LICENSE-2.0

     Unless required by applicable law or agreed to in writing, software
     distributed under the License is distributed on an "AS IS" BASIS,
     WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     See the License for the specific language governing permissions and
     limitations under the License.
-->

<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android"
        android:title="@string/ethernet_settings"
        xmlns:settings="http://schemas.android.com/apk/res/com.android.settings">
        <Preference
            android:title="@string/eth_conf_perf_title"
            android:summary="@string/eth_conf_summary"
            android:key="ETHERNET_CONFIG"
            android:persistent="false" />
</PreferenceScreen>
```
然后增加了packages/apps/Settings/src/com/android/settings/ethernet/EthernetSettings.java

这是我最终的代码，这里就不贴上来了，下面我会给下载地址

编译后效果应该是这个样子的:
1.png

点击进去后会有一个Dialog布局，需要增加文件packages/apps/Settings/res/layout/eth_configure.xml
```java
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content">

    <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:padding="8dip"
            android:orientation="vertical">

            <LinearLayout
                android:id="@+id/table"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:orientation="vertical">
            </LinearLayout>

        <!-- Connection type -->
	<TextView android:id="@+id/eth_con_type"
                style="?android:attr/textAppearanceSmall"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:layout_marginTop="8dip"
                android:text="@string/eth_con_type" />
        <RadioGroup android:id="@+id/con_type"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                >
                <RadioButton android:id="@+id/dhcp_radio"
			style="?android:attr/textAppearanceSmall"
			android:layout_width="fill_parent"
			android:layout_height="wrap_content"
			android:text="@string/eth_con_type_dhcp"
                ></RadioButton>
                <RadioButton android:id="@+id/manual_radio"
			style="?android:attr/textAppearanceSmall"
			android:layout_width="fill_parent"
			android:layout_height="wrap_content"
			android:text="@string/eth_con_type_manual"
                ></RadioButton>
        </RadioGroup>

        <!-- IP address -->
        <LinearLayout android:id="@+id/enterprise_wrapper"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:padding="0dip"
            android:orientation="vertical">
                <TextView android:id="@+id/ipaddr_text"
                        style="?android:attr/textAppearanceSmall"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="8dip"
                        android:text="@string/eth_ipaddr" />
                <EditText android:id="@+id/ipaddr_edit"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="2dip"
                        android:singleLine="true" />
<!--
                <TextView android:id="@+id/netmask_text"
                        style="?android:attr/textAppearanceSmall"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="8dip"
                        android:text="@string/eth_mask" />
                <EditText android:id="@+id/netmask_edit"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="2dip"
                        android:singleLine="true" />
-->
                <TextView android:id="@+id/prefix_text"
                        style="?android:attr/textAppearanceSmall"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="8dip"
                        android:text="@string/eth_network_prefix_length" />
                <EditText android:id="@+id/prefix_edit"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="2dip"
                        android:singleLine="true" />

                <TextView android:id="@+id/dns_text"
                        style="?android:attr/textAppearanceSmall"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="8dip"
                        android:text="@string/eth_dns" />
                <EditText android:id="@+id/eth_dns_edit"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="2dip"
                        android:singleLine="true" />
                <TextView android:id="@+id/gw_text"
                        style="?android:attr/textAppearanceSmall"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="8dip"
                        android:text="@string/eth_gw" />
                <EditText android:id="@+id/eth_gw_edit"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginTop="2dip"
                        android:singleLine="true" />
        </LinearLayout>

    </LinearLayout>

</ScrollView>
```
里面包括了dhcp与static ip的选择，以及static ip的设置

然后增加Settings/src/com/android/settings/ethernet/EthernetDialog.java

以及packages/apps/Settings/src/com/android/settings/ethernet/EthernetSettings.java

这些是具体的实现，最后我会给下载地址

最后出来的效果应该是这样的:

2.png

3.png

增加这些修改和文件后，基本上就可以进行动态设置了。但是设置的关于ethernet的开关是没有作用的，所以这里增加开关的控制

修改文件frameworks/base/core/java/android/provider/Settings.java

在Settings数据库中增加一个ethernet的控制
```java
public static final String ETHERNET_ON = "ethernet_on";//add by hclydao
```
增加这个以后需要更新api才能编译过,增加这个后在ethernet-service和Settings中ethernet中进行读写操作
修改frameworks/opt/net/ethernet/java/com/android/server/ethernet/EthernetServiceImpl.java文件
在
```java
mHandler = new Handler(handlerThread.getLooper());
```
后增加如下代码
```java
		int enable = Settings.Global.getInt(mContext.getContentResolver(),Settings.Global.ETHERNET_ON,0);//add by hclydao
		if(enable != EthernetManager.ETH_STATE_ENABLED) {
			Log.i(TAG, "Ethernet is not enable");
			return;
		}
```
如果没有打开就直接返回，不启动Service后继的操作,然后增加两个接口
```java
	class TstartThread extends Thread {
		public void run() {
			Looper.prepare();
			mTracker.start(mContext, mHandler);
			mStarted.set(true);
			Looper.loop();
		}
	}

	public void Trackstart() { //add by hclydao
		new TstartThread().start();
	}

	public void Trackstop() {
		Log.i(TAG, "Stop Ethernet service");
		Thread tstopthread = new Thread(new Runnable() {
			public void run() {
				Looper.prepare();
				mTracker.stop();
				mStarted.set(false);
				Looper.loop();
			}
		});
		tstopthread.start();
	}
```
同时要修改frameworks/base/core/java/android/net/EthernetManager.java文件增加
```java
    public static final int ETH_STATE_UNKNOWN = 0;
    public static final int ETH_STATE_DISABLED = 1;
    public static final int ETH_STATE_ENABLED = 2;

    public void start() {
        try {
            mService.Trackstart();
        } catch (NullPointerException | RemoteException e) {
        }
    }

    public void stop() {
        try {
            mService.Trackstop();
        } catch (NullPointerException | RemoteException e) {
        }
    }
```
同时修改frameworks/base/core/java/android/net/IEthernetManager.aidl
增加
```java
	void Trackstart();//add by hclydao
	void Trackstop();
```
提供给设置进行状态控制,这里基本功能就实现了。
跟踪测试时发现静态ip设置的时候有时候不成功，修改文件frameworks/opt/net/ethernet/java/com/android/server/ethernet/EthernetNetworkFactory.java
增加
```java
private Handler mHandler;
```
然后在
```java
mContext = context;
```
后增加
```java
mHandler = target;//add by hclydao
```
在
```java
if (!setStaticIpAddress(config.getStaticIpConfiguration())) {
```
后增加
```java
						//if error then stop and restart add by hclydao
						if((mContext != null) && (mHandler != null)) {
							Log.d(TAG, "Setting static ip failed now restart");
							stop();
							start(mContext,mHandler);
						}
```
如果设置失败，stop后重新start
然后在
```java
if (mNMService.getInterfaceConfig(iface).hasFlag("running")) {
```
前面增加
```java
						if(!iface.equals("eth0"))//add by hclydao make sure the interface is eth0
							continue;
```
这里只设置了一个设备名，为保存是eth0所以加上这句,防止意外.

最后来增加Systemui statusbar中的状态提示,这里只增加了两种状态，一种是连接成功，一种是连接不成功，简单点来
修改frameworks/base/packages/SystemUI/res/layout/signal_cluster_view.xml
在
```java
     <View
         android:id="@+id/wifi_signal_spacer"
         android:layout_width="4dp"
```
前增加
```java
     </FrameLayout>
	<!--add by hclyado for ethernet-->
	<FrameLayout
	    android:id="@+id/ethernet_combo"
	    android:layout_height="wrap_content"
	    android:layout_width="wrap_content"
	    android:layout_marginRight="-6dp"
	    >
	    <ImageView
	        android:id="@+id/ethernet_state"
	        android:layout_height="wrap_content"
	        android:layout_width="wrap_content"
	        android:layout_alignParentRight="true"
	        android:layout_centerVertical="true"
	        android:scaleType="center"
	        />
	</FrameLayout>
```
修改文件frameworks/base/packages/SystemUI/res/values/strings.xml
增加
```java
    <!-- Content description of the Ethernet connected icon for accessibility (not shown on the screen). [CHAR LIMIT=NONE] add by hclydao-->
    <string name="accessibility_ethernet_connected">Ethernet connected.</string>
    <string name="accessibility_ethernet_disconnected">Ethernet disconnected.</string>
    <string name="accessibility_ethernet_connecting">Ethernet connecting.</string>
```
修改文件
frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/policy/NetworkControllerImpl.java
增加
```java
import android.net.EthernetManager;
```
在mWifiSignalController.notifyListeners();前面加上
```java
cluster.setEthernetIndicators(false,R.drawable.ethernet_disconnected,R.string.accessibility_ethernet_disconnected);
```
在pushConnectivityToSignals函数中的
```java
         mWifiSignalController.setInetCondition(
                 mValidatedTransports.get(mWifiSignalController.getTransportType()) ? 1 : 0);
```
后面增加
```java
//add by hclydao
        int length = mSignalClusters.size();
		int ethicon = R.drawable.ethernet_connecting;
		int ethacc = R.string.accessibility_ethernet_connecting;
		if(mValidatedTransports.get(TRANSPORT_ETHERNET)) {
			ethicon = R.drawable.ethernet_connected;
			ethacc = R.string.accessibility_ethernet_connected;
		}
        for (int i = 0; i < length; i++) {
            mSignalClusters.get(i).setEthernetIndicators(mEthernetConnected, ethicon,ethacc);
        }
//end add
```
在
```java
void setIsAirplaneMode(boolean is, int airplaneIcon, int contentDescription);
```
后增加
```java
public void setEthernetIndicators(boolean visible, int stateIcon, int contentDescription);
```
修改文件frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/SignalClusterView.java
增加
```java
    private boolean mEthernetVisible = false;//add by hclydao
    private int mEthernetStateId = 0;
	private int mEthernetDescription;
    ViewGroup mWifiGroup,mEthernetGroup;//modify by hclydao
    ImageView mVpn, mWifi, mAirplane, mNoSims,mEthernet;//modify by hclydao
```
在
```java
         mWifiAirplaneSpacer =         findViewById(R.id.wifi_airplane_spacer);
         mWifiSignalSpacer =           findViewById(R.id.wifi_signal_spacer);
         mMobileSignalGroup = (LinearLayout) findViewById(R.id.mobile_signal_group);
```
后增加
```java
        mEthernetGroup  = (ViewGroup) findViewById(R.id.ethernet_combo);//add by hclydao
        mEthernet       = (ImageView) findViewById(R.id.ethernet_state);
```
在
```java
         mWifi           = null;
         mAirplane       = null;
```
后增加
```java
        mEthernetGroup  = null;//add by hclydao
        mEthernet	    = null;
```
增加函数
```java
	//add by hclydao
	@Override
    public void setEthernetIndicators(boolean visible, int stateIcon, int contentDescription) {
        mEthernetVisible = visible;
        mEthernetStateId = stateIcon;
        mEthernetDescription = contentDescription;

        apply();
    }
```
在apply函数中
```java
             mWifiSignalSpacer.setVisibility(View.GONE);
         }
```
后增加
```java
        if (mEthernetVisible && !mWifiVisible) {//add by hclydao
            mEthernetGroup.setVisibility(View.VISIBLE);
            mEthernet.setImageResource(mEthernetStateId);
            mEthernetGroup.setContentDescription(mContext.getString(mEthernetDescription));
        } else {
            mEthernetGroup.setVisibility(View.GONE);
        }
```
在
```java
boolean anythingVisible = mNoSimsVisible || mWifiVisible || mIsAirplaneMode
```
上增加
```java
|| mEthernetVisible
```


![打赏](https://github.com/hcly/pics/blob/master/zhifu.png)
