# APE JavaScriptFramework (JSF) vs. ApePubSub (APS)

This document was created to show and explain the differences between the Ajax Push Engine (APE) default [Javascript Framework (JSF)](https://github.com/APE-Project/APE_JSF) and Pablo Tejada [Ape Pub Sub (APS)](https://github.com/ptejada/ApePubSub) framework. Both framework are used to control the [APE Server](https://github.com/APE-Project/APE_Server). This document supposed you're already familliar with APE.

For the purpose of this comparation, I used the [Move Demo from the APE Project Website](http://ape-project.org/demos/move/) to compare both frame and serve an example of the same app running on both framework. The "move" demo and is ideal to do so since it is pretty simple. If you are already familiar with both framework, you can skip to the code examples included in this repository. You'll notice how both example use the same HTML, CSS and Javascript helper file. The only thing that changes is the APE related javascript code included in the HTML files.


To start things off, I must point out that both framework have the same goal in mind : Provide a way to communicate in real-time with the [APE Server](https://github.com/APE-Project/APE_Server) inside a webpage using Javascript. They both provide the same APIs more or less, they both support websocket and other transport method for legacy broswer and have both proven to be realible on large scale application. What differenciate them is the way they work at their core.


## APE JSF
APE JSF (or JavaScript Framework) have been around since the birth of the APE Server project. It is the default javascript client shipped with APE and the one offically supported by the APE Community. 

The JSF is built around Mootools and separated in two parts : The **client** and the **core**. The client, avaiable in plain Javascript or Mootools based is used to load the core inside an iFrame. The overall configuration is traditionannly done using a dedicated config file, containing the app domain, server url and JSF url parameters. *Sessions* support is enabled by including an additionnal file to the APE core.

The last update to the JSF is from **june 2011** as of 30 avril 2014.

## ApePubSub
ApePubSub (APS) is an alternative event driven Pub/Sub framework to the official APE_JSF for the APE Server written by [Pablo Tejada](https://github.com/ptejada). It is written in plain javascript and is great for beginners, good for those small/simpler projects. The client framework is based on @paraboul's next generation APE_JSF draft and the server framework is a compilation of custom commands, hooks and rewrites from the official server framework, the actual server binary does not require modification. [[Ref](https://github.com/ptejada/ApePubSub/wiki/Intro)]

APS is composed of a single javascript file and the configuration (Ape Server URL, sessions support, etc.) is done in the client contructor. APS doesn't require a specific url parameter for it's location since no core file is loaded in an iframe and/or separate request. 

The last update to ApePubSub is from **march 2014** as of 30 avril 2014.

## Pros & cons of each framework
While both framework acheive the same result, they both have some pros and cons.

#### JSF
* JSF is offically supported by the APE Community (+)
* JSF have been around for many years and is really stable (+)
* JSF is based on the less popular Mootools (-)
* JSF make use of an iframe to load a separate core file (-)
* JSF last update is more than 3 years old (-)
* JSF demos currently uses the less popular Mootools (-)
* JSF can't access a channel/pipe by it's name (-)
* JSF include a way to communicate with the server using PHP (inlinepush) (+)
* Etc.

#### APS
* APS doesn't require a [frequency](https://github.com/APE-Project/APE_Server/wiki/APE_JSF-Frequency) if sessions are not enabled (+)
* APS is activelly maintained (+)
* APS parameter are set in the constructor and not a seperate var of file (+)
* APS is writtent in vanilla Javascript (+)
* APS sessions support is parameter based and not file based. It can easily be toggled on and off at any time, even after initialization. (+)
* APS include a robust log and debug function (+)
* APS can access/publish to a channel using the channel's name (+)
* APS incldue a way to communicate with the server using PHP (eventPush) (+)
* APS include a function to update a user property. This propertie can be synced across all connected user (JSF would require a dedicated server side script). (+)
* APS handling of users and channel is globally simpler (+)
* APS ways of sending a command/raw/event from a user to a channel is basically simpler. You can specify if the user should receive the event or not (including it subusers if you're using sessions).
* APS is relatively new and not supported by everyone (-)
* Converting an existing app from JSF to APS requires some work (-)
* Etc.

#### Common 
* They both support websocket will fallback to longpooling (+)
* They both support sessions (+)
* They both require the use of [frequencies](https://github.com/APE-Project/APE_Server/wiki/APE_JSF-Frequency) to use sessions (-)


To summarize, the biggest flaws of APE JSF are the use of Mootools, the client vs. core mix-up and it's lack of recent update. While this framework is powerfull, it's implemetation is slightly outdated. 

## Move demo example
The move demo included in this repo is a great example on how to use both framework to acehive the same goal. I suggest comparing both file side to side using *FileMerge* or something similar to really apeciate the differences. But lets compare the major difference here.

The first thing to notice is that APS use a couple less lines to acheive the same result (116 for JSF vs. 98 for APS). You'll also notice how JSF include two scripts while APS only include one. I can't really count this a major diffrence here since the second file for JSF is merely the configs parameters, which could be include in the main file. Nevertheless, I left it like this since the two separate files are the default JSF setup.

The second thing you'll notice is how the server url and port is defined directly in the constructor on the APS side. This can or cannot be a major point for some user since this could also be defined in the html file using a variable on the JSF side.


Next the **click** event handler is more or less the same. On the JSF side, *movePipeId* (which represent the channel the user is connected on) is used to send the **SETPOS** command to the server while APS calls the **update** function of the user directly. This is truly the ***biggest diffence*** between the frameworks.

The thing you might have not notice yet is that by updating the user propertie directly, ***APS doesn't require a custom server-side command*** in that case. This is a big advantage over JSF. While a custom server side script is relatively easy to code, it's always easier to work without touching the server. 

In this particular case, if you look at the server side javascript (*ssjs*) required by the JSF method, you'll notice everything the **SETPOS** command does is to set the user properties and propagate the change to the other clients connected to the APE Server. Exactly what APS does with the update command. 

Another thing you may notice is how APS define not only the client name on init, but also the default position. APS is able to define any user properties upon the initial connection compared to JSF which can only define the name if the *nickname.js* server side file is indluded. While this more or less depend of the server side scripts shipped with both framwork, it is still an advantage to APS. 

The best example of how APS handle best the user properties is how the user color is defined. This demo use the same method as the original move demo of using a color matching the nickname of the user. While with APS we could send the user (true random) color at the same time as his name, with JSF one would need to create or edit a serverside script to make this happend. 


If we move from the user properties to the next lines of code, both framework work essentially the same. Both listen to new user (userJoin/join) or leaving user (UserLeft/left) on the "move" channel to create/delete user in the DOM. They both connect/join the "move" channel to do so. It must be noted here that in both cases, the user properties (i.e., the position coordinates) are global to all channels and not only "move". The "move" channel is only use to keep track of the people currently on the page.

Both framework also listen to the position change (positions raw OR userPositionUpdate event). The only difference here is while JSF listen to the raw sent on to the "move" **channel** (sent by the *ssjs*), APS listen to the **user property update** on the main object. APS basically listen for a position change on all channels since the user properties
changes are sent to all clients regardless of which channels they are on (The user property updates are channel independent). JSF listen to this event only on the "move" channel since no built-in command is available to do so. In most cases, receiving a perperty update on a global scale isn't a big deal and it is sometime helpfull, for example when a property is shared by different channels (e.g. username).

The main difference in the way both framework listen to events are in the **code syntaxt**. Again, one point can be awarded to APS for not messing with load/start/ready states. It should be noted also how you can listen to an even with APS even if the channel/user is not currently conencted. 

To award one point to JSF, it must be noted that the 'userJoin' event does fire for all currently connected users when you join a channel while APS only fire the "join" event when a new user truly join a channel, the current connected user list beeing available in the "client.sub" function callback. It some cases, this can be helpfull while in other it is not. In this demo, this requires us to use the "createUser" function twice. In other situation it can be helpful if you need to perform different action depending if the user is already on the channel or if the new user really just connected to the channel. An example whould be a noise notification when a user join a chat room. You don't want that noise to be hear when you join yourself the room and for all 25 users already in the channel (one noise instance fired for each of them, at once).


Finnaly, the **createUser** function at the bottom differ only by the way the current user *pubid* is fetched. APS abstact the difference between the client and the core.



## Conclusion
### Personal favorite
If you read all of this, you may have notice that my personnal preference goes to **ApePubSub (APS)**. I used both framework with my projects since I started using APE four years ago. I find **APS** easier to setup and to with since there's no ambigity between the client and the core. More over, the even driven aspect of APS is easier to understand and to code. APS documentation is helpfull and I like how the channel and user objects works. I found over time that in most cases, APS requires less server-side code to acheive the same result. The move example is a great example of this.

But what make APS my default APS client is the fact that it source code is well documented and written in plain Javascript. The source code is easier to understand this way and make it more eady to maintaint. Futhermore, some unique APIs (For example the *client.update* function are really usefull). Finnally, APS was created and maintained by an active member of the APE Community.

That being said, and with eveything I wrote here *(Fun fact: it started as a bullet list. I got caught writing more I guess!)*, I truly belive **APS should replace JSF as the defaut APE Server javascript framework****. The simple fact that APS is modern compared to JSF, doesn't use Mootools and is well documented makes it worth a try.

### One more thing..
Don't hesiate to comment to this on the Gist directly or on the APE Project [Google Group](https://groups.google.com/forum/#!forum/ape-project). Fell free to edit or fix stuff I might have have missed or got got wrong (English is not my first language, *Désolé!*)
