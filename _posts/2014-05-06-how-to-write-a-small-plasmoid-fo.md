---
layout: post
title: "How to write a small plasmoid in QML for KDE desktop"
modified: 2014-05-06 15:52:34 +0200
tags: [qml,javascript,kde,linux]
image:
  feature: 
  credit: 
  creditlink: 
comments: 
share: 
---
I installed KDE on my laptop recently and started playing around with plasmoids, small scripts for the plasma engine of KDE. Plasmoids are very versatile and very easy to program. KDE api gives you the possibility to choose among  several script languages. As I became involved professionally with QML in the last years, I decided  to use this markup language developed by the Qt  people to make my first script.

My plasmoid is called wmip, sort of *what’s my IP address*, and it’s suppose to display your current IP address along with the Internet Service Provider.

First of all, this plasmoid lives in just one file: main.qml. I’ll cover the code step by step and explain later how to install it.

If you are just interested in the plasmoid itself you can get it from [here.](http://kde-apps.org/content/show.php/wmip?content=165084&PHPSESSID=d8fa8a386fcc05b6effdcf91d271c431)

I just go over the basics of QML I needed for this example. There is many online [resources](http://qt-project.org/doc/qt-5/qmlfirststeps.html) if you want to dig into QML. 

Programming our first plasmoid with QML/javascript
------------------------------------------------------------------------------

As every QML file, main.qml starts importing the needed headers:

{% highlight qml %}
import QtQuick 1.0;
{% endhighlight %}

It seems KDE4 only support QtQuick version 1.0 for plasmoids. I tries with 2.0 and it did not work.

Next, I defined the root element of type *item* and set the geometry 250 x 100. I also defined two string variables to store the IP address and the IPS.

{% highlight qml %}
Item {
    id:root

    width: 250
    height: 100
    property string ip: ""
    property string isp: ""
{% endhighlight %}

And here is where the magic happens: The function * request()* accepts a url and a callback function as parameter. Basically, it makes a http request to the given url and each time the server response, the function given as second argument (*callback* in this case) jumps into action. What does *callback* do? well, It assumes the response coming from the server is json-formatted, and in this case is, parse the *query* and *isp* values and assign them to the string *ip* and *isp*.

{% highlight javascript %}
  function callback(x){
        if (x.responseText) {
            var d = JSON.parse(x.responseText);
            root.ip = d.query;
            root.isp = d.isp;
        }
    }

    function request(url, callback) {
       var xhr = new XMLHttpRequest();
        xhr.onreadystatechange = (function f() {callback(xhr)});
       xhr.open('GET', url, true);
       xhr.send();
   }
{% endhighlight %}

The json parsing is guarded by an *if*, the reason is that during the request, the function *callback* is called several times before the server delivered a response with text.

Note that *callback* get invoked asynchronously, that is, the function *request* just set the property *onreadystatechange* to call *callback* on each response from server, but it does not call the function itself. 

How’s all this tarted?  Well I defined a timer element, which request the ip and isp once per minute by calling *request()*. Parameters  are the url to the *ip-api.com* server, and the *callback* function.

{% highlight qml %}
 Timer {
        running: true
        triggeredOnStart: true
        interval: 60000
        onTriggered: request('http://ip-api.com/json',callback)
    }
{% endhighlight %}

The rest is just layout: 2 line of text to display the values inside a column element.

{% highlight qml %}
   Column{
        Text {  text: "IP Address: "+root.ip }
        Text {  text: "ISP: "+root.isp }
 }
{% endhighlight %}


The qml engine updates the texts each time *ip* and *isp* get new values. That’s the magic of declarative programming.
 
Installation
------------

There are several ways to install our plasmoid. I’ll cover all I used during development and deployment:

I have the Qt framework installed, so I could use the qml standard viewer *qmlviewer* to launch the script:

    <path/>qmlviewer main.qml

Which has the advantage you can use the standard output for debugging: Just add *console.log(‘value to check’)* anywhere inside a javascript block and you’ll get the logs.

Once the script behaves as expected, you can install it as plasmoid locally. For this we need to create a package.

First of all, we need a .desktop file. This is a kind of index for KDE to manage the plasmoid:

    [Desktop Entry]
    Name=wmip
    Comment=A widget in QML for getting the current IP address
    Icon=kde 
    X-Plasma-API=declarativeappletscript
    X-Plasma-MainScript=ui/main.qml
    X-Plasma-DefaultSize=280,100    
    X-KDE-PluginInfo-Author=Juan de Hoyos
    X-KDE-PluginInfo-Email=juanmahv@gmail.com
    X-KDE-PluginInfo-Website=
    X-KDE-PluginInfo-Category=Example
    X-KDE-PluginInfo-Name=wmip
    X-KDE-PluginInfo-Version=0.1    
    X-KDE-PluginInfo-Depends=
    X-KDE-PluginInfo-License=GPL
    X-KDE-PluginInfo-EnabledByDefault=true
    X-KDE-ServiceTypes=Plasma/Applet
    Type=Service 

The mos important keys to define here are *X-Plasma-API*, and *X-Plasma-MainScript*, which tells KDE what script to load and which type. For more information about .desktop files look up the KDE documentation online.

In my case, the package is a folder called wmip with th following structure:
 
    wmip/contents/ui/main.qml
    wmip/metadata.dektop

Now that we have a package, we can register the plasmoid locally with *plasmapkg*:

    plasmapgk --install wmip

And then got to your desktop, try to add a widget as usual and you should see the wmip  among all your local plasmoid, ready for installation. Grab and drop it to the desktop and you’ll see it in action.

Obviously, if you plan to develop seriously you need a build tool. In my oppinion, CMake is the best option.
All you need is a CMakeLists file like this:

    project(wmip)
    
    set(appName wmip)
    find_package(KDE4 REQUIRED) 
    include(KDE4Defaults) 

    install(DIRECTORY wmip/
        DESTINATION ${DATA_INSTALL_DIR}/plasma/plasmoids/${appName})
 
    install(FILES wmip/metadata.desktop
        DESTINATION ${SERVICES_INSTALL_DIR} RENAME plasma-applet-${appName}.desktop)

        
and now you can use cmake to generate the corresponding makefile and install the plasmoid:

    mkdir build
    cd build
    cmake <path to CMakeLists.txt>
    make all
    sudo make install
  
and voilá, the plasmoid is installed.

If you want to save tipping and get the all the files, go to [my repository](https://github.com/juanmahv/wmip) in GitHub. Feel free to download and play around.

[Reference](http://techbase.kde.org/Development/Tutorials/Plasma/QML/GettingStarted)