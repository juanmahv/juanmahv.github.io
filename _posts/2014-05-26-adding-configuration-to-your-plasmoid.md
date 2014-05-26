---
layout: post
title: "Adding configuration to your plasmoid"
modified: 2014-05-26 22:42:51 +0200
tags: [kde,plasmoid,linux,Qt,QML]
image:
  feature: 
  credit: 
  creditlink: 
comments: yes
share: yes
---


In a previous post I explained how to program a simple plasmoid in QML. Now it's time to move forward and let the user configure some parameters.

In a plasmoid, a git part of the infrastructure needed for configuration is already provided by KDE, so we have to provide just the specific to our plasmoid: Basically a configuration widget were the user can enter her preferences and a xml-formatted configuration file. Everything else , like read/write from/to disk, display configuration widow, etc is done by KDE.

So, let's start with an example: A plasmoid I wrote called wmip. For all of you interested, all the stuff is available [here.](http://kde-apps.org/content/show.php/wmip?content=165084&PHPSESSID=d8fa8a386fcc05b6effdcf91d271c431). This plasmoid displays your current IP address, ISP name and physical location.  In the very first version, I got as feedback that the fonts being black made it almost unreadable on dark themes, so it was clear that if I wmip shall be more than a nice loose of time, the font color has to be configurable. So I did, and added also configuration of the font size and the possibility of turning the background off. Finally I decided to give the user control of the refresh timer as well; that is, after how many minutes the script has to fetch the IP address and other data again. 

So basically, these are my configuration parameters:

* Font size
* Font color
* Background y/n
* Refresh rate
 
I recomend having a look the my post [How to write a small plasmoid in QML for KDE desktop] (http://juanmanueldehoyos.com/how-to-write-a-small-plasmoid-fo/) just in case you are not familiar with QML.

First of all we need a configuration file in XML format according to what KDE expect.

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<kcfg xmlns="http://www.kde.org/standards/kcfg/1.0"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.kde.org/standards/kcfg/1.0
      http://www.kde.org/standards/kcfg/1.0/kcfg.xsd" >
  <kcfgfile name=""/>
  <group name="General">
    <entry name="fontColor" type="String">
      <label>font color</label>
      <default>#000000</default>
    </entry>
    <entry name="fontSize" type="Int">
      <label>font size</label>
      <default>9</default>
    </entry>
    <entry name="rate" type="Int">
      <label>refresh rate</label>
      <default>60</default>
    </entry>
    <entry name="background" type="Bool">
      <label>background hint</label>
      <default>true</default>
    </entry>
  </group>
</kcfg> 
{% endhighlight %}

We see here each parameter inside a <entry> section. The types are basic ones from javascript. The default value is the value contained by those parameter at the beginning. No surprises here.

Now that we have our configuration file, we save it as *main.xml* inside a directory called *config*.I’ll cover the directory structure later in this post.

Now we need a widget for the configuration. For this I used the design view of QtCreator to create a file I named *config.ui* and put it under a directory called *ui*, together with the script *main.qml*.

The file looks like this:
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<ui version="4.0">
 <class>Form</class>
 <widget class="QWidget" name="Form">
  <property name="geometry">
   <rect>
    <x>0</x>
    <y>0</y>
    <width>335</width>
    <height>186</height>
   </rect>
  </property>
  <property name="windowTitle">
   <string>Form</string>
  </property>
  <layout class="QFormLayout" name="formLayout">
   <item row="0" column="0">
    <widget class="QLabel" name="label">
     <property name="text">
      <string>Text color:</string>
     </property>
    </widget>
   </item>
   <item row="0" column="1">
    <widget class="QLineEdit" name="kcfg_fontColor">
     <property name="text">
      <string>#000000</string>
     </property>
    </widget>
   </item>
   <item row="1" column="0">
    <widget class="QLabel" name="label_2">
     <property name="text">
      <string>Font size:</string>
     </property>
    </widget>
   </item>
   <item row="1" column="1">
    <layout class="QHBoxLayout" name="horizontalLayout">
     <item>
      <widget class="QSpinBox" name="kcfg_fontSize">
       <property name="minimum">
        <number>8</number>
       </property>
       <property name="maximum">
        <number>24</number>
       </property>
       <property name="value">
        <number>9</number>
       </property>
      </widget>
     </item>
     <item>
      <spacer name="horizontalSpacer">
       <property name="orientation">
        <enum>Qt::Horizontal</enum>
       </property>
       <property name="sizeHint" stdset="0">
        <size>
         <width>40</width>
         <height>20</height>
        </size>
       </property>
      </spacer>
     </item>
    </layout>
   </item>
   <item row="2" column="0">
    <widget class="QLabel" name="label_3">
     <property name="text">
      <string>Refresh rate (seconds):</string>
     </property>
    </widget>
   </item>
   <item row="2" column="1">
    <layout class="QHBoxLayout" name="horizontalLayout_3">
     <item>
      <widget class="QSpinBox" name="kcfg_rate">
       <property name="minimum">
        <number>1</number>
       </property>
       <property name="maximum">
        <number>99999999</number>
       </property>
       <property name="value">
        <number>60</number>
       </property>
      </widget>
     </item>
     <item>
      <spacer name="horizontalSpacer_3">
       <property name="orientation">
        <enum>Qt::Horizontal</enum>
       </property>
       <property name="sizeHint" stdset="0">
        <size>
         <width>40</width>
         <height>20</height>
        </size>
       </property>
      </spacer>
     </item>
    </layout>
   </item>
   <item row="3" column="0" colspan="2">
    <layout class="QHBoxLayout" name="horizontalLayout_2">
     <item>
      <widget class="QCheckBox" name="kcfg_background">
       <property name="layoutDirection">
        <enum>Qt::RightToLeft</enum>
       </property>
       <property name="text">
        <string>Background</string>
       </property>
       <property name="checked">
        <bool>true</bool>
       </property>
      </widget>
     </item>
     <item>
      <spacer name="horizontalSpacer_2">
       <property name="orientation">
        <enum>Qt::Horizontal</enum>
       </property>
       <property name="sizeHint" stdset="0">
        <size>
         <width>40</width>
         <height>20</height>
        </size>
       </property>
      </spacer>
     </item>
    </layout>
   </item>
  </layout>
 </widget>
 <resources/>
 <connections/>
</ui>
{% endhighlight %}

You are free to copy&paste it and open it with QtCreator. But the interesting point here is the link between the entries we configured previously in *main.xml* and the controls defined in config.ui that are suppose to read/write them. The key in in the name: In config.ui, the controls are named kcfg_<parameter>. So the widget that controls the paramter *fontSize* is called kcfg_fontSize. We respect this naming convention and KDE puts the magic.

OK, we have a configuration file and the UI file to handle it. Now what?

Now we can make use of the configured values in our scrip main.qml.

We need a global object *plasmoid* whose properties become very handy to access the KDE infrastructure, in our case, the configuration system. In order to get the object, the following include is needed:


{% highlight javaScript %}
import org.kde.plasma.core 0.1 as PlasmaCore
{% endhighlight %}

Just as a matter of order, I declared some variables to store the values:

{% highlight javaScript %}
property string fontColor:"black"
property int fontSize:9
property int rate:60
property bool backgroundHints:true
{% endhighlight %}
    
An I declared a function to read all the parameter:

{% highlight javascript %}
function configChanged()
{
        root.fontColor = plasmoid.readConfig("fontColor");
        root.fontSize = plasmoid.readConfig("fontSize");
        root.backgroundHints = plasmoid.readConfig("background");
        root.rate = plasmoid.readConfig("rate")
        plasmoid.backgroundHints = root.backgroundHints?1:0;
}
{% endhighlight %}

By invoking plasmoid.readConfig() we get the corresponding value. But when do we invoke *configChanged()*? Yes, you guessed right: When the configuration changes. For that we need to attach this function to the signal *configChanged* emitted by KDE each time the user updates the configuration.

{% highlight javaScript %}
Component.onCompleted: {
        plasmoid.addEventListener('ConfigChanged', configChanged);	
    }
{% endhighlight %}

And that’s all we need. Just to give the whole picture, that’s how main.qml looks like:

{% highlight javaScript %}
import QtQuick 1.0
import org.kde.plasma.core 0.1 as PlasmaCore

Item {
    id:root
           
    property string ip: ""
    property string isp: ""
    property string country: ""
    property string region: ""
    property string city: ""
    property string lat: ""
    property string lon: ""
    
    property string fontColor:"black"
    property int fontSize:9
    property int rate:60
    property bool backgroundHints:true
    property string lastCheck:""
    
  Component.onCompleted: {
        plasmoid.addEventListener('ConfigChanged', configChanged);	
    }
    
    function configChanged()
    {
        root.fontColor = plasmoid.readConfig("fontColor");
        root.fontSize = plasmoid.readConfig("fontSize");
        root.backgroundHints = plasmoid.readConfig("background");
        root.rate = plasmoid.readConfig("rate")
        plasmoid.backgroundHints = root.backgroundHints?1:0;
    }
        
    function callback(x){
        if (x.responseText) {
            root.lastCheck = getFormattedDate()
            var d = JSON.parse(x.responseText);
            root.ip = d.query;
            root.isp = d.isp;
            root.country = d.country
            root.region = d.regionName
            root.city = d.city
            root.lat = d.lat
            root.lon = d.lon
        }
    }

    function request(url, callback) {
        var xhr = new XMLHttpRequest();
        xhr.onreadystatechange = (function f() {callback(xhr)});
       xhr.open('GET', url, true);
       xhr.send();
   }

   function getFormattedDate() {
        var date = new Date();
        var str = date.getFullYear() + "-" + (date.getMonth() + 1) + "-" + date.getDate() + " "
                        +  date.getHours() + ":" + date.getMinutes() + ":" + date.getSeconds();
        return str;
    }

    Timer {
        repeat: true
        running: true
        triggeredOnStart: true
        interval: root.rate * 1000
        onTriggered: request('http://ip-api.com/json',callback)	
    }
    
      Column{
          Text {  color: root.fontColor; font.pointSize: root.fontSize; text: " IP: "+root.ip }
          Text {  color: root.fontColor; font.pointSize: root.fontSize; text: " ISP: "+root.isp }
          Text {  color: root.fontColor; font.pointSize: root.fontSize; text: " City: "+root.city }
          Text {  color: root.fontColor; font.pointSize: root.fontSize; text: " Region: "+root.region }
          Text {  color: root.fontColor; font.pointSize: root.fontSize; text: " Country: "+root.country }

          Row {
              spacing: 5
              Text {  color: root.fontColor;font.pointSize: root.fontSize;text: " Lat: "+root.lat+", Lon:"+root.lon }
              Text {  color: root.fontColor;font.pointSize: root.fontSize;
                  text: "<u>map</u> "
                  MouseArea {
                  anchors.fill: parent
                  onClicked: { Qt.openUrlExternally('http://www.openstreetmap.org/?mlat='+root.lat+'&mlon='+root.lon+'') }
                  }
              }
          }
          Text {  color: root.fontColor; font.pointSize: root.fontSize-2; text: {"(Last check: "+ root.lastCheck+")" }}
    }
}
   
{% endhighlight %}

I promised to give a hint about the directory layout. In my case it looks like:

    wmip/contents/config/main.xml
    wmip/contents/ui/main.qml
    wmip/contents/ui/config.ui
    wmip/metadata.desktop

Feel free to download the whole plasmoid (link at the beginning of this post) and play around.

To install/upgrade just type
*plasmapkg -u <pasmoid file>*

Enjoy.
